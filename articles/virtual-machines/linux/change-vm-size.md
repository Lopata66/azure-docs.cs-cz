---
title: Postup změny velikosti virtuálního počítače se systémem Linux pomocí Azure CLI
description: Postup horizontálního navýšení nebo snížení kapacity virtuálního počítače se systémem Linux změnou velikosti virtuálního počítače.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969266"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Změna velikosti virtuálního počítače se systémem Linux pomocí Azure CLI 

Po zřízení virtuálního počítače můžete virtuální počítač škálovat nahoru nebo dolů změnou [velikosti virtuálního počítače][vm-sizes]. V některých případech je nutné nejprve zrušit přidělení virtuálního počítače. Pokud požadovaná velikost není k dispozici v hardwarovém clusteru, který je hostitelem virtuálního počítače, musíte zrušit přidělení virtuálního počítače. Tento článek podrobně popisuje, jak změnit velikost virtuálního počítače se systémem Linux pomocí Azure CLI. 

## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače
Pokud chcete změnit velikost virtuálního počítače, potřebujete nainstalovat nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

1. Prohlédněte si seznam dostupných velikostí virtuálních počítačů v hardwarovém clusteru, na kterém je virtuální počítač hostovaný, pomocí [AZ VM list-VM-Resize-Options](/cli/azure/vm). V následujícím příkladu jsou uvedené velikosti virtuálních počítačů pro virtuální počítač s názvem `myVM` ve skupině prostředků `myResourceGroup` oblasti:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Pokud je požadovaná velikost virtuálního počítače uvedená, změňte velikost virtuálního počítače pomocí [AZ VM Resize](/cli/azure/vm). Následující příklad změní velikost virtuálního počítače s názvem `myVM` na `Standard_DS3_v2` velikost:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Během tohoto procesu se virtuální počítač restartuje. Po restartování se existující operační systém a datové disky přemapují. Cokoli na dočasném disku ztratí.

3. Pokud požadovaná velikost virtuálního počítače není v seznamu uvedená, musíte nejdřív zrušit přidělení virtuálního počítače pomocí [AZ VM unallocate](/cli/azure/vm). Tento proces umožňuje změnit velikost virtuálního počítače na jakoukoli dostupnou velikost, kterou oblast podporuje a pak začala. Následující kroky zruší přidělení, změní velikost a potom spustí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Zrušení přidělení virtuálního počítače taky uvolní všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Operační systém a datové disky nejsou ovlivněny.

## <a name="next-steps"></a>Další kroky
Pro další škálovatelnost spusťte více instancí virtuálních počítačů a nahorizontální navýšení kapacity. Další informace najdete v tématu [Automatické škálování počítačů se systémem Linux v sadě škálování virtuálního počítače][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
