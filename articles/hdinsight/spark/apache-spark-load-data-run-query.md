---
title: 'Kurz: Načtení dat a spouštění dotazů v clusteru Apache Spark v Azure HDInsight '
description: Zjistěte, jak načítat data a spouštět interaktivní dotazy v clusterech Spark ve službě Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 05/16/2019
ms.openlocfilehash: 09509b32320fb10b8ab3d563442b6d0fb44ad34e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65909197"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Kurz: Načtení dat a spouštění dotazů v clusteru Apache Spark v Azure HDInsight

V tomto kurzu se dozvíte, jak vytvořit datový rámec ze souboru csv a jak spouštět interaktivní dotazy Spark SQL na [Apache Spark](https://spark.apache.org/) clusteru v Azure HDInsight. Ve Sparku je datový rámec distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Datový rámec je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření datového rámce ze souboru CSV
> * Spouštění dotazů nad datovým rámcem

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Zobrazit [vytvořit cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace. 

1. Upravte adresu URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` nahrazením `SPARKCLUSTER` s názvem vašeho clusteru Spark. Potom zadejte adresu URL upravených ve webovém prohlížeči. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

2. Z Jupyter webové stránky, vyberte **nový** > **PySpark** vytvoření poznámkového bloku. 

   ![Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem bez názvu (`Untitled.ipynb`).

    > [!NOTE]  
    > Díky použití jádra PySpark k vytvoření poznámkového bloku se relace `spark` vytvoří automaticky za vás při spuštění první buňky kódu. Není potřeba relaci vytvářet explicitně.

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření datového rámce ze souboru CSV

Aplikace může vytvářet datové rámce přímo ze souborů nebo složek ve vzdáleném úložišti, jako je Azure Storage nebo Azure Data Lake Storage, z tabulky Hive nebo z dalších zdrojů dat podporovaných Sparkem, jako je služba Cosmos DB, Azure SQL Database, Data Warehouse atd. Následující snímek obrazovky ukazuje snímek souboru HVAC.csv použitého v tomto kurzu. Tento soubor CSV je součástí všech clusterů HDInsight Spark. Data zaznamenávají změny teploty několika budov.
    
![Snímek dat pro interaktivní dotaz Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snímek dat pro interaktivní dotaz Spark SQL")

1. Vložte následující kód do prázdné buňky poznámkového bloku Jupyter a potom stiskněte klávesu **SHIFT + ENTER** spuštění kódu. Kód naimportuje typy potřebné pro tento scénář:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Po spuštění interaktivního dotazu v Jupyter se název okna nebo karty webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    ![Stav interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stav interaktivního dotazu Spark SQL")

2. Spuštěním následujícího kódu vytvořte datový rámec a dočasnou tabulku (**hvac**). 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Spouštění dotazů nad datovým rámcem

Po vytvoření tabulky můžete nad daty spustit interaktivní dotaz.

1. V prázdné buňce poznámkového bloku spusťte následující kód:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Zobrazí se následující tabulkový výstup.

     ![Tabulkový výstup výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabulkový výstup výsledku interaktivního dotazu Spark")

2. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete výstup zobrazit v podobě plošného grafu, vyberte **Oblast** a pak nastavte další hodnoty následujícím způsobem.

    ![Plošný graf výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Plošný graf výsledku interaktivního dotazu Spark")

3. V panelu nabídek poznámkového bloku, přejděte na **souboru** > **uložit a kontrolního bodu**.

4. Pokud právě začínáte s [dalším kurzem](apache-spark-use-bi-tools.md), nechte poznámkový blok otevřený. Pokud ne, vypnout a uvolnit tak prostředky clusteru Poznámkový blok: přejděte na panelu nabídek poznámkového bloku **souboru** >  **zavřít a zastavit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

HDInsight vaše data a poznámkové bloky Jupyter uloží ve službě Azure Storage nebo Azure Data Lake Storage, takže je můžete clusteru bezpečně odstranit, až nebude používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit datový rámec ze souboru csv a jak spouštět interaktivní dotazy Spark SQL proti clusteru Apache Spark v Azure HDInsight. Přejděte k dalšímu článku, pokud chcete zobrazit, jak můžete načíst data, která jste zaregistrovali v Apache Spark na nástroj pro analýzu BI, jako je Power BI.

> [!div class="nextstepaction"]
> [Analýza dat pomocí nástrojů BI](apache-spark-use-bi-tools.md)