---
title: Hromadné kopírování dat pomocí Azure Portal
description: Naučte se používat Azure Data Factory a aktivitu kopírování k hromadnému kopírování dat ze zdrojového úložiště dat do cílového úložiště dat.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/08/2020
ms.openlocfilehash: 4e39d4e106a399f0105ee4ec3f3606354f113165
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661064"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Hromadné kopírování více tabulek pomocí Azure Data Factory v Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento kurz ukazuje **kopírování několika tabulek z Azure SQL Database do Azure synapse Analytics (dřív SQL DW)**. Stejný vzor můžete využít i u dalších scénářů kopírování. Například kopírujete tabulky z SQL Server/Oracle do Azure SQL Database/Azure synapse Analytics (dříve SQL DW)/Azure objekt BLOB a kopírujete různé cesty z objektu blob do tabulek Azure SQL Database.

> [!NOTE]
> - Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

Tento kurz zahrnuje následující základní kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření Azure SQL Database, Azure synapse Analytics (dříve SQL DW) a Azure Storage propojených služeb.
> * Vytváření Azure SQL Database a datových sad Azure synapse Analytics (dříve SQL DW)
> * Vytvořte kanál pro vyhledání tabulek ke zkopírování a dalšího kanálu k provedení skutečné operace kopírování. 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá Azure Portal. Další informace o vytvoření datové továrny pomocí jiných nástrojů nebo sad SDK najdete v tématu [Šablony Rychlý start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
V tomto scénáři máte v Azure SQL Database několik tabulek, které chcete zkopírovat do Azure synapse Analytics (dřív SQL DW). Tady je logická posloupnost kroků tohoto pracovního postupu, které se provádějí v kanálech:

![Pracovní postup](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* První kanál vyhledá seznam tabulek, které je potřeba zkopírovat do úložišť dat jímky.  Další možností je udržovat tabulku metadat se seznamem všech tabulek, které je potřeba zkopírovat do úložišť dat jímky. Kanál potom aktivuje jiný kanál, který postupně prochází všechny tabulky v databázi a provádí operaci kopírování dat.
* Tento druhý kanál provádí vlastní kopírování. Jako parametr používá seznam tabulek. Pro každou tabulku v seznamu zkopírujte příslušnou tabulku v Azure SQL Database do odpovídající tabulky ve službě Azure synapse Analytics (dřív SQL DW) pomocí [připravené kopie prostřednictvím služby Blob Storage a základu](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) pro nejlepší výkon. V tomto příkladu první kanál předá seznam tabulek jako hodnotu parametru. 

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete.

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Účet Azure Storage se v operaci hromadného kopírování používá jako pracovní úložiště objektů blob. 
* **Azure SQL Database**. Tato databáze obsahuje zdrojová data. 
* **Azure synapse Analytics (dřív SQL DW)**. Tento datový sklad obsahuje data zkopírovaná z SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Příprava SQL Database a Azure synapse Analytics (dřív SQL DW)

**Příprava zdrojové databáze Azure SQL Database**:

Podle postupu v článku [Vytvoření databáze Azure SQL](../azure-sql/database/single-database-create-quickstart.md) vytvořte Azure SQL Database s ukázkovými daty Adventure Works LT. V tomto kurzu se zkopírují všechny tabulky z této ukázkové databáze do Azure synapse Analytics (dřív SQL DW).

**Příprava služby synapse Analytics pro službu Azure Data Sink (dříve SQL DW)**:

1. Pokud nemáte Azure synapse Analytics (dřív SQL DW), přečtěte si článek [vytvoření SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) , kde najdete kroky pro jeho vytvoření.

1. Vytváření odpovídajících schémat tabulek v Azure synapse Analytics (dřív SQL DW) K migraci/kopírování dat v pozdějším kroku můžete použít Azure Data Factory.

## <a name="azure-services-to-access-sql-server"></a>Služby Azure pro přístup k SQL serveru

Pro SQL Database a Azure synapse Analytics (dříve SQL DW) Umožněte službám Azure přístup k SQL serveru. Zajistěte, aby **byla pro váš** Server zapnutá možnost **Povolit službám a prostředkům Azure přístup k tomuto serveru** . Toto nastavení umožňuje službě Data Factory číst data z vašeho Azure SQL Database a zapisovat data do Azure synapse Analytics (dřív SQL DW). 

Pokud chcete toto nastavení ověřit a zapnout, přejděte na server > zabezpečení > brány firewall a virtuální sítě > nastavte **Povolit službám a prostředkům Azure přístup k tomuto serveru** na **zapnuto**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejít na [Azure Portal](https://portal.azure.com). 
1. Na levé straně nabídky Azure Portal vyberte **vytvořit data Factory prostředků**  >  **Analytics**  >  **Data Factory**. 
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **název**zadejte **ADFTutorialBulkCopyDF** . 
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialBulkCopyDF). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit novou**a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
1. Jako **verzi** vyberte **V2**.
1. Vyberte **umístění** pro objekt pro vytváření dat. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
1. Klikněte na **Vytvořit**.
1. Po dokončení vytváření vyberte **Přejít k prostředku** a přejděte na stránku **Data Factory** . 
   
1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.
1. Na stránce **Začínáme** přepněte na levém panelu na kartu **Autor** , jak je znázorněno na následujícím obrázku:

     ![Stránka Začínáme](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvoříte propojené služby, které propojí vaše úložiště dat a výpočetní prostředí s datovou továrnou. Propojená služba obsahuje informace o připojení, které služba Data Factory používá pro připojení k úložišti dat za běhu. 

V tomto kurzu propojíte Azure SQL Database, Azure synapse Analytics (dříve SQL DW) a Azure Blob Storage úložiště dat do vaší datové továrny. Azure SQL Database je zdrojové úložiště dat. Azure synapse Analytics (dřív SQL DW) je úložiště dat jímky a cíle. Azure Blob Storage slouží k přípravě dat před načtením dat do služby Azure synapse Analytics (dříve SQL DW) pomocí základu. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database pro zdroj
V tomto kroku vytvoříte propojenou službu, která propojí vaši databázi Azure SQL s datovou továrnou. 

1. V levém podokně otevřete [kartu spravovat](https://docs.microsoft.com/azure/data-factory/author-management-hub) .

1. Na stránce propojené služby vyberte **+ Nová** a vytvořte novou propojenou službu.

   ![Nová propojená služba](./media/doc-common-process/new-linked-service.png)
1. V okně **Nová propojená služba** vyberte **Azure SQL Database** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure SQL Database)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureSqlDatabaseLinkedService**.

    b. Vyberte server pro **název serveru**
    
    c. Jako **Název databáze** vyberte vaši databázi Azure SQL. 
    
    d. Zadejte **jméno uživatele** pro připojení k databázi Azure SQL. 
    
    e. Zadejte **heslo** pro tohoto uživatele. 

    f. Pokud chcete otestovat připojení k databázi Azure SQL s použitím zadaných informací, klikněte na **Test připojení**.
  
    například Kliknutím na **vytvořit** uložte propojenou službu.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Vytvoření propojené služby Azure synapse Analytics (dříve SQL DW) jímky

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure synapse Analytics (dřív SQL DW)** a klikněte na **pokračovat**. 
1. V okně **Nová propojená služba (dříve SQL DW))** proveďte následující kroky: 
   
    a. Jako **Název** zadejte **AzureSqlDWLinkedService**.
     
    b. Vyberte server pro **název serveru**
     
    c. Jako **Název databáze** vyberte vaši databázi Azure SQL. 
     
    d. Zadejte **uživatelské jméno** pro připojení ke službě Azure SQL Database. 
     
    e. Zadejte **heslo** pro uživatele. 
     
    f. Pokud chcete otestovat připojení k databázi Azure SQL s použitím zadaných informací, klikněte na **Test připojení**.
     
    například Klikněte na **Vytvořit**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Vytvoření pracovní propojené služby Azure Storage
V tomto kurzu použijete Azure Blob Storage jako dočasné pracovní oblast, abyste zajistili lepší výkon kopírování pro funkci PolyBase.

1. Na kartě **Připojení** znovu klikněte na **+ Nové** na panelu nástrojů. 
1. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 
1. V okně **Nová propojená služba (Azure Blob Storage)** proveďte následující kroky: 

    a. Jako **Název** zadejte **AzureStorageLinkedService**.                                                 
    b. Jako **Název účtu úložiště** vyberte svůj **účet služby Azure Storage**.
    
    c. Klikněte na **Vytvořit**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kurzu vytvoříte zdrojovou datovou sadu a datovou sadu jímky, které určují umístění pro uložení dat. 

Vstupní datová sada **AzureSqlDatabaseDataset** odkazuje na službu **AzureSqlDatabaseLinkedService**. Propojená služba určuje připojovací řetězec pro připojení k databázi. Datová sada určuje název databáze a tabulky obsahující zdrojová data. 

Výstupní datová sada **AzureSqlDWDataset** odkazuje na službu **AzureSqlDWLinkedService**. Propojená služba Určuje připojovací řetězec pro připojení ke službě Azure synapse Analytics (dříve SQL DW). Datová sada určuje databázi a tabulku, do kterých se data zkopírují. 

V tomto kurzu nejsou zdrojová a cílová tabulka SQL pevně zakódované v definicích datových sad. Místo toho aktivita ForEach předává název tabulky do aktivity kopírování za běhu. 

### <a name="create-a-dataset-for-source-sql-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Database

1. Klikněte na **+ (plus)** v levém podokně a pak klikněte na **datová sada**. 

    ![Nabídka Nová datová sada](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. V okně **Nová datová sada** vyberte **Azure SQL Database**a potom klikněte na **pokračovat**. 
    
1. V okně **nastavit vlastnosti** v části **název**zadejte **AzureSqlDatabaseDataset**. V části **propojená služba**vyberte **AzureSqlDatabaseLinkedService**. Pak klikněte na **OK**.

1. Přepněte na kartu **připojení** , vyberte libovolnou tabulku pro **tabulku**. Tato tabulka je fiktivní. Při vytváření kanálu zadáte dotaz na zdrojovou datovou sadu. Tento dotaz se použije k extrakci dat z databáze Azure SQL. Případně můžete kliknout na tlačítko **Upravit** a jako název tabulky zadat **dbo.** název. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Vytvoření datové sady pro jímku Azure synapse Analytics (dřív SQL DW)

1. Klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Datová sada**. 
1. V okně **Nová datová sada** vyberte **Azure synapse Analytics (dřív SQL DW)** a pak klikněte na **pokračovat**.
1. V okně **nastavit vlastnosti** v části **název**zadejte **AzureSqlDWDataset**. V části **propojená služba**vyberte **AzureSqlDWLinkedService**. Pak klikněte na **OK**.
1. Přepněte na kartu **Parametry**, klikněte na **+Nové** a jako název parametru zadejte **DWTableName**. Pokud zkopírujete nebo vložíte tento název ze stránky, zajistěte, aby na konci **DWTableName**nebyl žádný **znak místa** na konci.
1. Přepněte na kartu **Připojení**. 

    a. V části **tabulka**ověřte možnost **Upravit** . Do vstupního pole název první tabulky zadejte **dbo** . A potom vyberte druhý vstupní pole a klikněte na odkaz **Přidat dynamický obsah** níže. 

    ![Navázání spojení datové sady](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na stránce **Přidat dynamický obsah** klikněte v části **parametry**na **DWTAbleName** , který automaticky vyplní textové pole výrazu Top `@dataset().DWTableName` a pak klikněte na **Dokončit**. Vlastnost **tableName** datové sady je nastavená na hodnotu předávanou jako argument parametru **DWTableName**. Aktivita ForEach iteruje seznam tabulek a jednu po druhé je předává aktivitě kopírování. 

    ![Tvůrce parametru datové sady](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Vytvoření kanálů
V tomto kurzy vytvoříte dva kanály: **IterateAndCopySQLTables** a **GetTableListAndTriggerCopyData**. 

Kanál **GetTableListAndTriggerCopyData** provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál **IterateAndCopySQLTables**, který provede vlastní kopírování dat.

Kanál **IterateAndCopySQLTables** jako parametr používá seznam tabulek. Pro každou tabulku v seznamu zkopíruje data z tabulky v Azure SQL Database do služby Azure synapse Analytics (dříve SQL DW) pomocí připravené kopie a základu.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Vytvoření kanálu IterateAndCopySQLTables

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.

    ![Nabídka Nový kanál](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. Na panelu Obecné v části **vlastnosti**zadejte **IterateAndCopySQLTables** pro **název**. Pak panel sbalíte kliknutím na ikonu vlastnosti v pravém horním rohu.

1. Přepněte na kartu **Parametry** a proveďte následující akce: 

    a. Klikněte na **+ Nový**. 
    
    b. Jako **název**parametru zadejte **tableList** .
    
    c. Jako **Typ** vyberte **Pole**.

1. Na panelu nástrojů **Aktivity** rozbalte **Iterace a podmínky** a přetáhněte aktivitu **ForEach** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. 

    a. Na kartě **Obecné** dole zadejte **IterateSQLTables** jako **Název**. 

    b. Přepněte na kartu **Nastavení** , klikněte na vstupní pole pro **položky**a pak klikněte na odkaz **Přidat dynamický obsah** níže. 

    c. Na stránce **Přidat dynamický obsah** sbalte oddíly **systémové proměnné** a **funkce** klikněte na **tableList** v části **parametry**. tím se automaticky naplní textové pole horního výrazu `@pipeline().parameter.tableList` . Klikněte na **Dokončit**. 

    ![Tvůrce parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Přepněte na kartu **aktivity** , kliknutím na **ikonu tužky** přidejte podřízenou aktivitu do aktivity **foreach** .
    ![Tvůrce aktivit foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Na panelu nástrojů **aktivity** rozbalte **přesunout & přenos**a přetáhněte aktivitu **Kopírovat data** na plochu návrháře kanálu. Všimněte si nabídky navigace s popisem cesty v horní části. **IterateAndCopySQLTable** je název kanálu a **IterateSQLTables** je název aktivity ForEach. Návrhář je v oboru aktivity. Chcete-li přepnout zpět na Editor kanálů z editoru ForEach, můžete kliknout na odkaz v nabídce popis cesty. 

    ![Kopírování v aktivitě ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Přepněte na kartu **Zdroj** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte možnost **dotazu** pro **použití dotazu**. 
    1. Klikněte na vstupní pole **Dotaz** -> vyberte dole **Přidat dynamický obsah** -> zadejte následující výraz jako **Dotaz** -> vyberte **Dokončit**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Přepněte na kartu **Jímka** a proveďte následující kroky: 

    1. Jako **Datová sada jímky** vyberte **AzureSqlDWDataset**.
    1. Klikněte na vstupní pole pro hodnotu parametru DWTableName-> vyberte níže **Přidat dynamický obsah** a `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` jako skript zadejte výraz-> vyberte **Dokončit**.
    1. V případě metody Copy vyberte **základnu**. 
    1. Zrušte zaškrtnutí možnosti **použít výchozí typ** . 
    1. Klikněte na vstupní pole **Skript před kopírováním**, vyberte dole **Přidat dynamický obsah**, zadejte následující výraz jako skript a vyberte **Dokončit**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Nastavení jímky kopírování](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Přepněte na kartu **Nastavení** a proveďte následující kroky: 

    1. Zaškrtněte políčko **Povolit přípravu**.
    1. Jako **Propojená služba účtu úložiště** zadejte **AzureStorageLinkedService**.

1. Pokud chcete ověřit nastavení kanálu, klikněte na **Ověřit** na horním panelu nástrojů kanálu. Ujistěte se, že se nevyskytla žádná chyba ověřování. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Vytvoření kanálu GetTableListAndTriggerCopyData

Tento kanál provádí dvě akce:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál IterateAndCopySQLTables, který provede vlastní kopírování dat.

1. V levém podokně klikněte na symbol **+ (plus)** a pak klikněte na **Kanál**.
1. Na kartě **Obecné** změňte název kanálu na **GetTableListAndTriggerCopyData**. 

1. Na panelu nástrojů **aktivity** rozbalte **Obecné**a přetáhněte aktivitu **vyhledávání** na plochu návrháře kanálu a proveďte následující kroky:

    1. Jako **Název** zadejte **LookupTableList**. 
    1. Jako **Popis** zadejte **Načtení seznamu tabulek z databáze Azure SQL**.

1. Přepněte na kartu **Nastavení** a proveďte následující kroky:

    1. Jako **Zdrojová datová sada** vyberte **AzureSqlDatabaseDataset**. 
    1. Vyberte **dotaz** pro **použití dotazu**. 
    1. Jako **Dotaz** zadejte následující příkaz jazyka SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Zrušte zaškrtnutí pole **Pouze první řádek**.

        ![Aktivita vyhledávání – stránka Nastavení](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Přetáhněte aktivitu **Spustit kanál** z panelu nástrojů aktivity na plochu návrháře kanálu a nastavte název na **TriggerCopy**.

1. Pokud chcete aktivitu **vyhledávání** **připojit** k aktivitě **Spustit kanál** , přetáhněte **zelené pole** připojené k aktivitě vyhledávání nalevo od aktivity spustit kanál.

    ![Projení aktivit vyhledávání a spuštění kanálu](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Přepněte na kartu **Nastavení** aktivity **Spustit kanál** a proveďte následující kroky: 

    1. Jako **Vyvolaný kanál** vyberte **IterateAndCopySQLTables**. 
    1. Rozbalte část **Upřesnit** a zrušte zaškrtnutí políčka **čekání na dokončení**.
    1. Klikněte na **+ Nový** v části **Parametry**. 
    1. Jako **název**parametru zadejte **tableList** .
    1. Klikněte na vstupní pole HODNOTY -> vyberte dole **Přidat dynamický obsah** -> zadejte `@activity('LookupTableList').output.value` jako hodnotu názvu tabulky -> vyberte **Dokončit**. Seznam výsledků se nastavuje z aktivity vyhledávání jako vstup druhého kanálu. Seznam výsledků obsahuje seznam tabulek, jejichž data se musí zkopírovat do cíle. 

        ![Aktivita spuštění kanálu – stránka Nastavení](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Pokud chcete kanál ověřit, klikněte na **Ověřit** na panelu nástrojů. Ověřte, že se nezobrazí žádné chyby ověření. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

1. Pokud chcete publikovat entity (datové sady, kanály atd.) do služby Data Factory, klikněte na **publikovat vše** v horní části okna. Počkejte na úspěšné dokončení publikování. 

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

1. Přejděte na kanály **GetTableListAndTriggerCopyData**, klikněte na **Přidat aktivační událost** na horním panelu nástrojů kanálu a potom klikněte na **aktivovat hned**. 

1. Potvrďte spuštění na stránce **spuštění kanálu** a pak vyberte **Dokončit**.

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přepněte na kartu **monitorování** . klikněte na **aktualizovat** , dokud se nezobrazí spuštění obou kanálů ve vašem řešení. Pokračujte v aktualizacích seznamu, dokud se nezobrazí stav **Úspěch**. 

1. Pokud chcete zobrazit spuštění aktivit související s kanálem **GetTableListAndTriggerCopyData** , klikněte na odkaz název kanálu pro daný kanál. Pro toto spuštění kanálu by se měla zobrazit dvě spuštění aktivit. 
    ![Monitorování spuštění kanálu](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Výstup aktivity **vyhledávání** zobrazíte tak, že kliknete na odkaz **výstup** vedle aktivity pod sloupcem **název aktivity** . Okno **Výstup** můžete maximalizovat a obnovit. Po kontrole kliknutím na **X** zavřete okno **Výstup**.

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
1. Chcete-li přepnout zpět na zobrazení **spuštění kanálu** , klikněte na odkaz **všechny spuštěné kanály** v horní části nabídky s popisem cesty. Kliknutím na odkaz **IterateAndCopySQLTables** (pod sloupcem **název kanálu** ) zobrazte spuštění aktivit kanálu. Všimněte si, že pro každou tabulku ve výstupu **vyhledávací** aktivity je spuštěná jedna aktivita **kopírování** . 

1. Potvrďte, že se data zkopírovala do cílové služby Azure synapse Analytics (dříve SQL DW), kterou jste použili v tomto kurzu. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření Azure SQL Database, Azure synapse Analytics (dříve SQL DW) a Azure Storage propojených služeb.
> * Vytváření Azure SQL Database a datových sad Azure synapse Analytics (dříve SQL DW)
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o přírůstkovém kopírování ze zdroje do cíle, přejděte k následujícímu kurzu:
> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat](tutorial-incremental-copy-portal.md)
