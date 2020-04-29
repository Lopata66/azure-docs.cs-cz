---
title: Kurz – Apache Kafka & Enterprise Security – Azure HDInsight
description: Kurz – Naučte se konfigurovat zásady Apache Ranger pro Kafka v Azure HDInsight pomocí Balíček zabezpečení podniku.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 58c5b3bdd6d50f2e512cccffe78bd4e70805d729
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78204731"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Kurz: Konfigurace zásad Apache Kafka v HDInsight pomocí Balíček zabezpečení podniku (Preview)

Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro clustery Balíček zabezpečení podniku (ESP) Apache Kafka. ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger pro omezení přístupu k tématům `sales` a `marketingspend`.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytvoření témat v clusteru Kafka
> * Testování zásad Ranger

## <a name="prerequisite"></a>Požadavek

[Cluster HDInsight Kafka se balíček zabezpečení podniku](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://ClusterName.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `ClusterName` na název vašeho clusteru Kafka. Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

   ![Uživatelské rozhraní správce HDInsight Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

V tématu [vytvoření clusteru HDInsight s balíčkem zabezpečení podniku](./apache-domain-joined-configure-using-azure-adds.md) se naučíte vytvořit uživatele domény **sales_user** a **marketing_user**. V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

## <a name="create-ranger-policy"></a>Vytvoření zásady Ranger

Vytvoření zásady Ranger pro uživatele **sales_user** a **marketing_user**.

1. Otevřete **Uživatelské rozhraní správce Ranger**.

2. V části **Kafka**vyberte ** \<název_clusteru>_kafka** . Může být uvedena jedna předem nakonfigurovaná zásada.

3. Vyberte **Přidat novou zásadu** a zadejte následující hodnoty:

   |Nastavení  |Navrhovaná hodnota  |
   |---------|---------|
   |Název zásady  |  hdi sales* policy   |
   |Téma   |  sales* |
   |Vybrat uživatele  |  sales_user1 |
   |Oprávnění  | publish, consume, create (publikovat, využívat, vytvářet) |

   Následující zástupné znaky mohou být součástí názvu tématu:

   * * zastupuje nula nebo více výskytů libovolných znaků.
   * ? zastupuje jeden libovolný znak.

   ![Uživatelské rozhraní správce Apache Ranger vytvořit Policy1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Pokud uživatel domény v části **Select User** (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Vyberte **Přidat** a uložte zásadu.

5. Vyberte **Přidat novou zásadu** a zadejte následující hodnoty:

   |Nastavení  |Navrhovaná hodnota  |
   |---------|---------|
   |Název zásady  |  hdi marketing policy   |
   |Téma   |  marketingspend |
   |Vybrat uživatele  |  marketing_user1 |
   |Oprávnění  | publish, consume, create (publikovat, využívat, vytvářet) |

   ![Uživatelské rozhraní správce Apache Ranger vytvořit Policy2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Vyberte **Přidat** a uložte zásadu.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Vytvoření témat v clusteru Kafka pomocí ESP

Chcete-li vytvořit dvě `salesevents` témata `marketingspend`a:

1. Navažte připojení SSH ke clusteru použitím následujícího příkazu:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Nahraďte `DOMAINADMIN` uživatelem s oprávněními správce pro váš cluster nakonfigurovaný během [vytváření clusteru](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)a nahraďte `CLUSTERNAME` názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte heslo pro uživatelský účet správce. Další informace o použití `SSH` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujících příkazů uložte název clusteru do proměnné a nainstalujte nástroj pro parsování JSON `jq`. Po zobrazení výzvy zadejte název clusteru Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. K získání hostitelů zprostředkovatele Kafka použijte následující příkazy. Po zobrazení výzvy zadejte heslo účtu správce clusteru.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Než budete pokračovat, možná budete muset nastavit vývojové prostředí, pokud jste to ještě neudělali. Budete potřebovat komponenty, jako je Java JDK, Apache Maven, a klient SSH se spojovacím bod služby. Další informace najdete v tématu [pokyny k instalaci](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Stáhněte si [příklady pro producenta a konzumenta Apache Kafka připojené k doméně](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Postupujte podle kroků 2 a 3 v části **Sestavení a nasazení příkladu** v [kurzu použití rozhraní Apache Kafka Producer and Consumer API](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example).

1. Spusťte následující příkazy:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

V závislosti na nakonfigurovaných zásadách Ranger může **sales_user** vytvářet a spotřebovávat téma `salesevents` , ale ne `marketingspend`téma. Naopak **marketing_user** může vytvářet a využívat téma `marketingspend` , ale ne téma. `salesevents`

1. Otevřete nové připojení SSH ke clusteru. Pomocí následujícího příkazu se přihlaste jako uživatel **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Spusťte následující příkaz:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Pomocí názvů zprostředkovatelů z předchozí části nastavte následující proměnnou prostředí:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Příklad: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Postupujte podle kroků 3 v části **sestavení a nasazení příkladu** v [kurzu: pomocí rozhraní API pro Apache Kafka výrobce a příjemce](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) ověřte, `kafka-producer-consumer.jar` zda je k dispozici také **sales_user**.

**Poznámka: pro tento kurz použijte Kafka-Producer-Consumer. jar v části "DomainJoined-producent-Consumer" projekt (nikoli na jednom projektu producent-příjemce).**

5. Spuštěním následujícího **sales_user1** příkazu ověřte, že sales_user1 `salesevents` může vydávat k tématu:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Spusťte následující příkaz, který se má využít `salesevents`v tématu:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Ověřte, zda je možné číst zprávy.

7. Ověřte, že **sales_user1** nejde vyvolat do tématu `marketingspend` , a to spuštěním následujícího příkazu ve stejném okně SSH:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Dojde k chybě autorizace, kterou můžete ignorovat.

8. Všimněte si, že **marketing_user1** nemůže spotřebovávat `salesevents`z tématu.

   Opakujte kroky 1-4 výše, ale tentokrát jako **marketing_user1**.

   Spusťte následující příkaz, který se má využít `salesevents`v tématu:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Předchozí zprávy nejsou zobrazeny.

9. Zobrazte události přístupu k auditu v uživatelském rozhraní Ranger.

   ![Události auditu přístupu k Ranger zásad uživatelského rozhraní ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster Kafka, který jste vytvořili, pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.
1. V části **služby**vyberte **clustery HDInsight** .
1. V seznamu clusterů HDInsight, které se zobrazí, klikněte na **...** vedle clusteru, který jste vytvořili pro účely tohoto kurzu. 
1. Klikněte na **Odstranit**. Klikněte na tlačítko **Ano**.

## <a name="troubleshooting"></a>Řešení potíží
Pokud Kafka-Producer-Consumer. jar v clusteru připojeném k doméně nefunguje, ujistěte se prosím, že používáte Kafka-Producer-Consumer. jar v rámci projektu DomainJoined-producent-příjemce (nikoli v rámci projektu, který není připojený k doméně).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Šifrování disků s využitím klíčů spravovaných zákazníky](../disk-encryption.md)
