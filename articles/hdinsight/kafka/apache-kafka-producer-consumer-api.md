---
title: 'Kurz: Apache Kafka & zákaznických rozhraní API pro zákazníky – Azure HDInsight'
description: Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight. V tomto kurzu zjistíte, jak používat tato rozhraní API se systémem Kafka ve službě HDInsight z aplikace Java.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 65fc3259b0bc5fce61ccd1ceb8df30f1bba49b19
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161711"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Kurz: Použití rozhraní Apache Kafka Producer and Consumer API

Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight.

Rozhraní Kafka Producer API umožňuje aplikacím odesílat datové proudy do clusteru Kafka. Rozhraní Kafka Consumer API umožňuje aplikacím číst datové proudy z clusteru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Předpoklady
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Spuštění aplikace v clusteru

Další informace o rozhraních API najdete v dokumentaci k rozhraní [Producer API](https://kafka.apache.org/documentation/#producerapi) a [Consumer API](https://kafka.apache.org/documentation/#consumerapi) na webu Apache.

## <a name="prerequisites"></a>Předpoklady

* Apache Kafka ve službě HDInsight 3,6. Informace o tom, jak vytvořit Kafka v clusteru HDInsight, najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks) nebo ekvivalent, jako je například OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace se nachází na adrese [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v podadresáři `Producer-Consumer`. Aplikace se skládá primárně ze čtyř souborů:

* `pom.xml`: Tento soubor definuje závislosti projektu, verzi Javy a metody balení.
* `Producer.java`: Tento soubor pomocí rozhraní Producer API odesílá do systému Kafka náhodné věty.
* `Consumer.java`: Tento soubor pomocí rozhraní Consumer API čte data ze systému Kafka a posílá je do výstupu STDOUT.
* `Run.java`: Rozhraní příkazového řádku, které slouží ke spuštění kódu producenta a konzumenta.

### <a name="pomxml"></a>Pom.xml

V souboru `pom.xml` je důležité porozumět následujícímu:

* Závislosti: Tento projekt spoléhá na rozhraní Kafka Producer and Consumer API, která jsou součástí balíčku `kafka-clients`. Tuto závislost definuje následující kód XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    Položka `${kafka.version}` se deklaruje v části `<properties>..</properties>` souboru `pom.xml` a je nakonfigurovaná na verzi systému Kafka v clusteru HDInsight.

* Moduly plug-in: Moduly plug-in Mavenu poskytují různé funkce. V tomto projektu se používají následující moduly plug-in:

    * `maven-compiler-plugin`: Slouží k nastavení verze Javy, kterou projekt používá, na 8. To je verze Javy, kterou používá HDInsight 3.6.
    * `maven-shade-plugin`: Slouží k vygenerování souboru JAR, který obsahuje tuto aplikaci i všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="producerjava"></a>Producer.java

Producent komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a odesílá data do tématu Kafka. Následující fragment kódu pochází ze souboru [producent. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) z [úložiště GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) a ukazuje, jak nastavit vlastnosti producenta:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Konzument komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a ve smyčce čte záznamy. Následující fragment kódu ze souboru [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) nastaví vlastnosti příjemce:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

V tomto kódu je konzument nakonfigurovaný tak, aby četl od začátku tématu (hodnota `auto.offset.reset` je nastavená na `earliest`).

### <a name="runjava"></a>Run.java

Soubor [Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) poskytuje rozhraní příkazového řádku, které spouští buď producenta, nebo kód příjemce. Jako parametr je potřeba zadat informace o hostiteli zprostředkovatele Kafka. Volitelně můžete zahrnout hodnotu ID skupiny, kterou používá proces příjemce. Pokud vytvoříte více instancí příjemce pomocí stejného ID skupiny, vyčtou se z tématu čtení z vyrovnávání zatížení.

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení příkladu

1. Stáhněte a extrahujte příklady z [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Nastavte aktuální adresář na umístění adresáře `hdinsight-kafka-java-get-started\Producer-Consumer` a použijte tento příkaz:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadejte následující příkaz, který zkopíruje soubor `kafka-producer-consumer-1.0-SNAPSHOT.jar` do clusteru HDInsight. Po zobrazení výzvy zadejte heslo uživatele SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a>Spuštění příkladu

1. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadáním následujícího příkazu otevřete připojení SSH ke clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nainstalujte [JQ](https://stedolan.github.io/jq/)procesor JSON pro příkazový řádek. V otevřeném připojení SSH zadejte následující příkaz pro instalaci `jq`:

    ```bash
    sudo apt -y install jq
    ```

1. Nastavte proměnnou hesla. Nahraďte `PASSWORD` heslem přihlášení clusteru a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahuje správně použita název clusteru. V závislosti na tom, jak byl cluster vytvořen, může být skutečná velikost názvu clusteru odlišná, než očekáváte. Tento příkaz získá skutečnou velikost písmen a uloží ji do proměnné. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```
    > [!Note]  
    > Pokud provádíte tento proces mimo cluster, existuje jiný postup pro uložení názvu clusteru. Získá název clusteru z Azure Portal malými písmeny. Pak v následujícím příkazu nahraďte název clusteru pro `<clustername>` a spusťte ho: `export clusterName='<clustername>'`.  

1. Chcete-li získat hostitele zprostředkovatele Kafka, použijte následující příkaz:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za NSG, spusťte tento příkaz z počítače, který má přístup k Ambari.

1. Zadáním následujícího příkazu vytvořte Kafka téma, `myTest`.

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Pokud chcete spustit producenta a zapsat data do tématu, použijte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Jakmile bude producent hotový, pomocí následujícího příkazu zahajte čtení z tématu:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    Zobrazí se počet načtených záznamů spolu s celkovým počtem.

1. Konzumenta ukončíte stisknutím __Ctrl+C__.

### <a name="multiple-consumers"></a>Víc současných konzumentů

Konzumenti Kafka při čtení záznamů používají skupiny konzumentů. Výsledkem použití skupiny s více konzumenty je vyvážení zatížení při čtení záznamů z tématu. Každý konzument ze skupiny obdrží určitou část záznamů.

Aplikace konzumenta přijímá parametr, který se použije jako ID skupiny. Například následující příkaz spustí konzumenta s použitím ID skupiny `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Konzumenta ukončíte stisknutím __Ctrl+C__.

Pokud chcete vidět tento proces v akci, použijte následující příkaz:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Tento příkaz pomocí `tmux` rozdělí terminál do dvou sloupců. V obou sloupcích je spuštěný konzument se stejnou hodnotou ID skupiny. Jakmile konzumenti dokončí čtení, všimněte si, že oba přečetli pouze část záznamů. Pro ukončení `tmux`použijte dvakrát __kombinaci kláves CTRL + C__ .

Konzumace klienty ze stejné skupiny se realizuje rozdělením tématu na oddíly. V tomto vzorovém kódu má dříve vytvořené téma `test` osm oddílů. Pokud spustíte osm konzumentů, každý z nich bude číst záznamy z jednoho oddílu tématu.

> [!IMPORTANT]  
> Ve skupině příjemců nemůže být víc instancí konzumentů než má téma oddílů. V tomto příkladu může skupina konzumentů obsahovat až osm konzumentů, protože to je počet oddílů tématu. Nebo můžete mít více skupin konzumentů, každou s maximálně osmi konzumenty.

Záznamy uložené v Kafka jsou uloženy v pořadí, v jakém jsou přijímány v rámci oddílu. Pro dosažení doručování záznamů ve správném pořadí *v rámci oddílu* vytvořte skupinu příjemců, ve které bude počet instancí konzumentů odpovídat počtu oddílů. Pro dosažení doručování záznamů ve správném pořadí *v rámci tématu* vytvořte skupinu obsahující pouze jednu instanci konzumenta.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat Apache Kafkaho výrobce a zákaznického rozhraní API s Kafka ve službě HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

> [!div class="nextstepaction"]
> [Analyzovat protokoly Apache Kafka](apache-kafka-log-analytics-operations-management.md)
