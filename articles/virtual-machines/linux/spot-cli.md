---
title: Použití rozhraní příkazového řádku k nasazení virtuálních počítačů Azure
description: Naučte se používat rozhraní příkazového řádku k nasazení virtuálních počítačů Azure na místě, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: f8515668cc30f62edadc0975533fe3bb9646e5e5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288381"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Nasazení virtuálních počítačů s přímým použitím Azure CLI

Použití [virtuálních počítačů Azure](spot-vms.md) se dá využít při využití naší nevyužité kapacity při výrazném úsporách nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena za virtuální počítač na místě se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [virtuální počítače – ceny](spot-vms.md#pricing).

Postup vytvoření virtuálního počítače s využitím rozhraní příkazového řádku Azure CLI je stejný, jak je uvedeno v [článku rychlý Start](./quick-create-cli.md). Stačí přidat parametr--priority – nastavte na `--eviction-policy` buď zrušit přidělení (Toto je výchozí nastavení) `Delete` , nebo zadejte Max Price nebo `-1` . 


## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Pokud chcete vytvořit virtuální počítače se systémem, musíte mít spuštěnou verzi Azure CLI 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

Přihlaste se k Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Vytvoření virtuálního počítače s přímým sebou

Tento příklad ukazuje, jak nasadit virtuální počítač se systémem Linux, který nebude vyřazení na základě ceny. Zásada vyřazení je nastavená tak, aby se virtuální počítač nastavil jako neplatný, aby se mohl později restartovat. Pokud chcete odstranit virtuální počítač a příslušný disk, když je virtuální počítač vyřazený, nastavte `--eviction-policy` na `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Po vytvoření virtuálního počítače se můžete dotazovat, aby se zobrazila maximální fakturovaná cena za všechny virtuální počítače ve skupině prostředků.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Další kroky**

Můžete také vytvořit virtuální počítač s přímým použitím pomocí [Azure PowerShell](../windows/spot-powershell.md), [portálu](../windows/spot-portal.md)nebo [šablony](spot-template.md).

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
