---
title: 'Rychlý start: Vytvoření clusteru Spark v HDInsight pomocí šablony'
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Resource Manageru vytvořit cluster Apache Spark ve službě Azure HDInsight a jak spustit jednoduchý dotaz Spark SQL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 4bfa325017d485a76bfe87c0f7facde4acf8f2b4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066101"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rychlý start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí šablony Resource Manageru

Zjistěte, jak vytvořit [Apache Spark](https://spark.apache.org/) clusteru v Azure HDInsight a spouštění dotazů Spark SQL proti [Apache Hive](https://hive.apache.org/) tabulky. Apache Spark umožňuje rychlou analýzu dat a clusterové výpočty s využitím zpracování v paměti. Informace o Sparku v HDInsight najdete v tématu [přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md).

V tomto rychlém startu pomocí šablony Resource Manageru vytvoříte cluster HDInsight Spark. Můžete zobrazit podobné šablony [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). Odkaz na šablonu najdete [tady](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

Cluster jako úložiště využívá Azure Storage Blob. Další informace o použití Data Lake Storage Gen2 najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Clustery HDInsight se fakturují za minutu bez ohledu na to, jestli je používáte, nebo ne. Až přestanete cluster používat, nezapomeňte ho odstranit. Další informace najdete v části [Vyčištění prostředků](#clean-up-resources) tohoto článku.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-hdinsight-spark-cluster"></a>Vytvoření clusteru HDInsight Spark

Vytvořte cluster HDInsight Spark pomocí šablony Azure Resource Manageru. Šablony lze nalézt v [Githubu](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Syntaxi JSON a vlastnosti clusteru najdete v tématu [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters).

1. Výběrem následujícího odkazu otevřete šablonu na webu Azure Portal na nové kartě prohlížeče:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Nasazení do Azure</a>

2. Zadejte následující hodnoty:

    | Vlastnost | Hodnota |
    |---|---|
    |**Předplatné**|Vyberte předplatné Azure použité k vytvoření tohoto clusteru. V tomto rychlém startu se používá předplatné **&lt;název předplatného Azure>** . |
    | **Skupina prostředků**|Vytvořte skupinu prostředků nebo vyberte existující. Skupina prostředků slouží ke správě prostředků Azure pro vaše projekty. V tomto rychlém startu se používá název nové skupiny prostředků **myspark20180403rg**.|
    | **Umístění**|Vyberte umístění skupiny prostředků. Šablona toto umístění používá k vytvoření clusteru i jako výchozí úložiště clusteru. V tomto rychlém startu se používá umístění **USA – východ 2**.|
    | **Název clusteru**|Zadejte název clusteru HDInsight, který chcete vytvořit. V tomto rychlém startu se používá název nového clusteru **myspark20180403**.|
    | **Přihlašovací jméno a heslo clusteru**|Výchozí přihlašovací jméno je admin. Zvolte heslo pro přihlášení ke clusteru. V tomto rychlém startu se používá přihlašovací jméno **admin**.|
    | **Uživatelské jméno a heslo SSH**|Zvolte heslo pro uživatele SSH. V tomto rychlém startu se používá uživatelské jméno SSH **sshuser**.|

    ![Vytvoření clusteru HDInsight Spark pomocí šablony Azure Resource Manageru](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Vytvoření clusteru Spark ve službě HDInsight pomocí šablony Azure Resource Manageru")

3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**, vyberte **Připnout na řídicí panel** a pak vyberte **Koupit**. Zobrazí se nová dlaždice s názvem **Nasazení šablony**. Vytvoření clusteru trvá přibližně 20 minut. Než budete moct pokračovat k další relaci, musí se cluster nejdříve vytvořit.

Pokud narazíte na problém s vytvářením clusterů HDInsight, může to být způsobeno tím, že k tomu nemáte správná oprávnění. Další informace najdete v tématu popisujícím [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="install-intellijeclipse-for-spark-application"></a>Instalace IntelliJ/Eclipse pro aplikaci Spark
Použití sady Azure Toolkit pro modul plug-in IntelliJ nebo Eclipse k vývoji aplikací Spark napsané v [Scala](https://www.scala-lang.org/)a odešlete je do clusteru Azure HDInsight Spark přímo z (IntelliJ nebo Eclipse, integrované vývojové prostředí INTEGROVANÉ VÝVOJOVÉ PROSTŘEDÍ). Další informace najdete v článcích [o použití IntelliJ k vytvoření/odeslání aplikace Spark](./apache-spark-intellij-tool-plugin.md) a [o použití Eclipse k vytvoření/odeslání aplikace Spark](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>Instalace nástroje VSCode pro aplikace PySpark/Hive
Naučte se používat sadu nástrojů Azure HDInsight Tools for Visual Studio Code (VSCode) k vytvoření a odeslání dávkových úloh Hive, interaktivních dotazů Hive, dávky PySpark a interaktivních skriptů PySpark. Nástroje Azure HDInsight Tools můžete nainstalovat na platformách, které podporuje nástroj VSCode. Patří sem Windows, Linux a macOS. Další informace najdete v článku [o použití nástroje VSCode k vytvoření/odeslání aplikace PySpark](../hdinsight-for-vscode.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

[Poznámkový blok Jupyter](https://jupyter.org/) interaktivní prostředí poznámkového bloku podporující různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Vyberte **Clustery HDInsight** a pak vyberte cluster, který jste vytvořili.

    ![otevření clusteru HDInsight na webu Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Na portálu v části **Řídicí panely clusteru** klikněte na **Poznámkový blok Jupyter**. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

   ![Otevření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otevření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

4. Vyberte **Nový** > **PySpark** a vytvořte poznámkový blok.

   ![Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

   Nový poznámkový blok se vytvoří a otevře s názvem Bez názvu (Bez názvu.pynb).


## <a name="run-spark-sql-statements"></a>Spouštění příkazů Spark SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a transformaci dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Ověřte, že je jádro připravené. Jádro bude připravené, až se vedle názvu jádra v poznámkovém bloku zobrazí prázdný kroužek. Plný kruh označuje, že je jádro zaneprázdněno.

    ![Dotaz Hive v HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Dotaz Hive v HDInsight Spark")

    Při prvním spuštění poznámkového bloku jádro provede některé úlohy na pozadí. Počkejte, až bude jádro připravené.
2. Do prázdné buňky vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Příkaz vypíše tabulky Hive v clusteru:

    ```sql
    %%sql
    SHOW TABLES
    ```
    Pokud používáte poznámkový blok Jupyter s clusterem HDInsight Spark, získáte přednastavenou relaci `spark`, kterou můžete použít ke spouštění dotazů Hive pomocí Spark SQL. `%%sql` říká poznámkovému bloku Jupyter, aby ke spuštění dotazu Hive použil přednastavenou relaci `spark`. Dotaz načte prvních 10 řádků z tabulky Hive (**hivesampletable**), která je ve výchozím nastavení k dispozici na všech clusterech HDInsight. Při prvním odeslání dotazu vytvoří Jupyter pro poznámkový blok aplikaci Spark. Dokončení trvá přibližně 30 sekund. Jakmile bude aplikace Spark připravená, přibližně během jedné sekundy se dotaz provede a vygeneruje výsledky. Výstup bude vypadat následovně:

    ![Dotaz Hive v HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Dotaz Hive v HDInsight Spark")

    Při každém spuštění dotazu v Jupyter se v názvu okna webového prohlížeče zobrazí stav **(Busy)** (Zaneprázdněn) společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu.

2. Spuštěním dalšího dotazu zobrazíte data v tabulce `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Obrazovka by se měla aktualizovat a zobrazit výstup dotazu.

    ![Výstup dotazu Hive v HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Výstup dotazu Hive v HDInsight Spark")

2. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**. Vypnutím poznámkového bloku se uvolní prostředky clusteru včetně aplikace Spark.

## <a name="clean-up-resources"></a>Vyčištění prostředků
HDInsight ukládá vaše data a poznámkové bloky Jupyter ve službě Azure Storage nebo Azure Data Lake Store, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na kurzu uvedeném v části [Další kroky](#next-steps), měli byste cluster zachovat.

Přepněte zpět na web Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight Spark a spustit základní dotaz Spark SQL. V dalším kurzu se dozvíte, jak pomocí clusteru HDInsight Spark spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
>[Spouštění interaktivních dotazů na Apache Sparku](./apache-spark-load-data-run-query.md)


