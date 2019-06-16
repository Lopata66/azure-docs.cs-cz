---
title: Nasazení služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, jak nasadit Azure File Sync, a to od začátku do konce.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0913e1877c63ed1a8e960676be02a12b45a34a7d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240098"
---
# <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure
Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, NFS a FTPS. Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

Důrazně doporučujeme, abyste si přečetli [plánování nasazení služby soubory Azure](storage-files-planning.md) a [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) před provedením kroků popsaných v tomto článku.

## <a name="prerequisites"></a>Požadavky
* Sdílené složky Azure ve stejné oblasti, kterou chcete nasadit Azure File Sync. Další informace naleznete v tématu:
    - [Dostupnost v oblastech](storage-sync-files-planning.md#region-availability) pro Azure File Sync.
    - [Vytvoření sdílené složky](storage-how-to-create-file-share.md) podrobný popis toho, jak vytvořit sdílenou složku.
* Alespoň jeden podporované instance systému Windows Server nebo cluster s Windows serverem, synchronizaci se službou Azure File Sync. Další informace o podporovaných verzích systému Windows Server najdete v tématu [vzájemná funkční spolupráce s Windows serverem](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Modul Az PowerShell lze pomocí prostředí PowerShell 5.1 nebo Powershellu 6 +. Pro všechny podporované systémy, včetně systémů než Windows, ale rutinu pro registraci serveru musí být vždy spuštěný přímo na instanci Windows serveru, kterou registrujete může použít modul Az Powershellu pro Azure File Sync. Ve Windows serveru 2012 R2, můžete ověřit, že používáte alespoň prostředí PowerShell 5.1. \* prohlédněte hodnoty **PSVersion** vlastnost **$PSVersionTable** objektu:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Pokud vaše PSVersion hodnotu menší než 5.1. \*, budou v případě většiny novou instalací systému Windows Server 2012 R2, budete moct snadno upgradovat stažením a instalací [Windows Management Frameworku (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Odpovídající balíček ke stažení a instalaci pro Windows Server 2012 R2 je **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6 + lze použít libovolné podporované systémem a je možné stáhnout prostřednictvím jeho [stránku Githubu](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Pokud máte v plánu použít uživatelské rozhraní serveru registrace, spíše než registrace přímo z prostředí PowerShell, je nutné použít prostředí PowerShell 5.1.

* Pokud jste se rozhodli použít prostředí PowerShell 5.1, ujistěte se, že na je nainstalované minimálně rozhraní .NET 4.7.2. Další informace o [rozhraní .NET Framework verze a závislosti](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) ve vašem systému.
* Modul Az PowerShell, kterou můžete instalovat podle zde uvedených pokynů: [Instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
* Modul Az.StorageSync, který je aktuálně nainstalované nezávisle na modulu Az:

    ```PowerShell
    Install-Module Az.StorageSync -AllowClobber
    ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava Windows Serveru na použití se Synchronizací souborů Azure
Pro každý server, který chcete používat s Azure File Sync, včetně každého uzlu serveru v clusteru převzetí služeb při selhání, zakázat **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**. To se vyžaduje jenom pro počáteční server registrace. Po zaregistrování serveru můžete tuto možnost znovu povolit.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
1. Otevřete správce serveru.
2. Klikněte na tlačítko **místní Server**:  
    !["Místního serveru" v levé části uživatelského rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. V podokně **Vlastnosti** vyberte odkaz **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
    ![V podokně "Konfigurace rozšířeného zabezpečení" v Uživatelském rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. V **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** dialogu **vypnout** pro **správci** a **uživatelé**:  
    ![Pop – okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s "Off" vybraná](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete zakázat rozšířeného zabezpečení aplikace Internet Explorer, spusťte z relace Powershellu se zvýšenými oprávněními následující:

```powershell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Microsoft Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby synchronizace úložiště 
Nasazení služby Azure File Sync začíná umístěním **služba synchronizace úložiště** prostředků do skupiny prostředků vybraného předplatného. Doporučujeme, abyste podle potřeby zřizování jako málo z nich. Vytvoříte vztah důvěryhodnosti mezi servery a tento prostředek a serveru lze registrovat pouze do jedné služby synchronizace úložiště. V důsledku toho se doporučuje nasadit tolik služby synchronizace úložiště potřebujete k oddělení skupin serverů. Mějte na paměti, že servery z jiného úložiště služby synchronizace nemůže synchronizovat mezi sebou.

> [!Note]
> Služba synchronizace úložiště Zdědit z předplatného a skupiny prostředků, které byly nasazeny do přístupová oprávnění. Doporučujeme důkladně zkontrolovat kdo má přístup k němu. Entity s oprávněním k zápisu může provést synchronizaci nové sady souborů ze serverů zaregistrovaných do tohoto úložiště synchronizační služba a způsobit, že datový tok do úložiště Azure, který je přístupný na ně.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete nasadit službu synchronizace úložiště, přejděte na [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko *vytvořit prostředek* a vyhledejte Azure File Sync. Ve výsledcích hledání vyberte **Azure File Sync**a pak vyberte **vytvořit** otevřít **nasadit synchronizaci úložiště** kartu.

V podokně, které se otevře, zadejte následující informace:

- **Název**: Jedinečný název (pro každé předplatné) služby synchronizace úložiště.
- **Předplatné**: Předplatné, ve kterém chcete vytvořit službu synchronizace úložiště. V závislosti na strategie konfigurace vaší organizace může mít přístup k jedné nebo více odběrů. Předplatné Azure je nejzákladnější kontejner fakturace pro jednotlivé cloudové služby (například soubory Azure).
- **Skupina prostředků**: Skupina prostředků je logická skupina prostředků Azure, jako je účet úložiště nebo služba synchronizace úložiště. Můžete vytvořit novou skupinu prostředků nebo použijte existující skupinu prostředků pro Azure File Sync. (Doporučujeme použít skupiny prostředků jako kontejnery a izolovat prostředky logicky pro vaši organizaci, jako je například seskupení HR prostředků nebo prostředky pro konkrétní projekt.)
- **Umístění**: Oblast, ve kterém chcete nasazení služby Azure File Sync. Pouze podporované oblasti jsou k dispozici v tomto seznamu.

Až budete hotovi, vyberte **vytvořit** nasadit službu synchronizace úložiště.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Nahraďte **< Az_Region >** , **< RG_Name >** , a **< my_storage_sync_service >** pomocí vlastní hodnoty, pak pomocí následujících příkazů vytvořte a nasaďte Služba synchronizace úložiště:

```powershell
Connect-AzAccount

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Agenta z si můžete stáhnout [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování, klikněte dvakrát na balíček MSI, který chcete spustit instalaci agenta Azure File Sync.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem převzetí služeb při selhání, musíte na všech uzlech v clusteru nainstalovat agenta Azure File Sync. Každý uzel v clusteru musí být zaregistrovaný pro práci s Azure File Sync.

Doporučujeme, abyste udělali toto:
- Ponechte výchozí instalační cesta (C:\Program Files\Azure\StorageSyncAgent), zjednodušit Poradce při potížích a správu serveru.
- Aktivaci služby Microsoft Update k zajištění aktuálnosti Azure File Sync. Všechny aktualizace agenta Azure File Sync, včetně aktualizace funkcí a opravy hotfix, dojde k ze služby Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizaci Azure File Sync. Další informace najdete v tématu [zásady aktualizace Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta Azure File Sync rozhraní registrace serveru se automaticky otevře. Služba synchronizace úložiště musí mít před registrací; viz následující část o tom, jak vytvořit službu synchronizace úložiště.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující kód Powershellu správnou verzi agenta Azure File Sync pro váš operační systém stáhnout a nainstalovat do vašeho systému.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem převzetí služeb při selhání, musíte na všech uzlech v clusteru nainstalovat agenta Azure File Sync. Každý uzel v clusteru musí být zaregistrovaný pro práci s Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrace Windows Serveru ve službě synchronizace úložiště
Registrací vašeho Windows Serveru ve službě synchronizace úložiště se vytvoří vztah důvěryhodnosti mezi vaším serverem (nebo clusterem) a službou synchronizace úložiště. Každý server je možné zaregistrovat pouze k jedné službě synchronizace úložiště a může se synchronizovat s ostatními servery a sdílenými složkami Azure přidruženými ke stejné službě synchronizace úložiště.

> [!Note]
> Registrace serveru používá přihlašovací údaje Azure k vytvoření vztahu důvěryhodnosti mezi službou synchronizace úložiště a systému Windows Server, ale následně serveru vytváří a používá svůj vlastní identity, která je platná tak dlouho, dokud zůstává registrovaného serveru a platnost aktuálního tokenu sdíleného přístupového podpisu (SAS úložiště). Nový token SAS nelze mu vystavit na server po zrušit, proto odebrání serveru možnost přístupu k vaší sdílené složky Azure, zastavuje se všechny synchronizace serveru.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Uživatelské rozhraní serveru registrace otvírat automaticky po instalaci agenta Azure File Sync. Pokud tomu tak není, lze jej otevřít ručně z umístění souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Když se otevře uživatelské rozhraní serveru registrace, vyberte **přihlášení** začít.

Po přihlášení, zobrazí se výzva pro následující informace:

![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure**: Předplatné obsahující službu synchronizace úložiště (viz [nasadit službu synchronizace úložiště](#deploy-the-storage-sync-service)). 
- **Skupina prostředků**: Skupina prostředků, která obsahuje službu synchronizace úložiště.
- **Služba synchronizace úložiště**: Název služby synchronizace úložiště, který chcete zaregistrovat.

Po výběru příslušné informace, vyberte **zaregistrovat** k dokončení registrace serveru. V rámci procesu registrace se zobrazí výzva k dalšímu přihlášení.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvoření skupiny synchronizace a koncového bodu cloudu
Skupina synchronizace definuje topologii synchronizace sady souborů. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure, a jeden nebo několik koncových bodů serveru. Koncový bod serveru představuje cestu na registrovaného serveru. Server může mít koncové body serveru v několika skupinách synchronizace. Můžete vytvořit libovolný počet skupin synchronizace podle musíte vhodně popsat topologii požadované synchronizace.

Koncový bod cloudu je ukazatel na sdílené složky Azure. Všechny koncové body serveru budou synchronizovat s koncovým bodem cloudové, vytváření koncového bodu cloudu rozbočovače. Účet úložiště pro sdílené složky Azure se musí nacházet ve stejné oblasti jako služba synchronizace úložiště. Podkladové sdílené složky Azure se synchronizují s jednou výjimkou: Speciální složky srovnatelné skryté složky "System Volume Information" na svazku NTFS, se zřídí. Tento adresář se nazývá ". SystemShareInformation". Obsahuje důležité synchronizovat metadata, která nebudou synchronizovány do dalších koncových bodů. Použití nebo ho odstranit.

> [!Important]  
> Můžete provádět změny libovolný koncový bod v cloudu nebo koncový bod serveru ve skupině synchronizace a soubory se synchronizovalo s ostatní koncové body ve skupině synchronizace. Pokud provedete změny ke koncovým bodům cloudu (Azure file share) přímo, nutné změny nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jenom jednou za 24 hodin se spustí úloha zjišťování změn. Další informace najdete v tématu [soubory Azure – nejčastější dotazy](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Chcete-li vytvořit skupinu synchronizace v [webu Azure portal](https://portal.azure.com/), přejděte do služby synchronizace úložiště a pak vyberte **+ skupina synchronizace**:

![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře, zadejte následující informace pro vytvoření skupiny synchronizace s koncovým bodem cloudu:

- **Název skupiny synchronizace**: Název skupiny synchronizace, který se má vytvořit. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl.
- **Předplatné**: Předplatné, kam jste nasadili službu synchronizace úložiště v [nasadit službu synchronizace úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště**: Pokud vyberete **vyberte účet úložiště**, zobrazí se další podokno, ve kterém můžete vybrat účet úložiště obsahující sdílenou složku Azure, který chcete synchronizovat s.
- **Sdílené složky Azure**: Název sdílené složky Azure se kterým chcete synchronizovat.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit skupinu synchronizace, spusťte následující příkaz Powershellu. Nezapomeňte nahradit `<my-sync-group>` s požadovaným názvem skupiny synchronizace.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Jakmile se skupina synchronizace se úspěšně vytvořil, můžete vytvořit váš koncový bod cloudu. Nezapomeňte nahradit `<my-storage-account>` a `<my-file-share>` s očekávané hodnoty.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Vytvoření koncového bodu serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru. Koncový bod serveru musí být cesta na registrovaném serveru (spíše než připojené sdílené složky) a pokud chcete použít vrstvení cloudu, cesta musí být na svazku bez systému. Úložiště připojené k síti (NAS) se nepodporuje.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Chcete-li přidat koncový bod serveru, přejděte do nově vytvořeného synchronizace skupiny a pak vyberte **přidat koncový bod serveru**.

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

- **Registrovaný server**: Název serveru nebo clusteru, ve kterém chcete vytvořit koncový bod serveru.
- **Cesta**: Windows Server cesta k synchronizovat jako součást skupiny synchronizace.
- **Vrstvení cloudu**: Přepnout na povolit nebo zakázat cloudu ovládání datových vrstev. S cloudem ovládání datových vrstev, zřídka používají nebo používaných souborů můžete Vrstvená do soubory Azure.
- **Volné místo na svazku**: Množství volného místa vyhradit na svazku, na kterém je umístěn koncový bod serveru. Například pokud volné místo na svazku je nastavena na 50 % na svazku, který má koncový bod jeden server, přibližně polovina množství dat je Vrstvená do soubory Azure. Bez ohledu na to, jestli cloud ovládání datových vrstev je povoleno, sdílené složky Azure vždy obsahuje úplnou kopii dat ve skupině synchronizace.

Chcete-li přidat koncový bod serveru **vytvořit**. Soubory jsou teď udržovat synchronizované sdílené složky Azure a Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující příkazy prostředí PowerShell k vytvoření koncového bodu serveru a nezapomeňte nahradit `<your-server-endpoint-path>` a `<your-volume-free-space>` s požadované hodnoty.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Registrace pomocí služby Azure File Sync
Doporučený postup pro připojení v Azure File Sync první z nich s nula výpadek při zachování úplnou věrnost a seznam řízení přístupu (ACL) jsou následující:
 
1. Nasazení služby synchronizace úložiště.
2. Vytvořte skupinu synchronizace.
3. Instalace agenta Azure File Sync na serveru s plnou datovou sadou.
4. Zaregistrujte tento server a vytvořte koncový bod serveru ve sdílené složce. 
5. Umožní synchronizaci provést úplné nahrát do sdílené složky Azure (koncový bod v cloudu).  
6. Po dokončení počáteční nahrávání nainstalujte agenta Azure File Sync na všechny ostatní servery.
7. Vytvořte nové sdílené složky na všechny zbývající servery.
8. Vytvořte koncové body serveru v nové sdílené složky pomocí zásad vrstvení cloudu, v případě potřeby. (Tento krok vyžaduje další úložiště k dispozici pro počáteční instalaci.)
9. Nechte agenta Azure File Sync provést rychlé obnovení úplné oboru názvů bez převodu skutečná data. Po synchronizaci úplný obor názvů vyplní synchronizační modul na místním disku na základě zásad vrstvení cloudu pro koncový bod serveru. 
10. Ujistěte se synchronizace dokončí a testování vaší topologii, podle potřeby. 
11. Přesměrování uživatelů a aplikací do této nové sdílené složky.
12. Volitelně můžete odstranit všechny duplicitní sdílených složek na serverech.
 
Pokud není k dispozici dodatečné úložiště pro počáteční připojení a chcete se připojit k existující sdílené složky, můžete předvyplnění dat ve sdílených složek Azure. Tento přístup je určeno pouze v případě můžete přijmout výpadky a zaručit vůbec žádné změny dat na sdíleních serveru během počátečního připojování. 
 
1. Ujistěte se, že data na žádném serveru nelze změnit během procesu registrace.
2. Předem osivo sdílených složek Azure s daty serveru využít libovolný nástroj pro přenos dat přes SMB třeba Robocopy s přímým přístupem kopírování SMB. Protože nástroj AzCopy není načtení dat prostřednictvím SMB, nelze ji použít pro předem synchronizace replik indexů.
3. Vytvoření s koncovými body požadovanému serveru odkazující na existující sdílené složky Azure File Sync topologie.
4. Umožnit synchronizaci, dokončete proces sloučení pro všechny koncové body. 
5. Po odsouhlasení hotový, můžete otevřít sdílené složky pro změny.
 
Uvědomte si, že v současné době předem synchronizace replik indexů přístup má několik omezení- 
- Nezachová se plnou věrností na souborech. Například soubory ztratit seznamy řízení přístupu a časová razítka.
- Změny dat na serveru před topologie synchronizace je plně nahoru a je spuštěn, může způsobit konflikty na koncové body serveru.  
- Po vytvoření koncového bodu cloudu Azure File Sync spustí proces pro zjištění souborů v cloudu před zahájením počáteční synchronizace. Čas potřebný k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupnou šířku pásma a počet souborů a složek. Pro hrubý odhad ve verzi preview spustí proces zjišťování přibližně za 10 soubory.  Proto i v případě, že před fast synchronizace replik indexů spuštění, celkový čas získat plně systému může být výrazně déle, pokud jsou předem dosazená data v cloudu.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace replikace DFS (DFS-R) nasazení do Azure File Sync
Migrace systému souborů DFS-R nasazení do Azure File Sync:

1. Vytvořte skupinu synchronizace k reprezentaci topologie systému souborů DFS-R, který chcete nahradit.
2. Spusťte na serveru, který má úplnou sadu dat ve vaší topologii systému souborů DFS-R na migraci. Azure File Sync nainstalujte na tomto serveru.
3. Zaregistrujte tento server a vytvořte koncový bod serveru pro první server chcete migrovat. Nepovolujte cloudu ovládání datových vrstev.
4. Umožnit všem synchronizaci dat do sdílené složky Azure (koncový bod v cloudu).
5. Instalace a registrace agenta Azure File Sync na všechny zbývající servery systému souborů DFS-R.
6. Zakázat systému souborů DFS-R. 
7. Vytvoření koncového bodu serveru na všech serverech systému souborů DFS-R. Nepovolujte cloudu ovládání datových vrstev.
8. Ujistěte se synchronizace dokončí a testování vaší topologii, podle potřeby.
9. Vyřazení z provozu systému souborů DFS-R.
10. Květen vrstvení cloudu teď možné povolit na libovolný koncový bod serveru podle potřeby.

Další informace najdete v tématu [spolupráce Azure File Sync s distribuovaného systému souborů (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Další postup
- [Přidání nebo odebrání koncového bodu Azure File Sync serveru](storage-sync-files-server-endpoint.md)
- [Zaregistrovat nebo zrušit registraci serveru pomocí služby Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorování Azure File Sync](storage-sync-files-monitoring.md)
