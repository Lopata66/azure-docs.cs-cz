---
title: Provádění operací Azure Table storage pomocí Powershellu | Dokumentace Microsoftu
description: Provádění operací Azure Table storage pomocí Powershellu.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: b1cae7dc553ce324349e66f1bcb8a281d7c7c7e0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995593"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Provádění operací Azure Table storage pomocí Azure Powershellu 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage je úložištěm dat typu NoSQL, které můžete použít k ukládání a dotazování obrovských sad strukturovaných, nerelačních dat. Hlavní součástí služby jsou tabulky, entit a vlastnosti. Tabulka je kolekce entit. Entita je sada vlastností. Každá entita může mít až 252 vlastností, které jsou všechny dvojice název hodnota. Tento článek předpokládá, že jste už obeznámení s koncepty služby Azure Table Storage. Podrobné informace najdete v tématu [Principy datového modelu služby Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) a [Začínáme s Azure Table storage pomocí .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Tento článek popisuje běžné operace Azure Table storage. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načíst tabulku
> * Přidání tabulky entit
> * Dotaz na tabulku
> * Odstraňovat tabulkové entity
> * Odstranění tabulky

Tento článek ukazuje, jak vytvořit nový účet úložiště Azure do nové skupiny prostředků, takže je můžete snadno odebrat po dokončení. Pokud místo toho můžete využít existující účet úložiště, můžete to udělat místo.

V příkladech vyžadují moduly Powershellu Az `Az.Storage (1.1.0 or greater)` a `Az.Resources (1.2.0 or greater)`. V okně Powershellu, spusťte `Get-Module -ListAvailable Az*` k vyhledání verze. Pokud se nezobrazí nebo je potřeba upgradovat, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Pomocí této funkce Azure z prostředí PowerShell vyžaduje, abyste měli `Az` nainstalovaným modulem. Aktuální verze `AzTable` není kompatibilní s starší modul AzureRM.
> Postupujte podle [nainstalovat nejnovější pokyny pro instalaci modulu Az](/powershell/azure/install-az-ps) v případě potřeby.

Po instalaci prostředí Azure PowerShell nebo aktualizaci, musíte nainstalovat modul **AzTable**, který obsahuje příkazy pro správu entity. Chcete-li nainstalovat tento modul, spusťte PowerShell jako správce a použijte **Install-Module** příkazu.

> [!IMPORTANT]
> Pro modul název kompatibility důvodů, proč stále publikujeme Tento stejný modul v rámci starý název `AzureRmStorageTables` v galerii prostředí PowerShell. Tento dokument bude odkazovat pouze nový název.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Add-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Tyto příklady používají **eastus**. Store tuto hodnotu do proměnné **umístění** pro budoucí použití.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Název skupiny prostředků Store do proměnné pro budoucí použití. V tomto příkladu skupina prostředků s názvem *pshtablesrg* se vytvoří v *eastus* oblasti.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořit účet úložiště úrovně standard pro obecné účely s využitím místně redundantní úložiště (LRS) [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nezapomeňte zadat název účtu úložiště jedinečný. Potom získejte kontext, který představuje účet úložiště. Když používáte v účtu úložiště, můžete odkazovat na kontext namísto opakovaného zadávání svoje přihlašovací údaje.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Vytvořit novou tabulku

Chcete-li vytvořit tabulku, použijte [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) rutiny. V tomto příkladu je volána v tabulce `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Načíst seznam tabulek v účtu úložiště

Načíst seznam tabulek v účtu úložiště pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Načtěte odkaz na konkrétní tabulky

K provádění operací v tabulce, potřebujete odkaz na konkrétní tabulku. Získat odkaz pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Referenční dokumentace CloudTable vlastnosti konkrétní tabulky

> [!IMPORTANT]
> Využití CloudTable je povinný, pokud pracujete s **AzTable** modul prostředí PowerShell. Volání **Get-AzTableTable** příkazu získejte odkaz na tento objekt. Pokud ještě neexistuje, tento příkaz také vytvoří v tabulce.

K provádění operací na tabulku s využitím **AzTable**, potřebujete odkaz na vlastnost CloudTable konkrétní tabulky.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud chcete odstranit tabulku, použijte [odebrat AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Tato rutina odebere tabulky, včetně všechna jeho data.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili nový účet skupiny a úložiště prostředků na začátku tento návod, můžete odebrat všechny prostředky, které jste vytvořili v tomto cvičení odstraněním skupiny prostředků. Tento příkaz odstraní všechny prostředky v rámci skupiny, jakož i samotnou skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto článku s postupy jste se dozvěděli o běžných operací Azure Table storage s využitím Powershellu, také se naučíte: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načíst tabulku
> * Přidání tabulky entit
> * Dotaz na tabulku
> * Odstraňovat tabulkové entity
> * Odstranění tabulky

Další informace naleznete v následujících článcích

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage#storage)

* [Práce s tabulkami Azure powershellu – v2.0 modulu AzureRmStorageTable/AzTable PS](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
