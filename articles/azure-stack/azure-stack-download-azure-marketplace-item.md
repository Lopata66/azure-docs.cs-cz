---
title: Stažení položek z marketplace z Azure | Dokumentace Microsoftu
description: Operátor cloudu můžete stažení položek z marketplace z Azure do své nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/13/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: e396fc82754188ea655c70b44d4bf937a3c3163c
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544207"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Stažení položek z marketplace z Azure do služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako operátor cloudu stažení položek z Azure Marketplace a zpřístupněte je ve službě Azure Stack. Položky, které můžete použít způsobují uspořádaný seznam položek z Azure Marketplace, které jsou předem otestovali a získali podporované pro práci s Azure Stack. Do tohoto seznamu jsou často přidané další položky, takže můžete pokračovat ke kontrole zpět nový obsah. 

Existují dva scénáře pro připojení k Azure Marketplace: 

- **Připojené scénář** –, který vyžaduje prostředí Azure Stack pro připojení k Internetu. Na portálu Azure Stack umožňuje vyhledat a stáhnout položek. 
- **Scénář odpojených nebo připojených částečně** –, který vyžaduje, abyste pro přístup k Internetu pomocí marketplace syndikace nástroj pro stažení položek z marketplace. Pak přeneste své stažené soubory do vaší instalace služby Azure Stack. Tento scénář používá PowerShell.

Zobrazit [položky Azure Marketplace pro Azure Stack](azure-stack-marketplace-azure-items.md) seznam položky marketplace si můžete stáhnout.


## <a name="connected-scenario"></a>Připojené scénář
Pokud Azure Stack se připojuje k Internetu, můžete použít portál pro správu pro stažení položek z marketplace.

### <a name="prerequisites"></a>Požadavky
Nasazení Azure Stack musí mít připojení k Internetu a být [registrované v Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Použití portálu k stažení položek z marketplace  
1. Přihlaste se k portálu Správce služby Azure Stack.

2.  Zkontrolujte místo na úložiště k dispozici před stažením položek z marketplace. Později při výběru položky ke stažení, můžete porovnat velikost ke stažení na kapacitu úložiště k dispozici. Pokud kapacita je omezená, zvažte možnosti [Správa dostupného](azure-stack-manage-storage-shares.md#manage-available-space). 

    Kontrola dostupného místa v **Správa oblastí** vyberte oblast, kterou chcete prozkoumat a potom přejděte ke **poskytovatelů prostředků** > **úložiště**.

    ![Zkontrolujte místo úložiště](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Otevřete Azure Marketplace zásobníku a připojte se k Azure. Chcete-li to provést, vyberte **Marketplace správu**a pak vyberte **přidat z Azure**.

    ![Z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portál zobrazuje seznam položek, které jsou k dispozici ke stažení z webu Azure Marketplace. Kliknutím na jednotlivé položky k zobrazení jeho popis a další informace o tom, včetně jeho velikost ke stažení. 

    ![Seznam Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Vyberte položku a pak vyberte **Stáhnout**. Stahování se liší.

    ![Stáhnout zprávu](media/azure-stack-download-azure-marketplace-item/image04.png)

    Po dokončení stahování, můžete nasadit nové položky marketplace jako operátory Azure stacku nebo uživatele.

5. Pokud chcete nasadit stažené položky, vyberte **+ vytvořit prostředek**a potom je prohledejte mezi kategorie pro novou položku marketplace. Dále vyberte položku k zahájení procesu nasazení. Proces se liší pro marketplace různé položky. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojení nebo částečně připojeného scénáře

Pokud Azure Stack v odpojeném režimu a bez připojení k Internetu, pomocí prostředí PowerShell a *marketplace syndikace nástroj* pro stažení položek z marketplace k počítači s připojením k Internetu. Položky se pak přenášíte do svého prostředí Azure Stack. V odpojeném prostředí nelze stažení položek z marketplace pomocí portálu Azure Stack. 

V případě propojené se lze také nástroj marketplace syndikace. 

Existují dvě části pro tento scénář:
- **Část 1:** stáhnout z webu Azure Marketplace. Na počítači s přístupem k Internetu konfigurace prostředí PowerShell, stáhněte si nástroj syndikace a pak si stáhnout formuláře položky na webu Azure Marketplace.  
- **Část 2:** nahrávání a publikování na Tržišti Azure Stack. Přesunout soubory, které jste stáhli do prostředí Azure Stack je importovat do služby Azure Stack a publikujte je na Azure Marketplace zásobníku.  


### <a name="prerequisites"></a>Požadavky
- Musí být vašeho nasazení Azure stacku [registrované v Azure](azure-stack-register.md).  

- Počítač, který má připojení k Internetu, musí mít **modulu Azure Stack Powershellu verze 1.2.11** nebo vyšší. Pokud tam ještě nejsou, [nainstalujte Azure Stack konkrétní moduly Powershellu](azure-stack-powershell-install.md).  

- Umožňuje importovat položku staženého webu marketplace [prostředí PowerShell pro operátory Azure stacku](azure-stack-powershell-configure-admin.md) musí být nakonfigurované.  

- Musíte mít [účtu úložiště](azure-stack-manage-storage-accounts.md) ve službě Azure Stack, který je veřejně dostupný kontejner (což je objekt blob úložiště). Používáte kontejner jako dočasné úložiště pro soubory marketplace položky galerie. Pokud nejste obeznámeni s účty úložiště a kontejnery, přečtěte si téma [práce s objekty BLOB – Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) v dokumentaci k Azure.

- Nástroj syndikace marketplace se stáhne při prvním postupu. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Pomocí nástroje syndikace marketplace pro stažení položek z marketplace

1. Na počítači s připojením k Internetu otevřete konzolu Powershellu jako správce.

2. Přidáte účet Azure, který jste použili k registraci Azure Stack. Přidání účtu, v Powershellu spusťte `Add-AzureRmAccount` bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a budete nejspíš muset použít 2 ověřování na základě konfigurace vašeho účtu.

3. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, kterou jste použili k registraci:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Stažení nejnovější verze nástroje syndikace marketplace pomocí následujícího skriptu:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importujte modul syndikace a pak spusťte nástroj spuštěním následujícího skriptu. Nahradit *cesta k cílové složce* s umístěním pro uložení souborů můžete stáhnout z webu Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Když je nástroj spuštěn, zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure. Přihlaste se k účtu Azure, který jste použili k registraci Azure Stack. Po úspěšném přihlášení by se zobrazit obrazovka jako na následujícím obrázku se seznamem položek z marketplace k dispozici.  

   ![Automaticky otevírané okno položky Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Vyberte položku, kterou chcete stáhnout a poznamenejte si, *verze*. (Můžete podržet *Ctrl* výběr více bitových kopií.) Odkazujete *verze* při importu položky v dalším postupu. 
   
   Můžete také filtrovat seznam imagí pomocí **přidat kritéria** možnost.

8. Vyberte **OK**a pak si přečtěte a přijměte právní podmínky. 

9. Čas, který stahování bude trvat, závisí na velikosti položky. Po dokončení stahování, položka je k dispozici ve složce, která jste zadali ve skriptu. Soubor ke stažení obsahuje soubor virtuálního pevného disku (pro virtuální počítače) nebo. Soubor ZIP (pro rozšíření virtuálních počítačů). Zahrnuje také balíček Galerie v *.azpkg* formátu. (A *.azpkg* balíček je *ZIP* souboru.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importovat soubor ke stažení a publikování na Azure Marketplace zásobníku
1. Soubory pro Image virtuálních počítačů nebo šablon řešení, které máte [dříve stažené](#use-the-marketplace-syndication-tool-to-download-marketplace-items) musí být dostupné místně do vašeho prostředí Azure Stack.  

2. Použití portálu pro správu k odeslání balíčku pro položku marketplace (.azpkg soubor) a image virtuálního pevného disku (soubor .vhd) do úložiště objektů Blob v Azure stacku. Nahrání balíčku a souborů na disku je zpřístupní pro Azure Stack, později publikování položky Azure Stack Marketplace.

   Nahrávání je potřeba mít účet storage s kontejnerem veřejně přístupná (viz požadavky pro tento scénář).  
   1. V portálu pro správu služby Azure Stack, přejděte na **všechny služby** a potom v části **DATA + úložiště** vyberte **účty úložiště**.  
   
   2. Vyberte účet úložiště z vašeho předplatného a pak v části **služby BLOB SERVICE**vyberte **kontejnery**.  
      ![Služby BLOB service](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Vyberte kontejner, kterou chcete použít a pak vyberte **nahrát** otevřít **nahrát objekt blob** podokně.  
      ![Kontejner](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. V podokně nahrání objektu blob přejděte k souborům balíčku a disk k načtení do úložiště a pak vyberte **nahrát**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. V podokně kontejner se zobrazí soubory, které nahrajete. Vyberte soubor a zkopírujte adresu URL **vlastnosti objektu Blob** podokně. Tuto adresu URL použijete v dalším kroku při importu položky marketplace do služby Azure Stack.  Na následujícím obrázku je kontejner *úložiště objektů blob testu* a soubor je *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Soubor je adresa URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Vlastnosti objektu BLOB](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

3. Importovat obraz virtuálního pevného disku do služby Azure Stack pomocí **přidat AzsPlatformimage** rutiny. Když tuto rutinu použili, nahraďte *vydavatele*, *nabízejí*a jiné hodnoty parametru s hodnotami bitovou kopii, kterou importujete. 

   Můžete získat *vydavatele*, *nabízejí*, a *sku* hodnoty bitové kopie z textového souboru, který stahuje se soubor AZPKG. Textový soubor je uložen v cílovém umístění. *Verze* hodnotu verze, které jste si poznamenali při stahování položky z Azure v předchozím postupu. 
 
   V následujícím příkladu skriptu se používají hodnoty pro Windows Server 2016 Datacenter - virtuálního počítače jádra serveru. Hodnota pro *- Osuri* je příklad cesty k umístění úložiště objektů blob pro položku.

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```
   **Šablony řešení:** některé šablony může obsahovat malé 3 MB. Soubor virtuálního pevného disku s názvem **fixed3.vhd**. Není nutné importovat do služby Azure Stack. Fixed3.VHD.  Tento soubor je součástí některých šablony řešení požadavků pro publikování na webu Azure Marketplace.

   Zkontrolujte popis šablony a stáhly a naimportovaly další požadavky, jako jsou virtuální pevné disky, které jsou vyžadována pro práci s šablonu řešení.  
   
   **Informace o rozšíření:** při práci s příponami image virtuálního počítače použijte následující parametry:
   - *Publisher*
   - *Typ*
   - *Verze*  

   Je velmi riskantní používat *nabízejí* pro rozšíření.   


4.  Použití Powershellu k publikování položky marketplace do služby Azure Stack pomocí **přidat AzsGalleryItem** rutiny. Příklad:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Po publikování položky galerie je teď k dispozici pro použití. Pokud chcete potvrdit, publikování položky galerie, přejděte na **všechny služby**a potom v části **Obecné** vyberte **Marketplace**.  Při stahování šablony řešení, ujistěte se, že přidáte všechny závislé image virtuálního pevného disku pro danou šablonu řešení.  
  ![Zobrazení marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Verze Azure Stack Powershellu 1.3.0 nyní můžete přidat rozšíření virtuálního počítače.

Příklad:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Další postup
[Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)