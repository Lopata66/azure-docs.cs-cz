---
title: Co je Apache HBase v Azure HDInsight?
description: Úvod do Apache HBase v HDInsight, databáze typu NoSQL postavené na Hadoop. Další informace o případech použití a porovnání HBase s dalšími clustery Hadoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: b41c34d4dcbfa4107383318cd9665a41edee79f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120469"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co je Apache HBase v Azure HDInsight

[Apache HBase](https://hbase.apache.org/) je databáze NoSQL open source, která je založená na [Apache Hadoop](https://hadoop.apache.org/) a modelové po [Google BigTable](https://cloud.google.com/bigtable/). HBase poskytuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi schemaless uspořádané podle rodin sloupců.

HBase je z pohledu uživatele, podobně jako databáze. Data se ukládají do řádků a sloupců tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. Může se spoléhat na redundanci dat, zpracování dávkou a další funkce, které jsou poskytovány pomocí distribuovaných aplikací v ekosystému Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak je implementované Apache HBase v Azure HDInsight?

HDInsight HBase je nabízena jako spravovaný cluster, který je integrován do prostředí Azure. Clustery jsou nakonfigurovány k přímému ukládání dat do [služby Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) což zajišťuje nízkou latenci a zvýšení pružnosti ve volbách výkonu a nákladů. To umožňuje zákazníkům vytvářet interaktivní weby, které pracují s rozsáhlými datovými sadami a vytvářet služby, které ukládají údaje ze snímačů a telemetrie z milionů koncových bodů a analyzovat tato data pomocí úloh Hadoop. HBase a Hadoop jsou dobré počáteční body pro projekt velkých objemů dat v Azure; zejména umožňují spolupráci aplikací v reálném čase s rozsáhlými datovými sadami.

Implementace HDInsight využívá architekturu škálování HBase k zajištění automatického dělení tabulek, silnou konzistenci pro čtení a zápis a automatické převzetí služeb při selhání. Výkon je zvýšen ukládáním do mezipaměti pro čtení a vysokou propustností datových proudů pro zápis. Cluster HBase můžete vytvořit uvnitř virtuální sítě. Podrobnosti najdete v tématu [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Jakým způsobem jsou data spravována v HDInsight HBase?
Data mohou být spravována v HBase pomocí příkazů `create`, `get`, `put`, a `scan` z prostředí HBase. Data se zapisují do databáze pomocí `put` a čtou se pomocí `get`. Příkaz `scan` se používá k načítání dat z více řádků v tabulce. Data lze také spravovat pomocí rozhraní API HBase C#, které poskytuje knihovna klienta nad HBase REST API. Databáze aplikace HBase může být dotazována také pomocí [Apache Hive](https://hive.apache.org/). Úvod do těchto programovacích modelů najdete v tématu [začněte používat Apache HBase s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md). Coprocessors jsou také k dispozici, které umožňují zpracování dat v uzlech, které hostují databáze.

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.

## <a name="scenarios-use-cases-for-apache-hbase"></a>Scénáře: Případy použití Apache HBase
Byl vytvořen případ použití canonical, pro které je vytvořené BigTable (a pomocí rozšíření také HBase) vyhledávání na webu. Vyhledávací stroje sestavují indexy, které mapují termíny na webové stránky, které je obsahují. Ale existuje mnoho dalších případů použití, pro které je HBase vhodné – několik z nich je uvedeno v této části.

* Ukládání hodnot klíče
  
    HBase lze použít jako úložiště hodnota klíčů a je vhodný pro správu systémů zasílání zpráv. Facebook používá HBase pro svůj systém zasílání zpráv a je ideální pro ukládání a správu internetové komunikace. WebTable využívá HBase k hledání a správě tabulek, které jsou extrahovány z webových stránek.
* Data snímače
  
    HBase je užitečné pro zaznamenání dat shromážděných přírůstkově z různých zdrojů. To zahrnuje sociální analýzy, časové řady, aktualizaci interaktivních řídicích panelů s trendy a čítači a řízení systémů protokolu auditu. Mezi příklady patří obchodní terminál Bloomberg a otevřené časové řady databáze (OpenTSDB), které ukládají a poskytují přístup k metrikám získaným o stavu serverových systémů.
* Dotaz v reálném čase
  
    [Apache Phoenix](https://phoenix.apache.org/) je modul dotazů SQL pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL.
* HBase jako platforma
  
    Aplikace lze nad HBase spouštět v případě použití jako datového úložiště. Příklady zahrnují Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji a Titan. Aplikace lze také integrovat s HBase. Mezi příklady patří [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [ Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), a [Apache Drill](https://drill.apache.org/).

## <a name="next-steps"></a>Další postup

* [Začněte používat Apache HBase s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurace replikace Apache HBase v HDInsight](apache-hbase-replication.md)
