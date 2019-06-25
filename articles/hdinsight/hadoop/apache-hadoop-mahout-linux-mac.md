---
title: Generování doporučení pomocí Apache Mahout a HDInsight (SSH) – Azure
description: Další informace o použití Apache Mahout strojového učení knihovny ke generování filmových doporučení pomocí HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709081"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Generování filmových doporučení pomocí Apache Mahout s linuxovým Apache Hadoop v HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Další informace o použití [Apache Mahout](https://mahout.apache.org) knihovna pro machine learning pomocí Azure HDInsight ke generování filmových doporučení.

Mahout je [strojového učení](https://en.wikipedia.org/wiki/Machine_learning) knihovna pro Apache Hadoop. Mahout obsahuje algoritmy pro zpracování dat, jako je například filtrování, klasifikaci a clustering. V tomto článku můžete využít doporučovací modul generování filmových doporučení, které jsou založeny na filmy, které jste viděli vaši přátelé.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout správy verzí

Další informace o verzi Mahout v HDInsight najdete v tématu [HDInsight verze a součásti systému Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Vysvětlení doporučení

Jednou z funkcí, které poskytuje Mahout je doporučovací modul. Tento modul přijímá data ve formátu `userID`, `itemId`, a `prefValue` (Předvolby pro položku). Mahout pak můžete provádět analýzy společného výskytu určit: *uživatelé, kteří mají preference pro položku je navíc předvolbu pro tyto ostatní položky*. Mahout pak určuje uživatelé s předvolby podobné položky, které je možné použít k doporučení.

Následující pracovní postup je zjednodušený příklad, který používá data o filmech:

* **společného výskytu**: Joe, Alice a Bob všechny líbilo *Hvězdných*, *The Empire katastrof zpět*, a *návrat Jedi*. Mahout Určuje, že uživatelé, kteří se také jako jednu z těchto filmy, jako je další dvě.

* **společného výskytu**: Bob a Alice spokojeni také *The fiktivní Menace*, *útoku klonů*, a *odvety Sith*. Mahout Určuje, že uživatelé, kteří také líbilo předchozí tři filmy, jako jsou tyto tři videa.

* **Doporučení podle podobnosti**: Vzhledem k tomu, že Joe líbilo první tři videa, Mahout vypadá na filmy ostatní se podobá předvolby líbilo, ale nebyla Joe sledované (líbilo nebo hodnocení). V takovém případě se doporučuje Mahout *The fiktivní Menace*, *útoku klonů*, a *odvety Sith*.

### <a name="understanding-the-data"></a>Pochopení dat

Pohodlněji [GroupLens Research](https://grouplens.org/datasets/movielens/) poskytuje data hodnocení filmů, ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici na výchozím úložištěm váš cluster na `/HdiSamples/HdiSamples/MahoutMovieData`.

Existují dva soubory `moviedb.txt` a `user-ratings.txt`. `user-ratings.txt` Soubor se používá během analýzy. `moviedb.txt` Slouží k poskytování informací popisný text při prohlížení výsledků.

Data obsažená v ratings.txt uživatel má strukturu z `userID`, `movieID`, `userRating`, a `timestamp`, která indikuje, jak vysoce hodnocené filmu každého uživatele. Tady je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Spuštění analýzy

Z připojení SSH ke clusteru použijte následující příkaz ke spuštění úlohy doporučení:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Úlohy může trvat několik minut a může spustit několik úloh MapReduce.

## <a name="view-the-output"></a>Zobrazit výstup

1. Po dokončení úlohy pomocí následujícího příkazu zobrazíte generovaný výstup:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Ve výstupu se zobrazí takto:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    První sloupec je `userID`. Hodnoty obsažené v ' [' a ']' jsou `movieId`:`recommendationScore`.

2. Výstup, spolu s moviedb.txt, můžete použít k poskytnutí dalších informací o doporučení. Zkopírujte soubory místně pomocí následujících příkazů:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Tento příkaz zkopíruje výstupní data do souboru s názvem **recommendations.txt** v aktuálním adresáři, spolu se soubory dat videa.

3. Chcete-li vytvořit skript v jazyce Python, který vyhledá názvy video pro data ve výstupu doporučení použijte následující příkaz:

    ```bash
    nano show_recommendations.py
    ```

    Když se otevře editor, použijte jako obsah souboru následující text:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Stisknutím klávesy **Ctrl-X**, **Y**a nakonec **Enter** uložit data.

4. Spusťte skript Pythonu. Následující příkaz předpokládá, že máte v adresáři, kde byly staženy všechny soubory:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Tento příkaz zjistí doporučení generovaných pro uživatele ID 4.

   * **Uživatele ratings.txt** soubor se používá k načtení filmy, které nemají hodnocení.

   * **Moviedb.txt** soubor se používá k načtení názvy videa.

   * **Recommendations.txt** se používá k získání filmových doporučení pro tohoto uživatele.

     Výstup tohoto příkazu se podobá následujícímu textu:

       Skóre sedm let v Tibet (1997) = 5.0 Indiana Jones a poslední Crusade (1989), stanovení skóre = 5.0 čtečky Jaws (1975), skóre = 5.0 smysl a citlivosti (1995), skóre = 5.0 nezávislosti (ID 4) (1996), skóre = 5.0 Moje nejlepším pomocníkem svatbě (1997), stanovení skóre = 5.0 Jerry Maguire (1996), skóre = 5.0 Scream 2 (1997), skóre = 5.0 čas ukončit, (1996), stanovení skóre = 5.0

## <a name="delete-temporary-data"></a>Odstraňování dočasných dat

Mahout úlohy neodebírejte dočasných dat, který je vytvořen při zpracování úlohy. `--tempDir` v úloze příklad k izolaci dočasných souborů do konkrétní cesty pro snadné odstranění je zadaný parametr. Chcete-li odstranit dočasné soubory, použijte následující příkaz:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Pokud chcete spustit příkaz znovu, musíte také odstranit výstupní adresář. Pomocí následujícího postupu odstraňte tento adresář:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí Mahoutu, popisující další způsoby práce s daty v HDInsight:

* [Apache Hive s HDInsight](hdinsight-use-hive.md)
* [Apache Pig s HDInsight](hdinsight-use-pig.md)
* [MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)