---
title: Vytvoření datové továrny Azure pomocí uživatelského rozhraní služby Azure Data Factory | Dokumentace Microsoftu
description: Vytvořte datovou továrnu s kanálem, který kopíruje data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 5baa8c78ad581a00a3601706f31cf815359120c7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077050"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Rychlý start: Vytvoření datové továrny pomocí uživatelského rozhraní služby Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-portal.md)

Tento rychlý start popisuje vytvoření a monitorování datové továrny pomocí uživatelského rozhraní služby Azure Data Factory. Kanál, který vytvoříte v této datové továrně, *kopíruje* data z jedné složky do jiné složky v úložišti objektů blob v Azure. Kurz o tom, jak *transformovat* data pomocí Azure Data Factory, najdete v [tématu Kurz: Transformujte data pomocí Sparku](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Toto video vám pomůže seznámit se s uživatelským rozhraním služby Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na [Azure Portal](https://portal.azure.com). 
1. V levé nabídce vyberte **Vytvořit prostředek**, zvolte možnost **Analýza** a vyberte položku **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
 
   Název datové továrny Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (třeba na **&lt;váš_název_&gt;ADFTutorialDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/doc-common-process/name-not-available-error.png)
1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **Verzi** vyberte **V2**.
1. Jako **Umístění** vyberte umístění datové továrny.

   Seznam obsahuje jenom umístění podporovaná službou Data Factory, do kterých se budou ukládat vaše metadata Azure Data Factory. Přidružená úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředí (například Azure HDInsight), které Data Factory používá, mohou běžet v jiných oblastech.

1. Vyberte **Vytvořit**.

1. Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Vytvořit a monitorovat](./media/doc-common-process/data-factory-home-page.png)
1. Na stránce **Začínáme** přepněte na levém panelu na kartu **Vytvořit**. 

    ![Stránka Začínáme](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Vytvoření propojené služby
V tomto postupu vytvoříte propojenou službu, která propojí váš účet úložiště Azure s datovou továrnou. Tato propojená služba má informace o připojení, které služba Data Factory používá pro připojení za běhu.

1. Vyberte **připojení**a pak vyberte tlačítko **Nový** na panelu nástrojů (tlačítko**připojení** se nachází v dolní části levého sloupce v části **prostředky továrny**). 

1. Na stránce **Nová propojená služba** vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**. 

   ![Výběr dlaždice Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. Na stránce Nová propojená služba (Azure Blob Storage) proveďte následující kroky: 

   a. Jako **Název** zadejte **AzureStorageLinkedService**.

   b. Jako **Název účtu úložiště** vyberte název svého účtu úložiště Azure.

   c. Vyberte **Test připojení** a potvrďte, že se služba Data Factory dokáže připojit k účtu úložiště. 

   d. Pokud chcete propojenou službu uložit, vyberte **Dokončit**. 

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto postupu vytvoříte dvě datové sady: **InputDataset** a **OutputDataset**. Tyto datové sady jsou typu **AzureBlob**. Odkazují na propojenou službu Azure Storage, kterou jste vytvořili v předchozí části. 

Vstupní datová sada představuje zdrojová data ve vstupní složce. V definici vstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**input**) a soubor (**emp.txt**) obsahující zdrojová data. 

Výstupní datová sada představuje data kopírovaná do cíle. V definici výstupní datové sady určíte kontejner objektů blob (**adftutorial**), složku (**output**) a soubor, do kterého se data kopírují. Ke každému spuštění kanálu je přiřazené jedinečné ID. Přístup k tomuto ID můžete získat pomocí systémové proměnné **RunId**. Název výstupního souboru se dynamicky vyhodnocuje na základě ID spuštění kanálu.   

V nastavení propojené služby jste zadali účet úložiště Azure obsahující zdrojová data. V nastavení zdrojové datové sady určíte, kde přesně se data nacházejí (kontejner objektů blob, složka a soubor). V nastavení datové sady jímky určíte, kam se data kopírují (kontejner objektů blob, složka a soubor). 
 
1. Vyberte tlačítko **+** (plus) a pak vyberte **Datová sada**.

   ![Nabídka pro vytvoření datové sady](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na stránce **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **pokračovat**. 

   ![Výběr možnosti Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**. V takovém případě vyberte **binární soubor** při kopírování souborů bez analýzy obsahu.

    ![Typ formátu dat](./media/doc-common-process/select-binary.png)

1. Na stránce **nastavit vlastnosti** proveďte následující kroky:

    a. Do **pole název**zadejte **InputDataset**. 

    b. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.

    c. V části **Cesta k souboru** vyberte tlačítko **Procházet**.

    d. V okně **Zvolte soubor nebo složku** přejděte do složky **input** v kontejneru **adftutorial**, vyberte soubor **emp.txt** a pak vyberte **Dokončit**.
    
    e. Vyberte **pokračovat**.   

    ![Nastavit vlastnosti pro InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Pomocí stejného postupu vytvořte výstupní datovou sadu:  

    a. Vyberte tlačítko **+** (plus) a pak vyberte **Datová sada**.

    b. Na stránce **Nová datová sada** vyberte **Azure Blob Storage**a pak vyberte **pokračovat**.

    c. Na stránce **Vybrat formát** zvolte typ formátu dat a pak vyberte **pokračovat**.

    d. Na stránce **Vlastnosti sady** zadejte **OutputDataset** pro název. Jako propojená služba vyberte **AzureStorageLinkedService** .

    e. V části **cesta k souboru**zadejte **adftutorial/Output**. Pokud **výstupní** složka neexistuje, aktivita kopírování ji vytvoří za běhu.

    f. Vyberte **pokračovat**.   

## <a name="create-a-pipeline"></a>Vytvoření kanálu 
V tomto postupu vytvoříte a ověříte kanál s aktivitou kopírování, která používá vstupní a výstupní datovou sadu. Aktivita kopírování kopíruje data ze souboru zadaného v nastavení vstupní datové sady do souboru zadaného v nastavení výstupní datové sady. Pokud vstupní datová sada určuje pouze složku (bez názvu souboru), aktivita kopírování zkopíruje do cíle všechny soubory ve zdrojové složce. 

1. Vyberte tlačítko **+** (plus) a pak vyberte **Kanál**. 

1. Na kartě **Obecné** jako **Název** zadejte **CopyPipeline**. 

1. Na panelu nástrojů **Aktivity** rozbalte **Přesunout a transformovat**. Přetáhněte aktivitu **kopírování dat** z panelu nástrojů **aktivity** na plochu návrháře kanálu. Na panelu nástrojů **Aktivity** můžete aktivity také vyhledávat. Jako **Název** zadejte **CopyFromBlobToBlob**.

1. V nastavení aktivity kopírování přepněte na kartu **Zdroj** a jako **Zdrojová datová sada** vyberte **InputDataset**.

1. V nastavení aktivity kopírování přepněte na kartu **Jímka** a jako **Datová sada jímky** vyberte **OutputDataset**.

1. Kliknutím na **Ověřit** na panelu nástrojů kanálu nad plátnem ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Pokud chcete zavřít výstup ověřování, vyberte tlačítko **>>** (šipky doprava). 

## <a name="debug-the-pipeline"></a>Ladění kanálu
V tomto kroku provedete ladění kanálu před tím, než ho nasadíte do služby Data Factory. 

1. Kliknutím na **Ladit** na panelu nástrojů kanálu nad plátnem aktivujte testovací běh. 
    
1. Ověřte, že se na kartě **Výstup** v nastavení kanálu v dolní části zobrazí stav spuštění kanálu. 

1. Ověřte, že složka **output** kontejneru **adftutorial** obsahuje výstupní soubor. Pokud výstupní složka neexistuje, Služba Data Factory ji automaticky vytvoří. 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu
V tomto postupu nasadíte entity (propojené služby, datové sady a kanály) do služby Azure Data Factory. Pak ručně aktivujete spuštění kanálu. 

1. Před aktivací kanálu je nutné publikovat entity do služby Data Factory. Pokud je chcete publikovat, v horní části vyberte **Publikovat vše**. 

   ![Tlačítko Publikovat](./media/quickstart-create-data-factory-portal/publish-button.png)
1. Pokud chcete kanál aktivovat ručně, vyberte **Přidat aktivační událost** na panelu nástrojů kanálu a potom vyberte **aktivovat nyní**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Pomocí tlačítka **Aktualizovat** seznam aktualizujte.

   ![Karta pro monitorování spuštění kanálu](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Vyberte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Na této stránce se zobrazí stav spuštění aktivity kopírování. 

1. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) ve sloupci **Akce**. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

   ![Podrobnosti o aktivitě kopírování](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Ověřte, že složka **output** obsahuje nový soubor. 
1. Kliknutím na odkaz **spuštění kanálu** můžete přepnout zpátky na zobrazení **spuštění kanálu** ze zobrazení **spuštění aktivity** . 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Aktivace kanálu podle plánu
Tento postup je v tomto kurzu volitelný. Můžete vytvořit *aktivační událost plánovače* a naplánovat pravidelné spouštění kanálu (každou hodinu, každý den atd.). V tomto postupu vytvoříte aktivační událost, která se bude spouštět každou minutu až do koncového data a času, které zadáte. 

1. Přepněte na kartu **Vytvořit**. 

1. Přejít na svůj kanál, na panelu nástrojů kanálu vyberte **Přidat aktivační událost** a pak vyberte **Nová/upravit**. 

1. Na stránce **Přidat aktivační události** vyberte **Zvolit aktivační událost** a pak vyberte **Nová**. 

1. Na stránce **Nová aktivační událost** v části **Konec** vyberte **K datu**, zadejte koncový čas pár minut od aktuálního času a pak vyberte **Použít**. 

   Každé spuštění kanálu je zpoplatněno, proto zadejte koncový čas pouze pár minut po času zahájení. Ujistěte se, že se jedná o stejný den. Ujistěte se však, že je dostatek času na spuštění kanálu mezi časem publikování a koncovým časem. Aktivační událost nabývá účinnosti po publikování řešení do služby Data Factory, a ne při uložení aktivační události v uživatelském rozhraní. 

1. Na stránce **Nová aktivační událost** zaškrtněte políčko **aktivováno** a pak vyberte **Uložit**. 

   ![Nové nastavení triggeru](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Prostudujte si zprávu upozornění a vyberte **Dokončit**.

1. Vyberte **Publikovat vše** a publikujte změny do služby Data Factory. 

1. Vlevo přepněte na kartu **Monitorování**. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. Uvidíte, že se kanál spustí každou minutu od času publikování až do koncového času. 

   Všimněte si hodnot ve sloupci **Aktivoval(a)** . Ruční spuštění aktivační události proběhlo v kroku, který jste provedli dříve (**Aktivovat**). 

1. Přepněte na zobrazení **Spuštění aktivační události**. 

1. Ověřte, že se ve složce **output** vytvořil výstupní soubor pro každé spuštění kanálu až do zadaného koncového data a času. 

## <a name="next-steps"></a>Další postup
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 
