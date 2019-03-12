---
title: 'Rychlý start: Začínáme s Apache Hadoop a Apache Hive pomocí webu Azure portal – Azure HDInsight'
description: Naučte se vytvářet clustery HDInsight pomocí webu Azure Portal a dotazy na data pomocí Hivu.
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e46237117ae3e4a2879947be2c065bba5e720fb8
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570976"
---
# <a name="quickstart-get-started-with-apache-hadoop-and-apache-hive-in-azure-hdinsight-using-the-azure-portal"></a>Rychlý start: Začínáme s Apache Hadoop a Apache Hive v Azure HDInsight pomocí webu Azure portal

V tomto článku se dozvíte, jak vytvořit [Apache Hadoop](https://hadoop.apache.org/) clusterů v HDInsight pomocí webu Azure portal a potom spouštět úlohy Apache Hive v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy.

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight najdete v tématu [co je nového ve verzích clusterů systému Apache Hadoop poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvořit cluster Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Data a analýzy** > **HDInsight**. 

    ![Databricks na webu Azure Portal](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-hdinsight-on-portal.png "Databricks na webu Azure Portal")

2. V části **HDInsight** > **Rychle vytvořit** > **Základy** zadejte hodnoty podle tohoto snímku obrazovky:

    ![HDInsight Linux začínáme zadání základních hodnot clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-basic-values.png "Zadání základních hodnot pro vytvoření clusteru HDInsight")

    Zadejte nebo vyberte tyto hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název clusteru**     | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může mít až 59 znaků a může obsahovat písmena, číslice a pomlčky. První a poslední znak názvu nemůže být pomlčka. |
    |**Předplatné**     |  Vyberte své předplatné Azure. |
    |**Typ clusteru**     | Tohle zatím přeskočte. Tuto hodnotu zadáte v dalším kroku postupu.|
    |**Přihlašovací uživatelské jméno a heslo clusteru**     | Výchozí přihlašovací jméno je **admin** (správce). Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |**Uživatelské jméno SSH** | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Pro uživatelské jméno SSH můžete zadat jiný název. |
    | **Použít stejné heslo jako pro přihlášení ke clusteru** | Pokud toto políčko zaškrtnete, použije se stejné heslo pro uživatele SSH, jaké jste zadali pro přihlášení uživatele clusteru.|
    |**Skupina prostředků**     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |**Umístění**     | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
        
3. Vyberte **Typ clusteru** a pak zadejte hodnoty, jak ukazuje tento snímek obrazovky:

    ![HDInsight Linux začínáme zadání základních hodnot clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-cluster-type.png "Zadání základních hodnot pro vytvoření clusteru HDInsight")

    Vyberte tyto hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Typ clusteru**     | Vyberte **Hadoop** |
    |**Operační systém**     | Vyberte **Linux** |
    |**Verze**     | Vyberte **Hadoop 2.7.3 (HDI 3.6)**|

    Klikněte na **Vybrat** a pak na **Další**.

4. Na kartě **Úložiště** zadejte hodnoty, jak ukazuje tento snímek obrazovky:

    ![HDInsight Linux začínáme zadání hodnot úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-select-storage.png "Zadání hodnot úložiště pro vytvoření clusteru HDInsight")

    Vyberte tyto hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Typ primárního úložiště**     | Pro účely tohoto článku vyberte **úložiště Azure**, aby se jako výchozí účet úložiště použil Azure Storage Blob. Jako výchozí úložiště můžete vybrat také úložiště Azure Data Lake. |
    |**Metoda výběru**     |  Pro účely tohoto článku vyberte **Moje předplatné**, aby se použil účet úložiště z vašeho předplatného Azure. Pokud chcete používat účet úložiště z jiných předplatných, vyberte **Přístupový klíč** a pak zadejte přístupový klíč k účtu. |
    |**Vytvoření nového účtu úložiště**     | Zadejte název účtu úložiště.|

    Přijměte všechny ostatní výchozí hodnoty a pak vyberte **Další**.

5. Na kartě **Souhrn** zkontrolujte hodnoty, které jste vybrali v dřívějších krocích.

    ![HDInsight Linux začínáme souhrn clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-summary.png "Začínáme s HDInsight Linux a souhrn clusteru")
      
4. Vyberte **Vytvořit**. Na řídicím panelu portálu by se měla zobrazit nová dlaždice s názvem **Odesílá se nasazení pro HDInsight**. Vytvoření clusteru trvá přibližně 20 minut.

    ![Počáteční skupina prostředků HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/deployment-progress-tile.png "Skupina prostředků clusteru Azure HDInsight")

4. Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.
   
    ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-cluster-settings.png "Vlastnosti clusteru HDInsight")    
    
    Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být umístěny společně a nacházet se ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

    > [!NOTE]  
    > Další metody vytváření clusterů a principy vlastnosti používaných v tomto kurzu najdete v části [Vytváření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       

## <a name="run-apache-hive-queries"></a>Spustit dotazy Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto kurzu použijete zobrazení Ambari Hive z portálu. Další metody pro odesílání úloh Hive najdete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na adresu **https://&lt;název_clusteru>.azurehdinsight.net**, kde &lt;název_clusteru> je název clusteru vytvořeného v předchozí části.

    ![Počáteční řídicí panel clusteru HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Počáteční řídicí panel clusteru HDInsight Linux")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:
   
    ![Výběr zobrazení Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Nabídka zobrazení Hive služby HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:
   
        SHOW TABLES;

    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor dotazů zobrazení Hive služby HDInsight")     

5. Vyberte **Provést**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 
   
    Po dokončení dotazu se na kartě **DOTAZ** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.
   
    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor dotazů zobrazení Hive služby HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:
   
        SELECT * FROM hivesampletable;
   
7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložení výsledku dotazu Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Uložení výsledku dotazu Hive")

Po dokončení úlohy Hive můžete [Exportovat výsledky do databáze Azure SQL nebo databáze systému SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [zobrazit výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o používání Hive v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. 

> [!NOTE]  
> Pokud *rovnou* pokračujete k dalšímu kurzu, ve kterém se dozvíte, jak spouštět operace ETL s využitím Hadoopu ve službě HDInsight, můžete cluster nechat spuštěný. To proto, že v kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale nebudete hned pokračovat dalším kurzem, musíte teď cluster odstranit.
> 
>  

**Postup odstranění clusteru a/nebo výchozího účtu úložiště**

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Odstranění clusteru HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Odstranění clusteru HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Resource Manageru a jak provádět základní dotazy Hive. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí Apache Hive v HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Pokud chcete začít pracovat s vlastními daty a potřebujete další informace o ukládání dat službou HDInsight nebo o tom, jak data do této služby nahrát, přečtěte si následující články:

* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak vytvořit HDInsight cluster s Data Lake Storage najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md).
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

Další informace o analýze dat pomocí HDInsight najdete v následujících článcích:

* Další informace o používání Hivu se službou HDInsight, včetně postupu provádění dotazů Hivu ze sady Visual Studio, naleznete v tématu [použití Apache Hivu se službou HDInsight](hdinsight-use-hive.md).
* Další informace o jazyce používaném k transformaci dat, Pig najdete [použití Apache Pig s HDInsight](hdinsight-use-pig.md).
* Další informace o MapReduce, způsobu psaní programů, které zpracovávají data v Hadoopu, najdete v tématu [Použití MapReduce se službou HDInsight](hdinsight-use-mapreduce.md).
* Další informace o použití nástrojů HDInsight pro Visual Studio k analýze dat na HDInsight najdete v části [Začněte používat nástroje Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md).


Pokud potřebujete další informace o vytváření a správě clusteru HDInsight, přečtěte si následující články:

* Další informace o správě clusteru HDInsight se systémem Linux najdete v tématu [HDInsight Správa clusterů pomocí nástroje Apache Ambari](../hdinsight-hadoop-manage-ambari.md).
* Další informace o možnostech, které můžete vybrat při vytváření clusteru služby HDInsight, najdete v tématu [Vytváření HDInsight na Linuxu pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


