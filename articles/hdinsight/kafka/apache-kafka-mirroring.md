---
title: Zrcadlení Apache Kafka témata – Azure HDInsight
description: Naučte se používat funkci zrcadlení Apache Kafka k údržbě repliky Kafka v clusteru HDInsight tím, že rozzrcadlí témata do sekundárního clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 0f444838c87e14fa88f2785030c29915df637cf8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552198"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Použití nástroje MirrorMaker k replikaci Apache Kafkach témat pomocí Kafka ve službě HDInsight

Naučte se používat funkci zrcadlení Apache Kafka k replikaci témat do sekundárního clusteru. Zrcadlení může být spuštěno jako nepřetržitý proces nebo občasně používáno jako metoda migrace dat z jednoho clusteru do jiného.

V tomto příkladu se zrcadlení používá k replikaci témat mezi dvěma clustery HDInsight. Oba clustery jsou v různých datových centrech v různých virtuálních sítích.

> [!WARNING]  
> Pro zajištění odolnosti proti chybám by se nemělo brát v úvahu zrcadlení. Posun k položkám v rámci tématu se liší mezi primárním a sekundárním clusterem, takže klienti nemohou použít obě tato dvě zaměnitelné.
>
> Pokud máte obavy o odolnost proti chybám, měli byste nastavit replikaci pro témata v rámci vašeho clusteru. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak funguje zrcadlení Apache Kafka

Zrcadlení funguje pomocí nástroje [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (součást Apache Kafka) pro využívání záznamů z témat v primárním clusteru a pak vytvořit místní kopii v sekundárním clusteru. Nástroje MirrorMaker používá jednoho nebo více *uživatelů* , kteří se čtou z primárního clusteru, a *producent* , který zapisuje do místního (sekundárního) clusteru.

Nejužitečnější nastavení zrcadlení pro zotavení po havárii využívá clustery Kafka v různých oblastech Azure. Chcete-li toho dosáhnout, virtuální sítě, ve kterých jsou umístěny clustery, jsou propojeny partnerským vztahem.

Následující diagram znázorňuje proces zrcadlení a způsob komunikace mezi clustery:

![Diagram procesu zrcadlení](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Primární a sekundární clustery se mohou lišit v počtu uzlů a oddílů a posuny v rámci těchto témat jsou také jiné. Zrcadlení uchovává klíčovou hodnotu, která se používá pro dělení na oddíly, takže pořadí záznamů je zachováno po jednotlivých klíčích.

### <a name="mirroring-across-network-boundaries"></a>Zrcadlení napříč hranicemi sítě

Pokud potřebujete zrcadlit mezi clustery Kafka v různých sítích, existují tyto další předpoklady:

* **Brány**: sítě musí být schopné komunikovat na úrovni protokolu TCP/IP.

* **Adresování serverů**: můžete si vybrat, že se uzly clusteru budou řešit pomocí jejich IP adres nebo plně kvalifikovaných názvů domén.

    * **IP adresy**: Pokud nakonfigurujete clustery Kafka pro použití inzerce IP adres, můžete pokračovat v instalaci zrcadlení pomocí IP adres uzlů služby Broker a uzlů Zookeeper.
    
    * **Názvy domén**: Pokud nekonfigurujete clustery Kafka pro inzerování IP adres, clustery musí být schopné se vzájemně připojit pomocí plně kvalifikovaných názvů domény (FQDN). To vyžaduje server DNS (Domain Name System) v každé síti, který je nakonfigurován pro přeposílání požadavků do jiných sítí. Při vytváření Virtual Network Azure místo použití automatické služby DNS, která se poskytuje v síti, musíte zadat vlastní server DNS a IP adresu pro tento server. Po vytvoření Virtual Network musíte vytvořit virtuální počítač Azure, který tuto IP adresu používá, a pak na něj nainstalovat a nakonfigurovat software DNS.

    > [!WARNING]  
    > Před instalací HDInsight do Virtual Network vytvořte a nakonfigurujte vlastní server DNS. Pro HDInsight není nutná žádná další konfigurace pro použití serveru DNS nakonfigurovaného pro Virtual Network.

Další informace o propojení dvou virtuálních sítí Azure najdete v tématu [Konfigurace připojení typu VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architektura zrcadlení

Tato architektura nabízí dva clustery v různých skupinách prostředků a virtuálních sítích: **primární** a **sekundární**.

### <a name="creation-steps"></a>Postup vytvoření

1. Vytvořte dvě nové skupiny prostředků:

    |Skupina prostředků | Umístění |
    |---|---|
    | kafka-primary-rg | Střední USA |
    | kafka-secondary-rg | Středoseverní USA |

1. Vytvořte novou virtuální síť **Kafka-Primary-VNet** v **Kafka-Primary-RG**. Ponechte výchozí nastavení.
1. Vytvořte novou virtuální síť **Kafka-Secondary-VNet** v **Kafka-Secondary-RG**, ale také s výchozím nastavením.

1. Vytvořte dva nové clustery Kafka:

    | Název clusteru | Skupina prostředků | Virtual Network | Účet úložiště |
    |---|---|---|---|
    | Kafka-Primary-cluster | kafka-primary-rg | Kafka-Primary-VNet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Vytvořte partnerské vztahy virtuálních sítí. V tomto kroku vytvoříte dvě partnerské vztahy: jednu z **Kafka-Primary-** VNet na **Kafka-Secondary-VNet** a jednu zpět od **Kafka-Secondary-VNet** až **Kafka-Primary-VNet**.
    1. Vyberte virtuální síť **Kafka-Primary-VNet** .
    1. V části **Nastavení**vyberte **partnerské vztahy** .
    1. Vyberte **Přidat**.
    1. Na obrazovce **Přidat partnerský vztah** zadejte podrobnosti, jak je znázorněno na snímku obrazovky níže.

        ![HDInsight Kafka Přidání partnerského vztahu virtuální sítě](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurace reklamy protokolu IP

Nakonfigurujte reklamu protokolu IP, aby se klient mohl připojit pomocí IP adres zprostředkovatele místo názvů domén.

1. Pro primární cluster přejít na řídicí panel Ambari: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Vyberte **služby** > **Kafka**. CliSelectck kartu **Konfigurace** .
1. Do dolní části **šablony Kafka-ENV** přidejte následující konfigurační řádky. Vyberte **Uložit**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Na obrazovce **Uložit konfiguraci** zadejte poznámku a klikněte na **Uložit**.
1. Pokud se zobrazí výzva s upozorněním konfigurace, klikněte **přesto na pokračovat**.
1. V části **Uložit změny konfigurace**vyberte **OK** .
1. Vyberte **restartovat** > v oznámení **vyžadovaném restartováním** **restartujte všechny ovlivněné** . Vyberte **Potvrdit restartování vše**.

    ![Ambari restartování Apache All ovlivnilo](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Nakonfigurujte Kafka, aby naslouchal na všech síťových rozhraních.
    
1. V části **služby** > **Kafka**zůstat na kartě **Konfigurace** . V části **Kafka Broker** nastavte vlastnost **listeners** na hodnotu `PLAINTEXT://0.0.0.0:9092`.
1. Vyberte **Uložit**.
1. Vyberte **restartovat**a **potvrďte restart vše**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Zaznamenání IP adres zprostředkovatele a adres Zookeeper pro primární cluster.

1. Na řídicím panelu Ambari vyberte **hostitelé** .
1. Poznamenejte si IP adresy pro zprostředkovatele a uzly Zookeeper. Uzly zprostředkovatele **mají jako první** dvě písmena názvu hostitele a uzly Zookeeper mají **ZK** jako první dvě písmena názvu hostitele.

    ![IP adresy uzlů pro zobrazení Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Opakujte předchozí tři kroky pro druhý cluster **Kafka-Secondary-cluster**: Nakonfigurujte reklamu protokolu IP, nastavte naslouchací procesy a poznamenejte si IP adresy zprostředkovatele a Zookeeper.

## <a name="create-topics"></a>Vytváření témat

1. Připojte se k **primárnímu** clusteru pomocí SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** uživatelským jménem SSH použitým při vytváření clusteru. Nahraďte **PRIMARYCLUSTER** základním názvem použitým při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Pomocí následujícího příkazu vytvořte proměnnou s hostiteli Apache Zookeeper pro primární cluster. Řetězce jako `ZOOKEEPER_IP_ADDRESS1` musí být nahrazeny skutečnými IP adresami zaznamenanými dříve, například `10.23.0.11` a `10.23.0.7`. Pokud používáte překlad plně kvalifikovaného názvu domény s vlastním serverem DNS, postupujte podle [těchto kroků](apache-kafka-get-started.md#getkafkainfo) a získejte názvy pro Broker a Zookeeper:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Chcete-li vytvořit téma s názvem `testtopic`, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. K ověření, zda bylo téma vytvořeno, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpověď obsahuje `testtopic`.

1. K zobrazení informací o hostiteli Zookeeper pro tento ( **primární**) cluster použijte následující postup:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Vrátí informace podobné následujícímu textu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Uložte tyto informace. Používá se v další části.

## <a name="configure-mirroring"></a>Konfigurace zrcadlení

1. Připojte se k **sekundárnímu** clusteru pomocí jiné relace SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** uživatelským jménem SSH použitým při vytváření clusteru. Nahraďte **SECONDARYCLUSTER** názvem použitým při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. `consumer.properties` soubor se používá ke konfiguraci komunikace s **primárním** clusterem. Chcete-li vytvořit soubor, použijte následující příkaz:

    ```bash
    nano consumer.properties
    ```

    Jako obsah souboru `consumer.properties` použijte následující text:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Nahraďte **PRIMARY_ZKHOSTS** IP adresami Zookeeper z **primárního** clusteru.

    Tento soubor popisuje informace o uživateli, které se mají použít při čtení z primárního clusteru Kafka. Další informace o konfiguraci příjemce najdete v tématu Konfigurace [příjemců](https://kafka.apache.org/documentation#consumerconfigs) na adrese Kafka.Apache.org.

    Pokud chcete soubor uložit, použijte **CTRL + X**, **Y**a pak **Zadejte**.

1. Před konfigurací producenta, který komunikuje se sekundárním clusterem, nastavte proměnnou pro IP adresy zprostředkovatele **sekundárního** clusteru. K vytvoření této proměnné použijte následující příkazy:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Příkaz `echo $SECONDARY_BROKERHOSTS` by měl vracet podobné informace následujícímu textu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Soubor `producer.properties` slouží ke komunikaci **sekundárního** clusteru. Chcete-li vytvořit soubor, použijte následující příkaz:

    ```bash
    nano producer.properties
    ```

    Jako obsah souboru `producer.properties` použijte následující text:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Nahraďte **SECONDARY_BROKERHOSTS** IP adresami zprostředkovatele, které jste použili v předchozím kroku.

    Další informace o konfiguraci výrobců najdete v tématu Konfigurace [výrobců](https://kafka.apache.org/documentation#producerconfigs) na adrese Kafka.Apache.org.

1. Pomocí následujících příkazů Vytvořte proměnnou prostředí s IP adresami hostitelů Zookeeper pro sekundární cluster:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Výchozí konfigurace pro Kafka ve službě HDInsight neumožňuje automatické vytváření témat. Před spuštěním procesu zrcadlení je nutné použít jednu z následujících možností:

    * **Vytvoření témat v sekundárním clusteru**: Tato možnost umožňuje také nastavit počet oddílů a faktor replikace.

        Témata můžete vytvořit předem pomocí následujícího příkazu:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Nahraďte `testtopic` názvem tématu, které se má vytvořit.

    * **Konfigurace clusteru pro automatické vytváření témat**: Tato možnost umožňuje nástroje MirrorMaker automaticky vytvářet témata, ale může je vytvořit s jiným počtem oddílů nebo faktorem replikace, než je primární téma.

        Chcete-li nakonfigurovat sekundární cluster tak, aby automaticky vytvářel témata, proveďte tyto kroky:

        1. Pro sekundární cluster přejít na řídicí panel Ambari: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Klikněte na **služby** > **Kafka**. Klikněte na kartu **Konfigurace** .
        1. Do pole __Filtr__ zadejte hodnotu `auto.create`. Tím se vyfiltruje seznam vlastností a zobrazí se nastavení `auto.create.topics.enable`.
        1. Změňte hodnotu `auto.create.topics.enable` na true a pak vyberte __Save (Uložit__). Přidejte poznámku a pak znovu vyberte __Uložit__ .
        1. Vyberte službu __Kafka__ , vyberte __restartovat__a pak vyberte __restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restartování vše__.

        ![Kafka povolit automatické vytváření témat](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Spustit nástroje MirrorMaker

1. Z připojení SSH k **sekundárnímu** clusteru spusťte pomocí následujícího příkazu proces nástroje MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry použité v tomto příkladu jsou:

    |Parametr |Popis |
    |---|---|
    |--Consumer. config|Určuje soubor, který obsahuje vlastnosti příjemce. Tyto vlastnosti slouží k vytvoření příjemce, který čte z *primárního* clusteru Kafka.|
    |--producent. config|Určuje soubor, který obsahuje vlastnosti výrobce. Tyto vlastnosti slouží k vytvoření producenta, který zapisuje do *sekundárního* clusteru Kafka.|
    |--seznam povolených|Seznam témat, která nástroje MirrorMaker replikují z primárního clusteru do sekundárního.|
    |--NUM. Streams|Počet uživatelských vláken, která se mají vytvořit|

    Příjemce v sekundárním uzlu nyní čeká na příjem zpráv.

2. Z připojení SSH k **primárnímu** clusteru spusťte pomocí následujícího příkazu producenta a odešlete zprávy do tématu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Po zadání prázdného řádku se kurzorem zadejte několik textových zpráv. Zprávy jsou odesílány do tématu v **primárním** clusteru. Po dokončení použijte **kombinaci kláves CTRL + C** a ukončete proces výrobce.

3. Z připojení SSH k **sekundárnímu** clusteru ukončete proces nástroje MirrorMaker **stisknutím kombinace kláves CTRL + C** . Ukončení procesu může trvat několik sekund. Chcete-li ověřit, zda byly zprávy replikovány do sekundárního, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Seznam témat teď obsahuje `testtopic`, která se vytvoří, když MirrorMaster zrcadlí téma z primárního clusteru do sekundárního. Zprávy načtené z tématu jsou stejné jako ty, které jste zadali v primárním clusteru.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroky v tomto dokumentu vytvořily clustery v různých skupinách prostředků Azure. Pokud chcete odstranit všechny vytvořené prostředky, můžete odstranit dvě vytvořené skupiny prostředků: **Kafka-Primary-RG** a **Kafka-secondary_rg**. Odstraněním skupin prostředků se odstraní všechny prostředky vytvořené pomocí následujícího dokumentu, včetně clusterů, virtuálních sítí a účtů úložiště.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak pomocí [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) vytvořit repliku clusteru [Apache Kafka](https://kafka.apache.org/) . Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafka:

* [Apache Kafka dokumentaci k nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na adrese cwiki.Apache.org.
* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)
* [Použití Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Storm s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k Apache Kafka prostřednictvím Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
