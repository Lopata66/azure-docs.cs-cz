---
title: Přizpůsobení nastavení pro Azure-SSIS Integration Runtime
description: Tento článek popisuje, jak použít vlastní rozhraní pro instalaci pro Azure-SSIS Integration Runtime k instalaci dalších součástí nebo změně nastavení.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 576861265771977f7e13140dd595f47bf556e585
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331895"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vlastní nastavení pro Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR) v Azure Data Factory (ADF) poskytuje rozhraní pro přidání vlastních kroků během zřizování nebo opětovné konfigurace Azure-SSIS IR. 

Pomocí vlastní instalace můžete změnit výchozí konfiguraci konfigurace nebo prostředí, například spustit další služby systému Windows, zachovat přístup k přihlašovacím údajům pro sdílené složky nebo použít silný kryptografický protokol/vyšší zabezpečený síťový protokol (TLS 1,2). Případně můžete do každého uzlu Azure-SSIS IR nainstalovat další komponenty vlastní/třetí strany, například sestavení, ovladače nebo rozšíření. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).

Vlastní nastavení můžete provést na svém Azure-SSIS IR jedním ze dvou způsobů: 
* **Standardní vlastní nastavení pomocí skriptu**: Připravte skript a jeho přidružené soubory a nahrajte je dohromady do kontejneru objektů BLOB v účtu úložiště Azure. Když nastavíte nebo znovu nakonfigurujete Azure-SSIS IR, zadáte identifikátor URI (Uniform Resource Identifier) sdíleného přístupového podpisu (SAS) pro váš kontejner. Každý uzel vaší Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalaci se zvýšenými oprávněními. Po dokončení vlastní instalace každý uzel nahraje standardní výstup spuštění a další protokoly do vašeho kontejneru.
* **Expresní vlastní nastavení bez skriptu**: Spusťte některé běžné konfigurace systému a příkazy systému Windows nebo nainstalujte některé oblíbené nebo Doporučené dodatečné součásti bez použití skriptů.

Můžete nainstalovat bezplatné, nelicencované komponenty a placené, licencované komponenty se standardními a expresními vlastními nastaveními. Pokud jste nezávislý výrobce softwaru (ISV), přečtěte si téma [vývoj placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Pro výhody budoucích vylepšení doporučujeme pro vaši Azure-SSIS IR použít pro vlastní instalaci řadu uzlů V3 nebo novější.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí pouze pro standardní vlastní nastavení:

- Pokud chcete použít *gacutil.exe* ve vašem skriptu k instalaci sestavení v globální mezipaměti sestavení (GAC), je nutné zadat *gacutil.exe* jako součást vlastního nastavení. Případně můžete použít kopii, která je k dispozici v kontejneru *Public Preview* , popsaný dále v části "pokyny".

- Pokud chcete odkazovat na podsložku ve skriptu, *msiexec.exe* nepodporuje `.\` zápis pro odkaz na kořenovou složku. Použijte příkaz, například `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Sdílené složky pro správu nebo skryté sdílené síťové složky, které jsou automaticky vytvořeny systémem Windows, nejsou aktuálně podporovány v Azure-SSIS IR.

- Ovladač IBM iSeries Access ODBC není podporován na Azure-SSIS IR. Při vlastní instalaci se může zobrazit chyba instalace. Pokud to uděláte, požádejte o pomoc podporu společnosti IBM.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K přizpůsobení Azure-SSIS IR budete potřebovat následující položky:

- [Předplatné Azure](https://azure.microsoft.com/)

- [Zřízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Účet služby Azure Storage](https://azure.microsoft.com/services/storage/). Nepožaduje se pro expresní vlastní nastavení. Pro standardní vlastní nastavení nahráváte a ukládáte vlastní instalační skript a jeho přidružené soubory do kontejneru objektů BLOB. Vlastní proces instalace také nahraje své protokoly spuštění do stejného kontejneru objektů BLOB.

## <a name="instructions"></a>Pokyny

Můžete zřídit nebo znovu nakonfigurovat Azure-SSIS IR vlastními nastaveními v uživatelském rozhraní ADF. Pokud chcete stejný postup provést pomocí PowerShellu, Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Standardní vlastní nastavení

Pokud chcete zřídit nebo znovu nakonfigurovat Azure-SSIS IR se standardními vlastními nastaveními, proveďte následující kroky.

1. Připravte vlastní instalační skript a jeho přidružené soubory (například. bat,. cmd,. exe,. dll,. msi nebo. ps1).

   * Musíte mít soubor skriptu s názvem *Main. cmd*, který je vstupním bodem vlastní instalace.  
   * Abyste měli jistotu, že se skript dá spustit bezobslužně, doporučujeme ho nejdřív otestovat na svém místním počítači.  
   * Pokud chcete, aby byly do kontejneru nahrány další protokoly vygenerované jinými nástroji (například *msiexec.exe*), zadejte předdefinovanou proměnnou prostředí, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složku protokolu ve skriptech (například *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ Install. log*).

1. Stáhněte, nainstalujte a otevřete [Průzkumník služby Azure Storage](https://storageexplorer.com/).

   a. V části **(místní a připojené)** klikněte pravým tlačítkem na **účty úložiště**a pak vyberte **připojit k Azure Storage**.

      ![Připojení ke službě Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Vyberte **použít název a klíč účtu úložiště**a pak vyberte **Další**.

      ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Zadejte název a klíč účtu služby Azure Storage, klikněte na tlačítko **Další**a pak vyberte **připojit**.

      ![Zadejte název a klíč účtu úložiště.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. V rámci připojeného účtu úložiště Azure klikněte pravým tlačítkem na **kontejnery objektů BLOB**, vyberte **vytvořit kontejner objektů BLOB**a pojmenujte nový kontejner.

      ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Vyberte nový kontejner a nahrajte vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že jste nahráli *Main. cmd* na nejvyšší úrovni kontejneru, a ne do žádné složky. Také zajistěte, aby váš kontejner obsahoval pouze nezbytné vlastní instalační soubory, aby se mohly stahovat do Azure-SSIS IR později nebude trvat dlouhou dobu. Maximální doba vlastní instalace je v současnosti nastavená na 45 minut před vypršením časového limitu. To zahrnuje čas ke stažení všech souborů z vašeho kontejneru a jejich instalaci na Azure-SSIS IR. Pokud instalace vyžaduje více času, vyvolejte lístek podpory.

      ![Nahrání souborů do kontejneru objektů BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klikněte pravým tlačítkem na kontejner a pak vyberte **získat sdílený přístupový podpis**.

      ![Získání sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   například Vytvořte identifikátor URI SAS pro váš kontejner s dostatečně dlouhou dobou vypršení platnosti a s oprávněním pro čtení/zápis/seznam. Identifikátor URI SAS potřebujete ke stažení a spuštění skriptu vlastní instalace a jeho přidružených souborů pokaždé, když se kterýkoli uzel Azure-SSIS IR obnoví nebo restartuje. Pro nahrání protokolů spuštění instalace potřebujete oprávnění k zápisu.

      > [!IMPORTANT]
      > Zajistěte vypršení platnosti identifikátoru URI SAS a vlastní prostředky instalace budou vždy k dispozici během celého životního cyklu Azure-SSIS IR, od vytvoření po odstranění, zejména v případě, že během této doby pravidelně zastavíte a zahájíte Azure-SSIS IR.

      ![Generování sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Zkopírujte a uložte identifikátor URI SAS vašeho kontejneru.

      ![Zkopírování a uložení sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Při zřizování nebo překonfigurování Azure-SSIS IR v uživatelském rozhraní ADF zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** a zadejte identifikátor URI SAS kontejneru do pole **vlastní nastavení identifikátoru URI SAS kontejneru** .

   ![Rozšířená nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>Expresní vlastní instalace

Pokud chcete zřídit nebo znovu nakonfigurovat Azure-SSIS IR pomocí expresních vlastních nastavení, proveďte následující kroky.

1. Při zřizování nebo překonfigurování Azure-SSIS IR v uživatelském rozhraní ADF zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** . 

1. Výběrem **nové** otevřete podokno **Přidat vlastní nastavení Express** a potom vyberte typ v rozevíracím seznamu **typ vlastní instalace Express** :

   * Když vyberete typ **příkazu Spustit** účet, můžete zachovat přihlašovací údaje pro sdílené složky nebo sdílené složky Azure v Azure-SSIS IR tak, že do polí **/Add**, **/User**a **/Pass** zadáte cílový název počítače nebo název domény, název účtu nebo uživatelské jméno a klíč nebo heslo účtu. To se podobá spuštění příkazu Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) na místním počítači.
   
   * Pokud vyberete možnost **Přidat typ proměnné prostředí** , můžete přidat proměnné prostředí systému Windows, které budou použity v balíčcích spouštěné na Azure-SSIS IR, a to zadáním názvu proměnné prostředí a hodnoty do polí **název proměnné** a **hodnota proměnné** . To se podobá spuštění příkazu Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) na místním počítači.

   * Pokud vyberete možnost **instalovat licencovaný typ součásti** , pak můžete v rozevíracím seznamu **název součásti** vybrat integrovanou součást z našich partnerů ISV:

     * Pokud vyberete součást **Task Factory pro SentryOne** , můžete do svého Azure-SSIS IR nainstalovat sadu [úloh Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) komponent z SentryOne, a to zadáním licenčního kódu produktu, který jste si z nich koupili v poli **licenční klíč** . Aktuální integrovaná verze je **2019.4.3**.

     * Pokud vyberete **OH22'S HEDDA. V/** v součást, můžete nainstalovat [HEDDA. Kvalita/čisticí data Azure-SSIS IR v/](https://hedda.io/ssis-component/) v oh22 po zakoupení služby Aktuální integrovaná verze je **1.0.13**.

     * Pokud vyberete komponentu **oh22's SQLPhonetics.NET** , můžete nainstalovat součást [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/Matching z oh22 na svou Azure-SSIS IR zadáním licenčního kódu produktu, který jste si z nich koupili v poli **licenční klíč** . Aktuální integrovaná verze je **1.0.43**.

     * Pokud vyberete komponentu **KingswaySoft Integration Toolkit pro SSIS** , můžete nainstalovat konektory sady [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) pro aplikace CRM/ERP/marketing a spolupráci, jako je například Microsoft Dynamics/SharePoint/Project Server, marketingový Cloud Oracle nebo Salesforce, a to z KingswaySoft na vaše Azure-SSIS IR zadáním licenčního kódu produktu, který jste si z nich koupili v poli **licenční klíč** . Aktuální integrovaná verze je **2019,2**.

     * Pokud vyberete komponentu **KingswaySoft pro SSIS produktivity** , můžete do svého Azure-SSIS IR nainstalovat sadu funkcí sady [SSIS produktivní Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) z KingswaySoft, a to zadáním licenčního kódu produktu, který jste si z nich koupili v poli **licenční klíč** . Aktuální integrovaná verze je **10,0**.

     * Pokud vyberete **Xtrahovat software Theobald** , můžete nainstalovat [xtrahovat je](https://theobald-software.com/en/xtract-is/) sada konektorů pro systém SAP (ERP, s/4HANA, černobílá) od Theobald softwaru na vašich Azure-SSIS IR přetažením & ze souboru s **licenčním souborem** , který jste si z nich koupili, do pole soubor s licencí. Aktuální integrovaná verze je **6.1.1.3**.

Přidaná expresní vlastní nastavení se zobrazí na stránce **Upřesnit nastavení** . Pokud je chcete odebrat, zaškrtněte příslušná políčka a potom vyberte **Odstranit**.

### <a name="azure-powershell"></a>Azure PowerShell

Při zřizování nebo překonfigurování Azure-SSIS IR vlastními nastaveními pomocí Azure PowerShell proveďte následující kroky.

1. Pokud je vaše Azure-SSIS IR už spuštěná nebo spuštěná, zastavte ho jako první.

1. Pak můžete přidat nebo odebrat vlastní nastavení spuštěním `Set-AzDataFactoryV2IntegrationRuntime` rutiny před zahájením Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

1. Po dokončení standardní vlastní instalace a spuštění Azure-SSIS IR můžete najít standardní výstup *Main. cmd* a dalších protokolů spuštění ve složce *Main. cmd. log* vašeho kontejneru.

### <a name="standard-custom-setup-samples"></a>Ukázky standardních vlastních nastavení

Pokud chcete zobrazit a znovu použít některé ukázky standardních vlastních nastavení, proveďte následující kroky.

1. Připojte se k našemu Public Preview kontejneru pomocí Průzkumník služby Azure Storage.

   a. V části **(místní a připojená)** klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k Azure Storage**, vyberte **Použít připojovací řetězec nebo identifikátor URI sdíleného přístupového podpisu**a pak vyberte **Další**.

      ![Připojení ke službě Azure Storage pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Vyberte **použít identifikátor URI SAS** a potom do pole **URI** zadejte následující identifikátor URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Zadejte sdílený přístupový podpis pro kontejner.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Vyberte **Další**a pak vyberte **připojit**.

   d. V levém podokně vyberte připojený kontejner **publicpreview** a dvakrát klikněte na složku *CustomSetupScript* . V této složce jsou následující položky:

      * *Ukázková* složka, která obsahuje vlastní instalaci pro instalaci základní úlohy do každého uzlu vašeho Azure-SSIS IR. Úkol neprovede žádnou akci, ale během několika sekund režim spánku. Složka obsahuje taky složku *Gacutil* , jejíž celý obsah (*gacutil.exe*, *gacutil.exe.config*a *1033\gacutlrc.dll*) se dá zkopírovat jako do vašeho kontejneru.

      * Složka *UserScenarios* , která obsahuje několik vlastních ukázek instalace ze scénářů reálných uživatelů.

        ![Obsah kontejneru verze Public Preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dvojím kliknutím na složku *UserScenarios* vyhledejte následující položky:

      * Složka *rozhraní .NET FRAMEWORK 3,5* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci starší verze .NET Framework, která může být vyžadována pro vlastní součásti na každém uzlu Azure-SSIS IR.

      * Složka *BCP* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci SQL Server nástrojů příkazového řádku (*MsSqlCmdLnUtils.msi*), včetně programu hromadného kopírování (*BCP*), na každém uzlu Azure-SSIS IR.

      * Složka *aplikace Excel* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci sestavení a knihoven C#, které lze použít v úlohách skriptu k dynamickému čtení a zápisu souborů aplikace EXCEL na každém uzlu Azure-SSIS IR. 
      
        Nejdřív stáhněte [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) a [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)a pak je nahrajte dohromady pomocí *Main. cmd* do kontejneru. Případně, pokud chcete použít pouze standardního Správce připojení aplikace Excel, zdroj aplikace Excel a cíl aplikace Excel, je již v Azure-SSIS IR předinstalován požadovaný přístup pro distribuci, takže nepotřebujete žádnou vlastní instalaci.
      
      * Složka *MySQL ODBC* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladačů MySQL odbc do každého uzlu Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru MySQL pomocí Správce připojení ODBC, zdroje a cíle. 
     
        Nejdřív [Stáhněte nejnovější verze 64 a 32 instalačních programů ovladačů MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (například *mysql-connector-odbc-8.0.13-winx64.msi* a *mysql-connector-odbc-8.0.13-win32.msi*) a pak je nahrajte společně s *Main. cmd* do kontejneru.

      * Složka *Oracle Enterprise* , která obsahuje skript vlastního nastavení (*Main. cmd*) a konfigurační soubor bezobslužné instalace (*Client. rsp*) pro instalaci konektoru Oracle a ovladače OCI do každého uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení Oracle, zdroje a cíle. 
      
        Nejdřív Stáhněte konektory Microsoft Connectors v 5.0 pro Oracle (*AttunitySSISOraAdaptersSetup.msi* a *AttunitySSISOraAdaptersSetup64.msi*) z webu [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovějšího klienta Oracle (například *winx64_12102_client.zip*) od [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)a pak je nahrajte společně s *Main. cmd* a *Client. rsp* do svého kontejneru. Použijete-li TNS pro připojení k Oracle, budete také muset stáhnout *souboru Tnsnames. Ora*, upravit ho a nahrát ho do kontejneru, aby bylo možné ho zkopírovat do instalační složky Oracle během instalace.

      * *Standardní složka ADO.NET Oracle* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladače Oracle ODP.NET do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení ADO.NET, zdroje a cíle. 
      
        Nejdřív [Stáhněte nejnovější ovladač Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (například *ODP.NET_Managed_ODAC122cR1.zip*) a pak ho nahrajte společně s *Main. cmd* do kontejneru.
       
      * *Standardní složka ODBC standardu Oracle* , která obsahuje skript vlastního nastavení (*Main. cmd*), který nainstaluje ovladač Oracle ODBC a nakonfiguruje název zdroje dat (DSN) na každém uzlu vašeho Azure-SSIS IR. Tato instalace umožňuje použít Správce připojení ODBC, zdroj a cíl nebo Power Query Správce připojení a zdroj s typem zdroje dat ODBC pro připojení k serveru Oracle. 
      
        Nejdřív Stáhněte nejnovější verzi pro okamžitého klienta Oracle (základní balíček nebo základní balíček verze Lite) a balíček ODBC a nahrajte ho dohromady pomocí *Main. cmd* do svého kontejneru:
        * [Stáhnout 64 – bitové balíčky](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (základní balíček: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Základní balíček verze Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Balíček ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Stáhnout 32 – bitové balíčky](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (základní balíček: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Základní balíček verze Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Balíček ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *Standardní složka OLEDB Oracle* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladače Oracle OLEDB do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení OLEDB, zdroje a cíle. 
     
        Nejdřív [Stáhněte nejnovější ovladač Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (například *ODAC122010Xcopy_x64.zip*) a pak ho nahrajte společně s *Main. cmd* do kontejneru.

      * *POSTGRESQL složka ODBC* , která obsahuje skript vlastního nastavení (*Main. cmd*), který nainstaluje ovladače POSTGRESQL ODBC do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k PostgreSQL serveru pomocí Správce připojení ODBC, zdroje a cíle. 
     
        Nejdřív [Stáhněte nejnovější 64 a 32 PostgreSQL instalačních ovladačů ovladačů ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (například *psqlodbc_x64.msi* a *psqlodbc_x86.msi*) a pak je nahrajte dohromady pomocí *Main. cmd* do kontejneru.

      * *SAP BW* složky, která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci sestavení SAP .NET Connector (*librfc32.dll*) na každý uzel vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k SAP BW serveru pomocí Správce připojení SAP Business Warehouse (ČERNOBÍLý přístup), zdroje a cíle. 
      
        Nejdřív nahrajte 64 nebo 32 verzi *librfc32.dll* z instalační složky SAP společně s *Main. cmd* do svého kontejneru. Skript pak zkopíruje sestavení SAP do složky *%windir%\syswow64* nebo *%windir%\System32* během instalace.

      * Složka *úložiště* , která obsahuje skript vlastního nastavení (*Main. cmd*), který se má nainstalovat Azure PowerShell v každém uzlu Azure-SSIS IR. Tato instalace umožňuje nasadit a spustit balíčky SSIS, které spouštějí [skripty PowerShellu pro práci s účtem služby Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Zkopírujte *Main. cmd*, vzorový *AzurePowerShell.msi* (nebo použijte nejnovější verzi) a *storage.ps1* do svého kontejneru. Jako šablonu pro balíčky použijte *PowerShell. dtsx* . Šablona balíčku kombinuje [úlohu stažení objektu BLOB v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), která stáhne *storage.ps1* jako upravitelný skript PowerShellu, a [úlohu spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), která spustí skript na každém uzlu.

      * Složka *Teradata* , která obsahuje skript vlastního nastavení (*Main. cmd*), přidružený soubor (*install. cmd*) a instalační balíčky (*. msi*). Tyto soubory instalují konektory Teradata, rozhraní API Teradata Parallel Transporter (TPT) a ovladač ODBC na každém uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k serveru Teradata pomocí Správce připojení Teradata, zdroj a cíl. 
      
        Nejprve [si stáhněte soubor zip Tools a Utilities 15. x](http://partnerintelligence.teradata.com) (například *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) a pak ho nahrajte společně s dříve uvedenými soubory *. cmd* a *. msi* do kontejneru.

      * Složka *TLS 1,2* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro použití silné kryptografie/bezpečnějšího síťového protokolu (TLS 1,2) a zakázání starších verzí protokolu SSL/TLS na každém uzlu Azure-SSIS IR.

      * Složka *Zulu OPENJDK* , která obsahuje skript vlastního nastavení (*Main. cmd*) a soubor PowerShellu (*install_openjdk.ps1*) pro instalaci Zulu OPENJDK do každého uzlu Azure-SSIS IR. Tato instalace umožňuje použít Azure Data Lake Store a flexibilní konektory souborů ke zpracování souborů ORC a Parquet. Další informace najdete v tématu [Azure Feature Pack pro integrační služby](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Nejdřív [Stáhněte nejnovější Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (například *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) a pak ho nahrajte společně s *Main. cmd* a *install_openjdk.ps1* do svého kontejneru.

        ![Složky ve složce scénáře pro uživatele](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Chcete-li znovu použít tyto standardní ukázky pro vlastní nastavení, zkopírujte obsah vybrané složky do kontejneru.

1. Při zřizování nebo překonfigurování Azure-SSIS IR v uživatelském rozhraní ADF zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** a zadejte identifikátor URI SAS kontejneru do pole **vlastní nastavení identifikátoru URI SAS kontejneru** .
   
1. Když zřídíte nebo překonfigurujete Azure-SSIS IR pomocí Azure PowerShell, zastavte ho, pokud už je spuštěný nebo spuštěný, spusťte `Set-AzDataFactoryV2IntegrationRuntime` rutinu s identifikátorem URI SAS vašeho kontejneru jako hodnotu `SetupScriptContainerSasUri` parametru a pak spusťte Azure-SSIS IR.

1. Po dokončení standardní vlastní instalace a spuštění Azure-SSIS IR můžete najít standardní výstup *Main. cmd* a dalších protokolů spuštění ve složce *Main. cmd. log* vašeho kontejneru.

## <a name="next-steps"></a>Další kroky

- [Nastavte edici Enterprise Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Vývoj placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
