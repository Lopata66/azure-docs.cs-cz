---
title: Topologie Java příklad Apache Storm – Azure HDInsight
description: Zjistěte, jak vytvořit tak, že vytvoříte topologií počtu slov příklad topologií Apache Storm v jazyce Java.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache storm, příklad apache storm, storm java, příklad topologie storm
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 43f68908c8549c2f1d8322b5c4ad3985618cfe6e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695649"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Vytvoření topologie Apache Storm v jazyce Java

Zjistěte, jak vytvořit topologie založené na jazyce Java pro [Apache Storm](https://storm.apache.org/). Můžete zde vytvářet topologie Storm, který implementuje aplikace počet slov. Použijete [Apache Maven](https://maven.apache.org/) chcete sestavit a zabalit projekt. Potom se dozvíte, jak chcete definovat pomocí topologie [Apache Storm tok](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) rozhraní framework.

Po dokončení kroků v tomto dokumentu, můžete nasadit topologii do Apache Storm v HDInsight.

> [!NOTE]  
> Je k dispozici na úplnou verzi příkladů topologie Storm vytvořené v tomto dokumentu [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)

* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.

## <a name="test-environment"></a>Testovací prostředí
Prostředí, používá pro účely tohoto článku byl počítač se systémem Windows 10.  Příkazy byly provedeny v příkazovém řádku a různé soubory byly upravit článek přeložený překladatelem Poznámkový blok.

Z příkazového řádku zadejte následující příkazy k vytvoření pracovní prostředí:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

Zadáním následujícího příkazu vytvořte projekt Maven s názvem **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Tento příkaz vytvoří adresář s názvem `WordCount` na aktuální pozici, která obsahuje základní projekt Maven. Druhý příkaz změní aktuální pracovní adresář pro `WordCount`. Třetí příkaz vytvoří nový adresář `resources`, který se použije později.  `WordCount` Adresář obsahuje následující položky:

* `pom.xml`: Obsahuje nastavení pro projekt Maven.
* `src\main\java\com\microsoft\example`: Obsahuje kód vaší aplikace.
* `src\test\java\com\microsoft\example`: Obsahuje testy pro vaši aplikaci.  

### <a name="remove-the-generated-example-code"></a>Odebrat generovaný ukázkový kód

Odstranit vygenerované testy a soubory aplikace `AppTest.java`, a `App.java` zadáním následujících příkazů:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Přidat úložiště Maven

HDInsight je založené na Hortonworks Data Platform (HDP), doporučujeme používat úložiště Hortonworks ke stažení závislostí pro projekty Apache Storm.  

Otevřít `pom.xml` tak, že zadáte následující příkaz:

```cmd
notepad pom.xml
```

Pak přidejte následující kód XML po `<url> https://maven.apache.org</url>` řádku:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Přidání vlastností

Maven můžete zadat hodnoty na úrovni projektu, jako vlastnosti. V `pom.xml`, přidejte následující text po `</repositories>` řádku:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Můžete teď tuto hodnotu použijete v dalších částech `pom.xml`. Například při zadání verze komponenty Storm, můžete použít `${storm.version}` místo pevného kódování hodnotu.

## <a name="add-dependencies"></a>Přidat závislosti

Přidáte závislost pro komponenty Storm. V `pom.xml`, přidejte následující text do `<dependencies>` části:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

V době kompilace Maven pomocí těchto informací k vyhledání `storm-core` v úložiště Maven. Nejprve hledá v úložišti na místním počítači. Pokud nejsou soubory, nástroje Maven stáhne z veřejného úložiště Maven a ukládá je do místního úložiště.

> [!NOTE]  
> Všimněte si, že `<scope>provided</scope>` řádek v této části. Toto nastavení určuje Maven k vyloučení **storm core** z jakékoli soubory JAR, které jsou vytvořeny, protože se poskytuje v systému.

## <a name="build-configuration"></a>Konfigurace sestavení

Moduly plug-in maven umožňují přizpůsobit fáze sestavení umísťují projektu. Příklad jak je projekt kompilován nebo jak zabalíte ji do souboru JAR. V `pom.xml`, přidejte následující text přímo nad `</project>` řádku.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

V této části se používá k přidání modulů plug-in, prostředky a další možnosti konfigurace sestavení. Úplný přehled o `pom.xml` souborů naleznete v tématu [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Přidat moduly plug-in

* **Exec Maven Plugin**

    Pro topologií Apache Storm, které jsou implementovány v jazyce Java [plug-in Maven Exec](https://www.mojohaus.org/exec-maven-plugin/) je užitečné, protože umožňuje snadno spouštět místně ve vašem vývojovém prostředí topologie. Přidejte následující text do `<plugins>` část `pom.xml` souboru modulu plug-in Exec Maven:
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
            <goals>
                <goal>exec</goal>
            </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler Plugin**

    Další užitečné modul plug-in je [plug-in Apache Maven kompilátoru](https://maven.apache.org/plugins/maven-compiler-plugin/), který se používá, chcete-li změnit možnosti kompilace. Změna verze pro Javu, která používá Maven pro zdroj a cíl pro vaši aplikaci.
    
  * Pro HDInsight __3.4 nebo starší__, nastavit zdroj a cílit na verzi Javy k __1.7__.
    
  * Pro HDInsight __3.5__, nastavit zdroj a cílit na verzi Javy k __1.8__.
    
    Přidejte následující text do `<plugins>` část `pom.xml` souboru modulu plug-in Apache Maven kompilátoru. Tento příklad určuje verzi 1.8 je cílová HDInsight verze 3.5.
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>Konfigurace prostředků

V části prostředky umožňuje zahrnout prostředky bez kódu, jako jsou konfigurační soubory, které jsou potřeba součástmi v topologii. V tomto příkladu přidejte následující text `<resources>` část `pom.xml` souboru.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

V tomto příkladu přidá adresáře prostředků v kořenovém adresáři projektu (`${basedir}`) jako umístění, která obsahuje prostředky a zahrnuje soubor s názvem `log4j2.xml`. Tento soubor slouží ke konfiguraci, jaké informace je protokolovaným v topologii.

## <a name="create-the-topology"></a>Vytvoření topologie

Topologie založené na jazyce Java Apache Storm se skládá z tři komponenty, které je nutné vytvořit (nebo odkaz) jako závislost.

* **Spouts**: Čte data z externích zdrojů a vysílá datových proudů do topologie.

* **Bolts**: Provádí zpracování na datové proudy vyslané spoutů nebo z jiných boltů a vysílá jeden nebo více datových proudů.

* **Topologie**: Definuje, jak jsou uspořádány spoutů a boltů a poskytuje vstupní bod pro topologii.

### <a name="create-the-spout"></a>Vytvořte spout

Pokud chcete snížit požadavky na nastavení externí zdroje dat, následující spout jednoduše generuje náhodné věty. Je upravená verze spout, který je součástí služby [příkladů topologie Storm Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  I když tato topologie používá pouze jeden spout, jiné mohou mít několik, které přísun dat z různých zdrojů do topologie.

Zadejte příkaz a vytvořte a otevřete nový soubor `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru.  Zavřete soubor.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Příklad spout, který načte z externího zdroje dat naleznete v následujících příkladech:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Spout příklad, který čte z Twitteru.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Spout, která čte z Kafka.


### <a name="create-the-bolts"></a>Vytvořit boltů

Funkce Bolts zpracování dat zpracovává. Bolty mohou provádět všechny, například výpočet, trvalého nebo komunikujících na externí komponenty. Tato topologie používá dva bolty:

* **SplitSentence**: Rozdělí věty, protože ho vygeneroval **RandomSentenceSpout** do jednotlivých slov.

* **WordCount**: Počty kolikrát se každé slovo došlo k chybě.


#### <a name="splitsentence"></a>SplitSentence

Zadejte příkaz a vytvořte a otevřete nový soubor `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru.  Zavřete soubor.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Zadejte příkaz a vytvořte a otevřete nový soubor `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru.  Zavřete soubor.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definovat topologii

Topologie propojuje spoutů a bolts společně do grafu, který definuje, jak data proudí mezi komponentami. Poskytuje také pomocné parametry paralelismu, které Storm používá při vytváření instancí komponent v rámci clusteru.

Na následujícím obrázku je základní diagram grafu komponenty pro tuto topologii.

![Diagram znázorňující uspořádání spoutů a boltů](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Pokud chcete implementovat topologii, zadejte příkaz níže vytvořte a otevřete nový soubor `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Zkopírujte a vložte níže uvedený kód java do nového souboru.  Zavřete soubor.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurace protokolování

Storm používá [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) k protokolování informací. Pokud neprovedete konfiguraci protokolování, topologii vysílá diagnostické informace. Pokud chcete řídit, co se do protokolu zapíše, vytvořte soubor s názvem `log4j2.xml` v `resources` adresáře tak, že zadáte následující příkaz:

```cmd
notepad resources\log4j2.xml
```

Zkopírujte a vložte níže uvedený text XML do nového souboru.  Zavřete soubor.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Tato konfigurace XML nakonfiguruje pro nový protokolovací nástroj `com.microsoft.example` třídu, která obsahuje součásti v tomto příkladu topologie. Pro tento protokolovač, kterými zachycuje všechny informace o protokolování, protože ho vygeneroval komponenty v této topologii je nastavena úroveň trasování.

`<Root level="error">` Části nakonfiguruje kořenové úrovni protokolování (vše, co není v `com.microsoft.example`) se protokolovat jenom informace o chybě.

Další informace o konfiguraci protokolování pro Log4j 2 najdete v tématu [ https://logging.apache.org/log4j/2.x/manual/configuration.html ](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Storm verzi 0.10.0 a vyšší využití Log4j 2.x. Používat starší verzí storm Log4j 1.x, který používá jiný formát pro konfiguraci protokolu. Informace o konfiguraci starší, naleznete v tématu [ https://wiki.apache.org/logging-log4j/Log4jXmlFormat ](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testovací topologie místně

Po uložení souboru použijte následující příkaz k otestování topologie místně.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Za běhu, topologie zobrazuje informace pro spuštění. Následující text je příklad výstupu počet slov:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Tento příklad protokol označuje, že slovo "a" bylo aktivováno 113 časy. Počet dál jít nahoru tak dlouho, dokud topologie pracuje, protože spout průběžně vydá stejnou věty.

Mezi emisí slov a počty je interval 5 sekund. **WordCount** součást je nakonfigurovaná pouze generovat informace po přijetí odškrtávání řazených kolekcí členů. Požaduje této značky, které řazených kolekcí členů se doručí jenom každých pět sekund.

## <a name="convert-the-topology-to-flux"></a>Převést na topologii na tok

[Tok](https://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) je nový rámec dostupná se Stormem 0.10.0 a vyšší, který umožňuje oddělit konfiguraci z implementace. Vaše komponenty jsou stále definována v Javě, ale topologii je definováno pomocí souboru YAML. Můžete balíček výchozí definici topologie s projektem, nebo použijte samostatný soubor při odesílání topologie. Při odesílání topologie do Storm, můžete použít proměnné prostředí nebo konfigurační soubory k naplnění hodnot v definici topologie YAML.

Soubor YAML definuje součásti, které budou používat pro topologii a data tok mezi nimi. Soubor YAML můžete zahrnout jako součást na soubor jar nebo můžete použít externí soubor YAML.

Další informace o toku, najdete v části [tok framework (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]  
> Z důvodu [chyb (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) pomocí Storm 1.0.1, budete muset nainstalovat [Storm vývojové prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) topologie tok spouštět místně.

1. Dříve `WordCountTopology.java` definice topologie, ale není potřeba s tokem. Stejný soubor odstraňte také pomocí následujícího příkazu:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. Zadejte příkaz a vytvořte a otevřete nový soubor `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    Zkopírujte a vložte níže uvedený text do nového souboru.  Zavřete soubor.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Zadejte následující příkaz pro otevření `pom.xml` na panelu je popsáno níže:

    ```cmd
    notepad pom.xml
    ```

   * Přidejte následující novou závislost `<dependencies>` části:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * Přidejte následující modul plug-in, který `<plugins>` oddílu. Tento modul plug-in zpracovává vytvořit balíček (soubor jar) pro projekt a platí některé transformace, které jsou specifické pro tok při vytváření balíčku.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * V **plug-in maven exec** `<configuration>` oddíl, změňte hodnotu `<mainClass>` z `${storm.topology}` k `org.apache.storm.flux.Flux`. Toto nastavení umožňuje tok pro zpracování místně spuštěná topologie ve vývoji.

   * V `<resources>` části, přidejte následující text do `<includes>`. Tato konfigurace XML obsahuje soubor YAML, který definuje topologii jako součást projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Otestovat tok topologie místně

1. Zadejte následující příkaz, který zkompilovat a spustit topologii tok pomocí nástroje Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Pokud vaše topologie používá Storm 1.0.1 bits, tento příkaz se nezdaří. Tato chyba je způsobená [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). Místo toho [nainstalovat Storm ve vašem vývojovém prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) a pomocí následujících kroků:
    >
    > Pokud máte [Storm nainstalovaný ve vašem vývojovém prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html), můžete místo toho použijte následující příkazy:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` Parametr spustí topologie v místním režimu ve svém vývojovém prostředí. `-R /topology.yaml` Používá parametr `topology.yaml` souboru prostředků ze souboru jar definovat topologii.

    Za běhu, topologie zobrazuje informace pro spuštění. Následující text je příklad výstupu:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Dochází ke zpoždění 10 sekundách mezi listy zaznamenané informace.

2. Vytvořte novou topologii yaml z projektu.
 
    a. Zadejte následující příkaz pro otevření `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. Následující části a změňte hodnotu vlastnosti `10` k `5`. Tato změna změní interval mezi generování dávky počty slov z 10 sekund až 5.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. Uložte soubor jako `newtopology.yaml`.

3. Pokud chcete spustit topologii, zadejte následující příkaz:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Nebo, pokud máte ve svém vývojovém prostředí Storm:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     Tento příkaz používá `newtopology.yaml` jako definice topologie. Protože jsme nezahrnuli `compile` parametr Maven používá verzi projekt vytvořený v předchozích krocích.

    Jakmile se spustí na topologii, měli byste zaznamenat, čas mezi emitovaný dávky se změnila tak, aby odrážely hodnotu v `newtopology.yaml`. Abyste viděli, že můžete změnit konfiguraci prostřednictvím souboru YAML bez nutnosti znovu kompilovat topologie.

Další informace o těchto a dalších funkcích rozhraní tok, najdete v části [tok (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) je vysokou úroveň abstrakce, která je poskytována Storm. Podporuje stavové zpracování. Hlavní výhodou Trident je, že může zaručit, že všechny zprávy, která vstupuje do topologie se zpracuje pouze jednou. Bez použití Trident, topologii pouze zaručit, že zprávy se zpracuje alespoň jednou. Existují také další rozdíly, jako je integrované součásti, které je možné použít místo vytváření boltů. Ve skutečnosti funkce bolts jsou nahrazené méně obecný komponenty, například funkcí, projekce a filtry.

Trident aplikací můžete vytvořit pomocí projektů Maven. Použijte stejný základní postup uvedenou výše v tomto článku – pouze pro kód se liší. Trident také (aktuálně) nejde v rámci toku.

Další informace o Trident naleznete v tématu [přehled rozhraní API Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Další kroky

Jste se naučili, jak vytvořit Apache Storm topologii s použitím jazyka Java. Teď zjistěte, jak:

* [Nasazení a správa topologií Apache Storm v HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Vývoj topologií C# pro Apache Storm v HDInsight pomocí sady Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Příklad lze najít další topologií Apache Storm návštěvou [příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md).
