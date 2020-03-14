---
title: Řešení Storm pomocí Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se použití Apache Storm pomocí Azure HDInsight.
keywords: HDInsight, Storm, nejčastější dotazy k Azure, průvodce, běžné problémy s řešením problémů
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271925"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Řešení potíží s Apache Storm pomocí Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešeních pro práci s [Apache Stormmi](https://storm.apache.org/) datovými částmi v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak získám přístup do uživatelského rozhraní Storm v clusteru?

Máte dvě možnosti pro přístup k rozhraní Storm z prohlížeče:

### <a name="apache-ambari-ui"></a>Uživatelské rozhraní Apache Ambari

1. Přejdete na řídicí panel Ambari.
2. V seznamu **služeb vyberte možnost**zaplavit.
3. V nabídce **Rychlé odkazy** vyberte **uživatelské rozhraní**pro zaplavení.

### <a name="direct-link"></a>Přímý odkaz

Je možné otevřít uživatelské rozhraní Storm na následující adrese URL:

`https://<cluster DNS name>/stormui`

Příklad: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak přenesu o kontrolním bodu spoutu centra událostí Stormu z jedné topologie do jiného?

Při vývoji topologií, které čtou z Azure Event Hubs pomocí centra událostí Stormu v HDInsight spout soubor .jar, je nutné nasadit topologii, která má stejný název v novém clusteru. Je však nutné zachovat data kontrolního bodu, která byla potvrzena k [Apache Zookeeper](https://zookeeper.apache.org/) v původním clusteru.

### <a name="where-checkpoint-data-is-stored"></a>Uložení dat kontrolního bodu

Data kontrolního bodu pro posunutí neukládají spoutu centra událostí v ZooKeeper v dvě kořenové cesty:

- Kontrolní body netransakčního Spout jsou uloženy v `/eventhubspout`.

- Data kontrolního bodu transakčního Spout se ukládají v `/transactional`.

### <a name="how-to-restore"></a>Postup při obnovení

Pokud chcete získat skripty a knihovny, které slouží k exportu dat z ZooKeeper, a pak data importovat zpět do ZooKeeper s novým názvem, přečtěte si [Příklady HDInsight pro HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib – složka obsahuje soubory .jar, které obsahují implementaci pro operace exportu/importu. Bash složka obsahuje ukázkový skript, který ukazuje, jak exportovat data ze serveru ZooKeeper v původním clusteru a potom naimportovat zpět na server ZooKeeper v novém clusteru.

Spusťte skript [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z uzlů Zookeeper pro export a import dat. Aktualizujte skript na správnou verzi Hortonworks Data Platform (HDP). (Kterých pracujeme na tom, aby tyto skripty Obecné v HDInsight. Obecný skripty můžete spustit z libovolného uzlu v clusteru bez úprav uživatele.)

Příkaz pro export zapíše metadata do cesty Apache Hadoop systém souborů DFS (Distributed File System) (HDFS) (v Azure Blob Storage nebo Azure Data Lake Storage) do umístění, které jste nastavili.

### <a name="examples"></a>Příklady

#### <a name="export-offset-metadata"></a>Export posunu metadat

1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být exportovány pomocí SSH.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) exportujte data posunutí ZooKeeper do cesty k `/stormmetadta/zkdata` HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Import metadat posunutí

1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být importovány pomocí SSH.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) importujte data posunutí ZooKeeper z cesty HDFS `/stormmetadata/zkdata` na server ZooKeeper v cílovém clusteru:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Odstranit posunu metadata tak, aby topologie začít od začátku nebo od časové razítko, které uživatel vybere možnost zpracování dat

1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být odstraněny pomocí SSH.
2. Spusťte následující příkaz k odstranění všech dat posunu ZooKeeper v aktuálním clusteru (po aktualizaci verze řetězce HDP):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak najít binární soubory Stormu v clusteru?

Binární soubory pro aktuální HDP zásobník jsou v `/usr/hdp/current/storm-client`. Umístění je stejný pro hlavní uzly i pro pracovní uzly.

V/usr/HDP může být několik binárních souborů pro konkrétní verze HDP (například `/usr/hdp/2.5.0.1233/storm`). Složka `/usr/hdp/current/storm-client` je symlinked na nejnovější verzi, která je spuštěna v clusteru.

Další informace najdete v tématu [připojení ke clusteru HDInsight pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak zjistím topologie nasazení clusteru Storm?

Nejprve Identifikujte všechny součásti, které se instalují s HDInsight Storm. Storm cluster se skládá z čtyři kategorie uzlu:

* Uzly brány
* Hlavní uzly
* Uzly zooKeeper
* Pracovní uzly

### <a name="gateway-nodes"></a>Uzly brány

Uzel brány je brány a reverzní proxy server služby, která umožní veřejný přístup ke službě active management Ambari. Také obstará Ambari volba vedoucího procesu.

### <a name="head-nodes"></a>Hlavní uzly

Hlavní uzly Storm spusťte tyto služby:
* Nimbus
* Ambari server
* Ambari metrik serveru
* Metriky Ambari kolekcí
 
### <a name="zookeeper-nodes"></a>Uzly zooKeeper

HDInsight se dodává s třemi uzly ZooKeeper kvora. Velikost kvora je pevná a nedá se změnit.

Automaticky použít kvorum ZooKeeper jsou nakonfigurované služby Storm v clusteru.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly Storm spusťte tyto služby:
* Dohledový uzel
* Pracovního procesu Java virtual machines (JVMs) pro topologií
* Ambari agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Kde najdu Storm binární soubory spoutů centra událostí pro vývoj?

Další informace o používání soubory .jar spoutu centra událostí Stormu s vaší topologii najdete v následující prostředky.

### <a name="java-based-topology"></a>Topologie založené na jazyce Java

[Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# – na základě topologie (Mono na clustery Linux Storm HDInsight 3.4 +)

[Zpracování událostí z Azure Event Hubs s využitím Apache Storm veC#službě HDInsight ()](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nejnovější Apache Storm event hub spout binárních souborů pro clustery HDInsight 3.5 + Linux Storm

Další informace o tom, jak používat nejnovější Spout centra událostí, která funguje s clustery HDInsight 3.5 + Linux, najdete v [souboru Readme pro MVN-úložiště](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Příklady zdrojového kódu

Podívejte se na [Příklady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) čtení a zápisu z centra událostí azure pomocí Apache Storm topologie (napsané v jazyce Java) v clusteru Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Kde najdu soubory konfigurace Storm Log4J 2 v clusterech?

Identifikujte konfigurační soubory [Apache log4j 2](https://logging.apache.org/log4j/2.x/) pro služby pro zaplavení.

### <a name="on-head-nodes"></a>Na hlavní uzly.

Konfigurace Nimbus Log4J je čtena z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Na pracovní uzly

Konfigurace Log4J správce je čtena z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

Konfigurační soubor Work Log4J je načtený z `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Příklady: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Není výjimkou vedoucího procesu.

Při odesílání topologie může uživatel obdržet chybovou zprávu podobnou této: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Aby uživatel mohl řešení vyřešit, může být potřeba, aby si museli pořídit lístek, aby se uzly restartovaly nebo restartovaly. Další informace najdete v tématu [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
