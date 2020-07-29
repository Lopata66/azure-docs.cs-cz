---
title: Vytvořte účet úložiště objektů blob bloku – Azure Storage | Microsoft Docs
description: Ukazuje, jak vytvořit účet Azure BlockBlobStorage s charakteristikou výkonu Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 31a7048c263d7231fe827ad5a1c927c5cb3e2f6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463548"
---
# <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

Typ účtu BlockBlobStorage umožňuje vytvářet objekty blob bloku s charakteristikami výkonu Premium. Tento typ účtu úložiště je optimalizovaný pro úlohy s vysokými sazbami transakcí nebo, které vyžadují velmi rychlý přístup krát. Tento článek ukazuje, jak vytvořit účet BlockBlobStorage pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell.

> [!NOTE]
> Funkce hierarchického oboru názvů v účtu úložiště objektů blob bloku je ve verzi Public Preview a je dostupná v USA – východ, USA – východ 2, USA – střed, USA (střed) – jih, USA – západ 2, Velká Británie – jih, Kanadě Central a Austrálii východní oblasti. Pokud chcete zkontrolovat omezení, přečtěte si téma [funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) a [známé problémy](data-lake-storage-known-issues.md). Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adlspremiumonboard).

Další informace o účtech BlockBlobStorage najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Žádné

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek s postupem vyžaduje Azure PowerShell modul AZ verze 1.2.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

- Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.
- Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal klikněte na tlačítko **Cloud Shell** :

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s návody:

[![Snímek obrazovky zobrazující okno Cloud Shell na portálu](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Tento článek s návody vyžaduje, abyste spustili Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

## <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete v Azure Portal vytvořit účet BlockBlobStorage, postupujte následovně:

1. V Azure Portal vyberte **všechny služby** > kategorii **úložiště** > **účty úložiště**.

2. V části **účty úložiště**vyberte **Přidat**.

3. V poli **předplatné** vyberte předplatné, ve kterém chcete účet úložiště vytvořit.

4. V poli **Skupina prostředků** vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**a zadejte název nové skupiny prostředků.

5. Do pole **název účtu úložiště** zadejte název účtu. Pamatujte na následující pokyny:

   - Název musí být v rámci Azure jedinečný.
   - Název musí být dlouhý 3 až 24 znaků.
   - Název může obsahovat jenom číslice a malá písmena.

6. V poli **umístění** vyberte umístění pro účet úložiště nebo použijte výchozí umístění.

7. V případě zbývajících nastavení proveďte následující konfiguraci:

   |Pole     |Hodnota  |
   |---------|---------|
   |**Výkon**    |  Vyberte **Premium**.   |
   |**Druh účtu**    | Vyberte **BlockBlobStorage**.      |
   |**Replikace**    |  Ponechte výchozí nastavení **místně redundantního úložiště (LRS)**.      |

   ![Zobrazuje uživatelské rozhraní portálu pro vytvoření účtu úložiště objektů blob bloku.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Vyberte kartu **Upřesnit** .

9. Pokud chcete optimalizovat účet úložiště pro analýzu dat, nastavte **hierarchický obor názvů** na **Enabled**. V opačném případě ponechte tuto možnost nastavenou na výchozí hodnotu.

   Další informace najdete v tématu [Úvod do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > Funkce hierarchického oboru názvů v účtu úložiště objektů blob bloku je ve verzi Public Preview a je dostupná v USA – východ, USA – východ 2, USA – střed, USA (střed) – jih, USA – západ 2, Velká Británie – jih, Kanadě Central a Austrálii východní oblasti. Pokud chcete zkontrolovat omezení, přečtěte si téma [funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) a [známé problémy](data-lake-storage-known-issues.md). Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adlspremiumonboard).

8. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení účtu úložiště.

9. Vyberte **Vytvořit**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce).

2. Spusťte následující příkaz, abyste se ujistili, že `Az` je nainstalovaná nejnovější verze modulu PowerShellu.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Otevřete novou konzolu PowerShellu a přihlaste se pomocí svého účtu Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. V případě potřeby vytvořte novou skupinu prostředků. Hodnoty v uvozovkách nahraďte a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Vytvořte účet BlockBlobStorage. Hodnoty v uvozovkách nahraďte a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Pokud chcete optimalizovat účet úložiště pro analýzu dat, přidejte `-EnableHierarchicalNamespace $True` ho do příkazu. Další informace najdete v tématu [Úvod do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > Funkce hierarchického oboru názvů v účtu úložiště objektů blob bloku je ve verzi Public Preview a je dostupná v USA – východ, USA – východ 2, USA – střed, USA (střed) – jih, USA – západ 2, Velká Británie – jih, Kanadě Central a Austrálii východní oblasti. Pokud chcete zkontrolovat omezení, přečtěte si téma [funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) a [známé problémy](data-lake-storage-known-issues.md). Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adlspremiumonboard).

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit účet bloku BLOB pomocí rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat rozhraní příkazového řádku Azure CLI v. 2.0.46 nebo novější verze. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Přihlaste se ke svému předplatnému Azure.

   ```azurecli
   az login
   ```

2. V případě potřeby vytvořte novou skupinu prostředků. Hodnoty v závorkách (včetně závorek) nahraďte a spusťte následující příkaz.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Vytvořte účet BlockBlobStorage. Hodnoty v závorkách (včetně závorek) nahraďte a spusťte následující příkaz.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Pokud chcete optimalizovat účet úložiště pro analýzu dat, přidejte `--hierarchical-namespace true` ho do příkazu. Další informace najdete v tématu [Úvod do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > Funkce hierarchického oboru názvů v účtu úložiště objektů blob bloku je ve verzi Public Preview a je dostupná v USA – východ, USA – východ 2, USA – střed, USA (střed) – jih, USA – západ 2, Velká Británie – jih, Kanadě Central a Austrálii východní oblasti. Pokud chcete zkontrolovat omezení, přečtěte si téma [funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) a [známé problémy](data-lake-storage-known-issues.md). Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adlspremiumonboard).
   
---

## <a name="next-steps"></a>Další kroky

- Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
