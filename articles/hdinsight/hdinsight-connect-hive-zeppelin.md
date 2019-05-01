---
title: Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v Azure HDInsight
description: Další informace o použití Apache Zeppelinu ke spuštění dotazy Apache Hive.
keywords: hdinsight hadoop, hive, interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 417e9f8ae78889374983bf77900ee00fa7fc6338
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718938"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v Azure HDInsight 

Clustery HDInsight Interactive Query zahrnují [Apache Zeppelin](https://zeppelin.apache.org/) poznámkových bloků, které můžete použít ke spuštění interaktivních dotazů Hive. V tomto článku se dozvíte, jak k použití Apache Zeppelinu ke spuštění [Apache Hive](https://hive.apache.org/) dotazy v Azure HDInsight. 

## <a name="prerequisites"></a>Požadavky
Před provedením tohoto článku, musíte mít následující položky:

* **Cluster HDInsight Interactive Query**. Zobrazit [vytvořit cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) k vytvoření clusteru HDInsight.  Nezapomeňte vybrat typ Interactive Query. 

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření Zeppelinu poznámky Apache

1. Přejděte na následující adresu URL:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Nahraďte **CLUSTERNAME** názvem vašeho clusteru.

2. Zadejte Hadoop uživatelské jméno a heslo. Na stránce Zeppelin můžete vytvoří novou poznámku. nebo otevřete stávající poznámky. HiveSample obsahuje několik ukázkových dotazů nástroje Hive.  

    ![HDInsight Interactive Query zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Klikněte na tlačítko **vytvořit nová poznámka**.
4. Zadejte nebo vyberte tyto hodnoty:

    - Poznámka: název: Zadejte název pro poznámku.
    - Výchozí překladač: vyberte **JDBC**.

5. Klikněte na tlačítko **vytvořit poznámku**.
6. Spuštěním následujícího dotazu Hive:

        %jdbc(hive)
        show tables

    ![HDInsight Interactive Query zeppelin spustí dotaz](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%Jdbc(hive)** příkaz na prvním řádku říká poznámkovému bloku určený interpret Hive JDBC.

    Dotaz se vrátí jeden tabulkou Hive se nazývají *hivesampletable*.

    Tady jsou dvě další dotazy Hive, které je možné spustit proti hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Porovnání s tradičním Hive, výsledky dotazu vrátit musí rychleji.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k vizualizaci dat pomocí HDInsight [Microsoft Power BI](https://powerbi.microsoft.com/).  Další informace naleznete v následujících článcích:

* [Vizualizace dat pomocí Microsoft Power BI v Azure HDInsight s Apache Hive](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace dat Interactive Query Apache Hive pomocí Power BI v Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./hdinsight-upload-data.md).
