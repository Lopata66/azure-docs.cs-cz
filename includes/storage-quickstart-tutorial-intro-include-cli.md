---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169615"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště pro obecné účely vytvoříte příkazem [az storage account create](/cli/azure/storage/account). Účet úložiště pro obecné účely můžete použít pro všechny čtyři služby: objekty blob, soubory, tabulky a fronty. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Zadání přihlašovacích údajů účtu úložiště

Azure CLI pro většinu příkazů v tomto kurzu potřebuje přihlašovací údaje účtu úložiště. Existuje několik možností, jak to udělat. Jeden z nejjednodušších způsobů je nastavit proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY`.

Nejprve zobrazte klíče účtu úložiště pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Nyní nastavte proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY`. To můžete provést v prostředí Bash pomocí příkazu `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
