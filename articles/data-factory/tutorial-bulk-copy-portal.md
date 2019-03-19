---
title: Hromadné kopírování dat pomocí Azure Data Factory | Dokumentace Microsoftu
description: Naučte se používat Azure Data Factory a aktivitu kopírování k hromadnému kopírování dat ze zdrojového úložiště dat do cílového úložiště dat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 16741461df2431cbf4433899dd375741e944ce0f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112564"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Hromadné kopírování několika tabulek pomocí Azure Data Factory
Tento kurz představuje **kopírování několika tabulek z Azure SQL Database do služby Azure SQL Data Warehouse**. Stejný vzor můžete využít i u dalších scénářů kopírování. Například při kopírování tabulek z SQL Serveru/Oraclu do služby Azure SQL Database/Data Warehouse/Azure Blob nebo při kopírování různých cest ze služby Blob do tabulek Azure SQL Database.

> [!NOTE]
> - Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

Tento kurz zahrnuje následující základní kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure SQL Database, Azure SQL Data Warehouse a Azure Storage
> * Vytvoření datových sad Azure SQL Database a Azure SQL Data Warehouse
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá Azure Portal. Další informace o vytvoření datové továrny pomocí jiných nástrojů nebo sad SDK najdete v tématu [Šablony Rychlý start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
V tomto scénáři máte několik tabulek ve službě Azure SQL Database, které chcete zkopírovat do služby SQL Data Warehouse. Tady je logická posloupnost kroků tohoto pracovního postupu, které se provádějí v kanálech:

![Pracovní postup](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* První kanál vyhledá seznam tabulek, které je potřeba zkopírovat do úložišť dat jímky.  Další možností je udržovat tabulku metadat se seznamem všech tabulek, které je potřeba zkopírovat do úložišť dat jímky. Kanál potom aktivuje jiný kanál, který postupně prochází všechny tabulky v databázi a provádí operaci kopírování dat.
* Tento druhý kanál provádí vlastní kopírování. Jako parametr používá seznam tabulek. Každá tabulka v tomto seznamu se zkopíruje z Azure SQL Database do příslušné tabulky ve službě SQL Data Warehouse pomocí [fázovaného kopírování prostřednictvím Blob Storage a PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) pro zajištění nejlepšího výkonu. V tomto příkladu první kanál předá seznam tabulek jako hodnotu parametru. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Účet služby Azure Storage**. Účet Azure Storage se v operaci hromadného kopírování používá jako pracovní úložiště objektů blob. 
* **Azure SQL Database**. Tato databáze obsahuje zdrojová data. 
* **Azure SQL Data Warehouse**. Tento datový sklad obsahuje data zkopírovaná z SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Příprava SQL Database a služby SQL Data Warehouse

**Příprava zdrojové databáze Azure SQL Database**:

Podle postupu v článku [Vytvoření databáze SQL Azure](../sql-database/sql-database-get-started-portal.md) vytvořte Azure SQL Database s ukázkovými daty Adventure Works LT. V tomto kurzu se všechny tabulky z této ukázkové databáze zkopírují do datového skladu SQL.

**Příprava jímky Azure SQL Data Warehouse**:

1. Pokud Azure SQL Data Warehouse nemáte, přečtěte si článek věnovaný [vytvoření služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md), kde najdete kroky pro její vytvoření.

1. V SQL Data Warehouse vytvořte odpovídající schémata tabulek. K **migraci schémat** z Azure SQL Database do Azure SQL Data Warehouse můžete využít [nástroj pro migraci](https://www.microsoft.com/download/details.aspx?id=49100) . K migraci/kopírování dat v pozdějším kroku můžete použít Azure Data Factory.

## <a name="azure-services-to-access-sql-server"></a>Služby Azure pro přístup k SQL serveru

Pro SQL Database i SQL Data Warehouse povolte službám Azure přístup k SQL serveru. Ujistěte se, že nastavení **Povolit přístup ke službám Azure** je pro SQL server Azure **zapnuté**. Toto nastavení umožňuje službě Data Factory načítat data z Azure SQL Database a zapisovat data do Azure SQL Data Warehouse. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Klikněte na **Další služby** na levé straně a potom klikněte na **Servery SQL**.
1. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
1. Na stránce **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialBulkCopyDF**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialBulkCopyDF). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **verzi** vyberte **V2**.
1. Vyberte **umístění** pro datovou továrnu. Seznam oblastí Azure, ve kterých je momentálně dostupná Data Factory, vyberte oblasti, které vás zajímají na následující stránce a potom rozbalte **Analytics** najít **služby Data Factory**: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
1. Zaškrtněte **Připnout na řídicí panel**.     
1. Klikněte na možnost **Vytvořit**.
1. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

     ![nasazování dlaždice datové továrny](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
1. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
     ![Domovská stránka datové továrny](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.
1. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**, jak je znázorněno na následujícím obrázku:  

     ![Stránka Začínáme](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvoříte propojené služby, které propojí vaše úložiště dat a výpočetní prostředí s datovou továrnou. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti dat za běhu. 

V tomto kurzu propojíte se svou datovou továrnou úložiště dat Azure SQL Database, Azure SQL Data Warehouse a Azure Blob Storage. Azure SQL Database je zdrojové úložiště dat. Azure SQL Data Warehouse je cílové úložiště dat jímky. Azure Blob Storage připraví data před nahráním do služby SQL Data Warehouse pomocí PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database pro zdroj
V tomto kroku vytvoříte propojenou službu, která propojí vaši databázi SQL Azure s datovou továrnou. 

1. Klikněte na **Připojení** v dolní části okna a pak klikněte na **+ Nové** na panelu nástrojů. 

    ![Tlačítko Nová propojená služba](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 

    ![Výběr služby Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
1. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**. 
    1. Jako **Název serveru** vyberte váš server SQL Azure.
    1. Jako **Název databáze** vyberte vaši databázi SQL Azure. 
    1. Zadejte **jméno uživatele** pro připojení k databázi SQL Azure. 
    1. Zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat připojení k databázi SQL Azure s použitím zadaných informací, klikněte na **Test připojení**.
    1. Klikněte na **Uložit**.

        ![Nastavení služby Azure SQL Database](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Vytvoření propojené služby Azure SQL Data Warehouse pro jímku

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure SQL Data Warehouse** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureSqlDWLinkedService**. 
    1. Jako **Název serveru** vyberte váš server SQL Azure.
    1. Jako **Název databáze** vyberte vaši databázi SQL Azure. 
    1. Zadejte **jméno uživatele** pro připojení k databázi SQL Azure. 
    1. Zadejte **heslo** pro tohoto uživatele. 
    1. Pokud chcete otestovat připojení k databázi SQL Azure s použitím zadaných informací, klikněte na **Test připojení**.
    1. Klikněte na **Uložit**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Vytvoření pracovní propojené služby Azure Storage
V tomto kurzu použijete Azure Blob Storage jako dočasné pracovní oblast, abyste zajistili lepší výkon kopírování pro funkci PolyBase.

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureStorageLinkedService**. 
    1. Jako **Název účtu úložiště** vyberte svůj **účet služby Azure Storage**.
    1. Klikněte na **Uložit**.


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kurzu vytvoříte zdrojovou datovou sadu a datovou sadu jímky, které určují umístění pro uložení dat. 

Vstupní datová sada **AzureSqlDatabaseDataset** odkazuje na službu **AzureSqlDatabaseLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k databázi. Datová sada určuje název databáze a tabulky obsahující zdrojová data. 

Výstupní datová sada **AzureSqlDWDataset** odkazuje na službu **AzureSqlDWLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k datovému skladu. Datová sada určuje databázi a tabulku, do kterých se data zkopírují. 

V tomto kurzu nejsou zdrojová a cílová tabulka SQL pevně zakódované v definicích datových sad. Místo toho aktivita ForEach předává název tabulky do aktivity kopírování za běhu. 

### <a name="create-a-dataset-for-source-sql-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Database

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Datová sada**. 

    ![Nabídka Nová datová sada](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database** a klikněte na **Dokončit**. Měla by se zobrazit nová karta s názvem **AzureSqlTable1**. 
    
    ![Výběr služby Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
1. V dolní části okna Vlastnosti jako **Název** zadejte **AzureSqlDatabaseDataset**.

1. Přepněte na kartu **Připojení** a proveďte následující kroky: 

   1. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.
   1. Jako **Tabulka** vyberte jakoukoli tabulku. Tato tabulka je fiktivní. Při vytváření kanálu zadáte dotaz na zdrojovou datovou sadu. Tento dotaz se použije k extrakci dat z databáze SQL Azure. Případně můžete kliknout na zaškrtávací políčko **Upravit** a jako název tabulky zadat **dummyName**. 

      ![Stránka Připojení ke zdrojové datové sadě](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Vytvoření datové sady pro SQL Data Warehouse jímky

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Datová sada**. 
1. V okně **Nová datová sada** vyberte **Azure SQL Data Warehouse** a klikněte na **Dokončit**. Měla by se zobrazit nová karta s názvem **AzureSqlDWTable1**. 
1. V dolní části okna Vlastnosti jako **Název** zadejte **AzureSqlDWDataset**.
1. Přepněte na kartu **Parametry**, klikněte na **+Nové** a jako název parametru zadejte **DWTableName**. Pokud zkopírujete/vložíte tento název ze stránky, ujistěte se, že není **znak koncové mezery** na konci **DWTableName**. 

    ![Stránka Připojení ke zdrojové datové sadě](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter.png)

1. Přepněte na kartu **Připojení**. 

    a. Jako **Propojená služba** vyberte **AzureSqlDatabaseLinkedService**.

    b. U **Tabulky** zaškrtněte možnost **Upravit**, klikněte do pole pro zadání názvu tabulky a pak pod ním klikněte na odkaz **Přidat dynamický obsah**. 
    
    ![Název parametru](./media/tutorial-bulk-copy-portal/table-name-parameter.png)

    c. Na stránce **Přidat dynamický obsah** klikněte na **DWTAbleName** pod **Parametry**. Tím se automaticky vyplní horní textové pole výrazem `@dataset().DWTableName`.Pak klikněte na **Dokončit**. Vlastnost **tableName** datové sady je nastavená na hodnotu předávanou jako argument parametru **DWTableName**. Aktivita ForEach iteruje seznam tabulek a jednu po druhé je předává aktivitě kopírování. 

    ![Tvůrce parametru datové sady](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)

## <a name="create-pipelines"></a>Vytvoření kanálů
V tomto kurzu vytvoříte dva kanály: **IterateAndCopySQLTables** a **GetTableListAndTriggerCopyData**. 

Kanál **GetTableListAndTriggerCopyData** provádí dva kroky:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál **IterateAndCopySQLTables**, který provede vlastní kopírování dat.

Kanál **GetTableListAndTriggerCopyData** přijímá jako parametr seznam tabulek. Data ze všech tabulek v tomto seznamu se zkopírují z Azure SQL Database do služby SQL Data Warehouse pomocí fázovaného kopírování a PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Vytvoření kanálu IterateAndCopySQLTables

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

    ![Nabídka Nový kanál](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na kartě **Obecné** zadejte název **IterateAndCopySQLTables**. 

1. Přepněte na kartu **Parametry** a proveďte následující akce: 

    1. Klikněte na **+ Nový**. 
    1. Jano **název parametru** zadejte **tableList**.
    1. Jako **Typ** vyberte **Pole**.

        ![Parametr kanálu](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
1. Na panelu nástrojů **Aktivity** rozbalte **Iterace a podmínky** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. 

    a. Na kartě **Obecné** dole zadejte **IterateSQLTables** jako **Název**. 

    b. Přepněte na kartu **Nastavení**, klikněte na pole pro **Položky**, pak pod ním klikněte na odkaz **Přidat dynamický obsah**. 

    ![Nastavení aktivity ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)

    c. Na stránce **Přidat dynamický obsah** sbalte sekce Systémové proměnné a Funkce a klikněte na **tableList** pod **Parametry**. Tím se automaticky vyplní horní textové pole výrazem `@pipeline().parameter.tableList`. Pak klikněte na **Dokončit**. 

    ![Tvůrce parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Přepněte na kartu **Aktivity** a kliknutím na **Přidat aktivitu** přidejte podřízenou aktivitu k aktivitě **ForEach**.

1. Na panelu nástrojů **Aktivity** rozbalte **Tok dat** a přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu. Všimněte si nabídky navigace s popisem cesty v horní části. IterateAndCopySQLTable je název kanálu a IterateSQLTables je název aktivity ForEach. Návrhář je v oboru aktivity. Pokud chcete z editoru aktivity ForEach přepnout zpět na editor kanálu, klikněte na odkaz v nabídce navigace s popisem cesty. 

    ![Kopírování v aktivitě ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
1. Přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Jako **Uživatelský dotaz** vyberte možnost **Dotaz**. 
    1. Klikněte na vstupní pole **Dotaz** -> vyberte dole **Přidat dynamický obsah** -> zadejte následující výraz jako **Dotaz** -> vyberte **Dokončit**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Nastavení zdroje kopírování](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
1. Přepněte na kartu **Jímka** a proveďte následující kroky: 

    1. Jako **Datová sada jímky** vyberte **AzureSqlDWDataset**.
    1. Klikněte na vstupní pole pro HODNOTU parametru DWTableName -> vyberte dole **Přidat dynamický obsah**, zadejte výraz `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` jako skript -> vyberte **Dokončit**.
    1. Rozbalte **Nastavení Polybase** a vyberte **Povolit Polybase**. 
    1. Vymažte možnost **Použít výchozí typ**. 
    1. Klikněte na vstupní pole **Skript před kopírováním**, vyberte dole **Přidat dynamický obsah**, zadejte následující výraz jako skript a vyberte **Dokončit**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Nastavení jímky kopírování](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. V části **Zapnout pracovní režim** vyberte hodnotu **True**.
    1. Jako **Propojená služba účtu úložiště** zadejte **AzureStorageLinkedService**.

        ![Zapnout pracovní režim](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)

1. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na horním panelu nástrojů kanálu. Ověřte, že se nezobrazí žádná chyba ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Vytvoření kanálu GetTableListAndTriggerCopyData

Tento kanál provádí dva kroky:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál IterateAndCopySQLTables, který provede vlastní kopírování dat.

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

    ![Nabídka Nový kanál](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. V okně Vlastnosti změňte název kanálu na **GetTableListAndTriggerCopyData**. 

1. Na panelu nástrojů **Aktivity** rozbalte nabídku **Obecné**, přetáhněte aktivitu **Vyhledávání** na plochu návrháře kanálu a pak postupujte takto:

    1. Jako **Název** zadejte **LookupTableList**. 
    1. Jako **Popis** zadejte **Načtení seznamu tabulek z databáze SQL Azure**.

        ![Aktivita vyhledávání – stránka Obecné](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
1. Přepněte na stránku **Nastavení** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Jako **Použít dotaz** vyberte **Dotaz**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Zrušte zaškrtnutí pole **Pouze první řádek**.

        ![Aktivita vyhledávání – stránka Nastavení](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Přetáhněte z panelu nástrojů Aktivity na plochu návrháře kanálu aktivitu **Spuštění kanálu** a nastavte její název na **TriggerCopy**.

    ![Aktivita spuštění kanálu – stránka Obecné](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
1. Přepněte na stránku **Nastavení** a proveďte následující kroky: 

    1. Jako **Vyvolaný kanál** vyberte **IterateAndCopySQLTables**. 
    1. Rozbalte část **Upřesnit**. 
    1. Klikněte na **+ Nový** v části **Parametry**. 
    1. Jako **název** parametru zadejte **tableList**.
    1. Klikněte na vstupní pole HODNOTY -> vyberte dole **Přidat dynamický obsah** -> zadejte `@activity('LookupTableList').output.value` jako hodnotu názvu tabulky -> vyberte **Dokončit**. Seznam výsledků z aktivity vyhledávání nastavujete jako vstup druhého kanálu. Seznam výsledků obsahuje seznam tabulek, jejichž data se musí zkopírovat do cíle. 

        ![Aktivita spuštění kanálu – stránka Nastavení](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. **Propojte** aktivitu **Vyhledávání** s aktivitou **Spuštění kanálu** přetažením **zeleného pole** připojeného k aktivitě vyhledávání nalevo od aktivity spuštění kanálu.

    ![Projení aktivit vyhledávání a spuštění kanálu](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Pokud chcete kanál ověřit, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

1. Pokud chcete publikovat entity (datové sady, kanály atd.) do služby Data Factory, nahoře v okně klikněte na **Publikovat vše**. Počkejte na úspěšné dokončení publikování. 

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

Přejděte do kanálu **GetTableListAndTriggerCopyData**, klikněte na **Aktivační událost** a klikněte na **Aktivovat**. 

![Aktivovat](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přepněte na kartu **Monitorování**. Klikejte na **Aktualizovat**, dokud se nezobrazí spuštění obou kanálů ve vašem řešení. Pokračujte v aktualizacích seznamu, dokud se nezobrazí stav **Úspěch**. 

    ![Spuštění kanálu](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
1. Pokud chcete zobrazit spuštění aktivit související s kanálem GetTableListAndTriggerCopyData, klikněte na první odkaz ve sloupci Akce pro příslušný kanál. Pro toto spuštění kanálu by se měla zobrazit dvě spuštění aktivit. 

    ![Spuštění aktivit](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
1. Pokud chcete zobrazit výstup aktivity **Vyhledávání**, klikněte na odkaz ve sloupci **Výstup** pro příslušnou aktivitu. Okno **Výstup** můžete maximalizovat a obnovit. Po kontrole kliknutím na **X** zavřete okno **Výstup**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Pokud chcete přepnout zpět na zobrazení **Spuštění kanálu**, klikněte na odkaz **Kanály** v horní části. Klikněte na odkaz **Zobrazit spuštění aktivit** (první odkaz ve sloupci **Akce**) pro kanál **IterateAndCopySQLTables**. Zobrazený výstup by měl jak je znázorněno na následujícím obrázku: Všimněte si, že existuje **kopírování** spuštění aktivit pro každou tabulku v **vyhledávání** výstup aktivity. 

    ![Spuštění aktivit](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
1. Ověřte, že se data zkopírovala do cílové služby SQL Data Warehouse, kterou jste použili v tomto kurzu. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure SQL Database, Azure SQL Data Warehouse a Azure Storage
> * Vytvoření datových sad Azure SQL Database a Azure SQL Data Warehouse
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o přírůstkovém kopírování ze zdroje do cíle, přejděte k následujícímu kurzu:
> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat](tutorial-incremental-copy-portal.md)
