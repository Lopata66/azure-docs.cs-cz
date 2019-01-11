---
title: Správa úložiště v Azure nezávislé cloudů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Správa úložiště v Číně, cloudu pro státní správu a německého cloudu s využitím Azure Powershellu
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: f7d5fcf1905200bc214a3ff42db9b7b511768dd0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214891"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Správa úložiště v Azure nezávislé cloudů pomocí Powershellu

Většina lidí pomocí veřejného cloudu Azure pro globální nasazení v Azure. Existují také některá nezávislé nasazení Microsoft Azure z důvodů suverenity a tak dále. Tato nezávislé nasazení jsou označovány jako "prostředí". Následující seznam obsahuje podrobnosti o nezávislé cloudy, které jsou aktuálně k dispozici.

* [Cloud Azure Government](https://azure.microsoft.com/features/gov/)
* [Cloud Azure China, provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Německého cloudu Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Díky nezávislé cloudu 

Používání Azure Storage v jednom z cloudů nezávislé, připojit s tímto cloudem místo Azure veřejné. Chcete použít jeden z nezávislé cloudy spíše než veřejné Azure:

* Můžete zadat *prostředí* pro připojení.
* Určení a používají dostupné oblasti.
* Můžete použít příponu správný koncový bod, který se liší od Azure veřejné.

V příkladech vyžadují Azure PowerShell verze modulu Az 0.7 nebo novější. V okně Powershellu, spusťte `Get-Module -ListAvailable Az` k vyhledání verze. Pokud se objevuje nic, nebo je potřeba upgradovat, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Spustit [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) rutiny zobrazíte dostupné prostředí Azure:
   
```powershell
Get-AzEnvironment
```

Přihlaste se ke svému účtu, který má přístup do cloudu, ke kterému chcete připojit a nastavte prostředí. Tento příklad ukazuje, jak přihlášení k účtu, který používá cloudu Azure Government.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Pro přístup k Číně, použijte prostředí **AzureChinaCloud**. Chcete-li získat přístup k německého cloudu, použijte **AzureGermanCloud**.

V tomto okamžiku, pokud potřebujete seznam umístění pro vytvoření účtu úložiště nebo jiný prostředek, můžete dát dotaz na umístění k dispozici pro vybraný cloud pomocí [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

Následující tabulka uvádí umístění pro německý cloud.

|Umístění | Zobrazovaný název |
|----|----|
| germanycentral | Německo – střed|
| germanynortheast | Německo – severovýchod | 


## <a name="endpoint-suffix"></a>Přípona koncového bodu

Přípona koncového bodu pro každou z těchto prostředí se liší od Azure veřejný koncový bod. Přípona koncového bodu objektu blob pro veřejný Azure je třeba **blob.core.windows.net**. U cloudu státní správy přípona koncového bodu objektu blob je **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azenvironment"></a>Získání koncového bodu pomocí Get-AzEnvironment 

Načíst pomocí koncového bodu přípona [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Koncový bod je *StorageEndpointSuffix* vlastnost prostředí. Následující fragmenty kódu ukazují, jak to udělat. Všechny tyto příkazy vracely něco jako "core.cloudapp.net" nebo "core.cloudapi.de" atd. Nový kód přidejte do služby storage pro přístup k této službě. Například "queue.core.cloudapi.de" se přístup ke službě fronty v německém cloudu.

Tento fragment kódu načte všechna prostředí a přípona koncového bodu pro každé z nich.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Tento příkaz vrátí následující výsledky.

| Název| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | Core.usgovcloudapi.NET |

Chcete-li načíst všechny vlastnosti pro zadané prostředí, zavolejte **Get-AzEnvironment** a zadejte název cloudu. Tento fragment kódu vrátí seznam hodnot vlastnosti; Vyhledejte **StorageEndpointSuffix** v seznamu. Následující příklad je určený pro německého cloudu.

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

Výsledky jsou podobné následujícímu:

|Property Name|Hodnota|
|----|----|
| Název | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

K načtení jenom vlastnost přípona úložiště koncového bodu, načtení konkrétní cloud a požádejte o právě tuto jednu vlastnost.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Vrátí následující informace.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Získání koncového bodu z účtu úložiště

Můžete také prozkoumat vlastnosti účtu úložiště načíst koncových bodů. To může být užitečný v případě, že už používáte účet úložiště ve vašem skriptu prostředí PowerShell; můžete načíst jenom koncový bod, které potřebujete. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Pro účet úložiště v cloudu pro státní správu vrátí následující: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po nastavení prostředí

Z tady do budoucna, můžete použít stejné prostředí PowerShell použít ke správě vašich účtů úložiště a přístup k rovině dat, jak je popsáno v článku [pomocí Azure Powershellu s Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků a účet úložiště pro toto cvičení, můžete odebrat všechny prostředky odstraněním skupiny prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účet úložiště, který jste vytvořili a samotnou skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

* [Zachování přihlášení uživatele napříč relacemi Powershellu](/powershell/azure/context-persistence)
* [Úložiště Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Příručka pro vývojáře Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Poznámky pro vývojáře pro aplikace Azure (Čína)](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentace k Azure Germany](../../germany/germany-welcome.md)
