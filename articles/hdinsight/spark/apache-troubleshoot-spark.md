---
title: Řešení potíží s Spark v Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s Apache Spark a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a4dc7293c00097c7a5752e29bf7c9a203cbb31a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721152"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Řešení potíží s Apache Sparku s využitím Azure HDInsight

Další informace o nejčastější problémy a jejich řešení při práci s [Apache Spark](https://spark.apache.org/) datové části v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Apache Ambari v clusterech?

### <a name="resolution-steps"></a>Postup řešení

Hodnoty konfigurace Spark můžete ladit vyhnout výjimku OutofMemoryError aplikací Apache Spark. Následující kroky ukazují výchozí konfigurační hodnoty Spark v Azure HDInsight: 

1. Vyberte v seznamu clusterů **Spark2**.

    ![Vyberte cluster ze seznamu](./media/apache-troubleshoot-spark/update-config-1.png)

2. Vyberte **Configs** kartu.

    ![Vyberte kartu Konfigurace](./media/apache-troubleshoot-spark/update-config-2.png)

3. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**.

    ![Vyberte výchozí nastavení vlastní spark](./media/apache-troubleshoot-spark/update-config-3.png)

4. Vyhledejte nastavení hodnoty, které je potřeba upravit, jako například **spark.executor.memory**. V takovém případě hodnota **4608m** je příliš vysoká.

    ![Vyberte pole spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Nastavte hodnotu na doporučené nastavení. Hodnota **2048m** se doporučuje pro toto nastavení.

    ![Změňte hodnotu na 2 048 m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Uložte hodnotu a pak konfiguraci uložte. Na panelu nástrojů vyberte **Uložit**.

    ![Uložte nastavení a konfigurace](./media/apache-troubleshoot-spark/update-config-6a.png)

    Pokud žádné konfigurace, které je potřeba věnovat pozornost, se zobrazí oznámení. Položky a potom vyberte **i přesto pokračovat**. 

    ![Vyberte přesto pokračovat](./media/apache-troubleshoot-spark/update-config-6b.png)

    Zápis poznámky o změnách konfigurace a pak vyberte **Uložit**.

    ![Zadejte poznámku o provedené změny](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Pokaždé, když je uložen na konfiguraci, budete vyzváni k restartování služby. Vyberte **restartovat**.

    ![Vyberte možnost restartování](./media/apache-troubleshoot-spark/update-config-7a.png)

    Potvrďte restartování.

    ![Výběr možnosti potvrdit restartujte všechny](./media/apache-troubleshoot-spark/update-config-7b.png)

    Můžete zkontrolovat procesy, které jsou spuštěny.

    ![Zkontrolujte spuštěné procesy](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Můžete přidat konfigurace. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**a pak vyberte **přidat vlastnost**.

    ![Výběr možnosti Přidat vlastnost](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definování nové vlastnosti. Pomocí dialogového okna pro konkrétní nastavení, jako je datový typ, můžete definovat jednu vlastnost. Nebo můžete definovat více vlastností pomocí jednu definici na řádek. 

    V tomto příkladu **spark.driver.memory** vlastnost je definována s hodnotou **4g**.

    ![Definovat nové vlastnosti](./media/apache-troubleshoot-spark/update-config-9.png)

10. Uložte konfiguraci a potom restartujte službu, jak je popsáno v kroku 6 a 7.

Tyto změny jsou platné pro celý cluster, ale lze přepsat při odesílání úlohy Spark.

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Poznámkový blok Jupyter v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu co způsobí, že výjimka OutofMemoryError aplikací Apache Spark.

2. V první buňky Poznámkový blok Jupyter po **%% konfigurace** směrnice, určení konfigurací Sparku v platném formátu JSON. Podle potřeby změňte skutečnými hodnotami:

    ![Přidat konfiguraci](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Spark pomocí Apache Livy v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu co způsobí, že výjimka OutofMemoryError aplikací Apache Spark. 

2. Odeslání aplikace Spark na Livy pomocí klienta REST jako cURL. Použijte příkaz podobný následujícímu. Podle potřeby změňte skutečnými hodnotami:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak nakonfigurovat aplikaci s použitím skriptu spark-submit Apache Spark v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Chcete-li určit, které Spark konfigurace muset nastavit a jaké hodnoty, najdete v tématu co způsobí, že výjimka OutofMemoryError aplikací Apache Spark.

2. Spusťte prostředí sparku s využitím příkaz podobný následujícímu. Podle potřeby změňte skutečné hodnoty konfigurace: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>Co způsobí, že výjimka OutofMemoryError aplikací Apache Spark?

### <a name="detailed-description"></a>Podrobný popis

Aplikace Spark se nezdaří s následující typy nezachycených výjimek:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Pravděpodobná příčina

Nejpravděpodobnější příčinou této výjimky je, že není dostatek paměti haldy je přidělit virtuálním počítačům Java (JVMs). Tyto JVMs se spouští jako exekutor nebo ovladače jako součást aplikace Spark. 

### <a name="resolution-steps"></a>Postup řešení

1. Určete maximální velikost dat Spark aplikace zpracovává. Můžete provést odhad na základě maximální velikosti vstupních dat, dočasných dat, který je produkovaných transformací vstupních dat a výstupních dat, který je vytvořen, pokud aplikace je další transformací dočasných dat. Tento proces může být iterační, pokud nemůžete formální počáteční odhad. 

2. Ujistěte se, že cluster HDInsight, který se chystáte použít, má dostatek prostředků z hlediska paměti a jader k obsluze aplikace Spark. Této služby můžete zjistit zobrazením část hodnoty v uživatelském rozhraní YARN clusteru metriky **paměti používá** vs. **Celkové paměti**, a **využitých** vs. **Celkových virtuálních jader**.

3. Nastavte následující konfiguraci Sparku na odpovídající hodnoty, které by neměly být delší než 90 % dostupné paměti a jader. Hodnoty mohou být také v rámci požadavky aplikace Spark na paměť: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Chcete-li vypočítat celkové paměti používané všechny moduly provádění: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Chcete-li vypočítat celkové paměti používané ovladače:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Další čtení

- [Přehled správy paměti Apache Sparku](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Ladit aplikace Apache Spark v clusteru služby HDInsight](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
