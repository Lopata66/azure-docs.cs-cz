---
title: MapReduce a SSH připojení s Apache Hadoop v HDInsight – Azure
description: Zjistěte, jak spouštět úlohy mapreduce je možné pomocí Apache Hadoop v HDInsight pomocí SSH.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 5eac6cd60b9e58dea8901fad076bb481d5f2eb06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078299"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Použití MapReduce se službou Apache Hadoop v HDInsight pomocí SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Zjistěte, jak odesílat úlohy MapReduce z připojení Secure Shell (SSH) k HDInsight.

> [!NOTE]
> Pokud jste už obeznámení s pomocí serverů se systémem Linux Apache Hadoop, ale HDInsight začínáte, přečtěte si téma [tipy k Linuxovým systémem HDInsight](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Požadavky

* Cluster Linuxovým systémem HDInsight (Hadoop v HDInsight)

* Klient SSH. Další informace najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Připojení přes SSH

Připojte se ke clusteru pomocí SSH. Například následující příkaz se připojí ke clusteru s názvem **myhdinsight** jako **sshuser** účtu:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud použijete klíč certifikátu pro ověřování SSH**, budete muset zadat umístění privátního klíče ve vašem systému klienta, například:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud použijete heslo pro ověřování SSH**, budete muset zadat heslo po zobrazení výzvy.

Další informace o použití SSH s HDInsight naleznete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Využít příkazy Hadoop

1. Až se připojíte ke clusteru HDInsight, pomocí následujícího příkazu spusťte úlohu MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Tento příkaz spustí `wordcount` třídu, která je součástí `hadoop-mapreduce-examples.jar` souboru. Používá `/example/data/gutenberg/davinci.txt` dokumentu jako vstup a výstup je uložen na `/example/data/WordCountOutput`.

    > [!NOTE]
    > Další informace o této úlohy MapReduce a ukázková data, najdete v části [použití MapReduce se v Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md).

2. Úlohy vysílá podrobnosti, jak zpracovávat, a vrátí informace podobné následujícímu textu po dokončení úlohy:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Po dokončení úlohy pomocí následujícího příkazu zobrazíte seznam výstupních souborů:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Tento příkaz zobrazí dva soubory `_SUCCESS` a `part-r-00000`. `part-r-00000` Soubor obsahuje výstup pro tuto úlohu.

    > [!NOTE]  
    > Některé úlohy mapreduce je možné může rozdělit mezi více výsledky **část. r ###** soubory. Pokud ano, použít ### příponu k určení pořadí souborů.

4. Pokud chcete zobrazit výstup, použijte následující příkaz:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Tento příkaz zobrazí seznam slov, které jsou součástí **wasb://example/data/gutenberg/davinci.txt** souboru a počet, kolikrát se každé slovo došlo k chybě. Následující text je příkladem data, která je obsažená v souboru:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Shrnutí

Jak je vidět, příkazy Hadoop poskytují snadný způsob, jak spouštět úlohy mapreduce je možné v clusteru HDInsight a pak zobrazte výstup úlohy.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úlohy mapreduce je možné v HDInsight:

* [Použití MapReduce pro Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
