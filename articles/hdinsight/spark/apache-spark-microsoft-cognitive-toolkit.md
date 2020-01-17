---
title: Microsoft Cognitive Toolkit s Apache Spark – Azure HDInsight
description: Seznamte se s tím, jak se dá získat školicí Microsoft Cognitive Toolkit model hloubkového učení použít pro datovou sadu pomocí rozhraní Spark Python API v clusteru Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 587de168a17c407abf3c2a7797969df013760a9f
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156637"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Použití Microsoft Cognitive Toolkit modelu hloubkového učení s clusterem Azure HDInsight Spark

V tomto článku provedete následující kroky.

1. Spusťte vlastní skript, který nainstaluje [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) do clusteru Azure HDInsight Spark.

2. Nahrajte [Jupyter notebook](https://jupyter.org/) do clusteru [Apache Spark](https://spark.apache.org/) , abyste zjistili, jak použít školicí model Microsoft Cognitive Toolkit hloubkového učení do souborů v účtu Azure Blob Storage pomocí [rozhraní Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html) .

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v tématech [načtení dat a spuštění dotazů s Apache Spark v HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Jak toto řešení pokračuje?

Toto řešení je rozdělené mezi Tento článek a Jupyter Poznámkový blok, který jste nahráli v rámci tohoto článku. V tomto článku provedete následující kroky:

* Spusťte akci skriptu v clusteru HDInsight Spark a nainstalujte Microsoft Cognitive Toolkit a balíčky Pythonu.
* Nahrajte Poznámkový blok Jupyter, který spouští řešení, do clusteru HDInsight Spark.

Do poznámkového bloku Jupyter se zabývá následující zbývající kroky.

* Načtěte vzorové obrázky do pružné nebo RDD distribuované datové sady Spark.
  * Načtěte moduly a definujte přednastavení.
  * Stáhněte si datovou sadu lokálně na clusteru Spark.
  * Převést datovou sadu na RDD.
* Vyhodnotit obrázky pomocí trained Cognitive Toolkit modelu.
  * Stáhněte si vyškolený Cognitive Toolkit model do clusteru Spark.
  * Definujte funkce, které budou používány pracovními uzly.
  * Skóre obrázků v pracovních uzlech.
  * Vyhodnotit přesnost modelu.

## <a name="install-microsoft-cognitive-toolkit"></a>Nainstalovat Microsoft Cognitive Toolkit

Můžete nainstalovat Microsoft Cognitive Toolkit v clusteru Spark pomocí akce skriptu. Akce skriptu používá vlastní skripty k instalaci komponent do clusteru, které nejsou ve výchozím nastavení dostupné. Vlastní skript můžete použít z Azure Portal, pomocí sady HDInsight .NET SDK nebo pomocí Azure PowerShell. Skript můžete také použít k instalaci sady nástrojů buď jako součást vytváření clusteru, nebo po zprovoznění a spuštění clusteru.

V tomto článku používáme portál k instalaci sady Toolkit po vytvoření clusteru. Další způsoby, jak spustit vlastní skript, najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokyny, jak pomocí Azure Portal spustit akci skriptu, najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Ujistěte se, že zadáváte následující vstupy pro instalaci Microsoft Cognitive Toolkit. Pro akci skriptu použijte následující hodnoty:

|Vlastnost |Hodnota |
|---|---|
|Typ skriptu|– Vlastní|
|Name (Název)| Nainstalovat MCT|
|Identifikátor URI skriptu bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Typ (typy) uzlů:|Vedoucí pracovník|
|Parametry|Žádné|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark

Pokud chcete použít Microsoft Cognitive Toolkit s clusterem Azure HDInsight Spark, musíte do clusteru Azure HDInsight Spark načíst Poznámkový blok Jupyter **CNTK_model_scoring_on_Spark_walkthrough. ipynb** . Tento Poznámkový blok je k dispozici na GitHubu na adrese [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Stažení a deextrahování [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net/jupyter`, kde `CLUSTERNAME` je název vašeho clusteru.

1. V poznámkovém bloku Jupyter vyberte **nahrát** v pravém horním rohu a pak přejděte ke stažení a vyberte soubor `CNTK_model_scoring_on_Spark_walkthrough.ipynb`.

    ![Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Nahrání poznámkového bloku Jupyter do clusteru Azure HDInsight Spark")

1. Vyberte **nahrát** znovu.

1. Po nahrání poznámkového bloku klikněte na název poznámkového bloku a pak postupujte podle pokynů v samotném poznámkovém bloku, jak načíst datovou sadu a provést tento článek.

## <a name="see-also"></a>Další informace najdete v tématech

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insight pomocí Apache Spark ve službě HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
