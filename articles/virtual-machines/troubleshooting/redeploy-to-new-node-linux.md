---
title: Znovu nasadit virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Jak znovu nasadit virtuální počítače s Linuxem v Azure a zmírnění problémů s připojením SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116938"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Znovu nasadit virtuální počítač s Linuxem do nového uzlu Azure
Pokud čelí potíží při řešení potíží s SSH nebo přístup k aplikaci pro virtuální počítač s Linuxem (VM) v Azure, znovu se nasazuje virtuální počítač může pomoci. Při opětovném nasazování virtuálního počítače, přesune virtuální počítač do nového uzlu v rámci infrastruktury Azure a pak ji zpět zapne. Možnosti konfigurace a přidružené prostředky zůstanou zachovány. Tento článek ukazuje, jak znovu nasadit virtuální počítač pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

> [!NOTE]
> Po opětovném nasazování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují. 


## <a name="use-the-azure-cli"></a>Použití Azure CLI
Nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index).

Opětovné nasazení virtuálního počítače s [opětovné nasazení virtuálního počítače v rámci az](/cli/azure/vm). Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Použití Azure classic CLI
Nainstalujte [nejnovější verzi Azure CLI classic](../../cli-install-nodejs.md) a přihlaste se ke svému účtu Azure. Ujistěte se, že jste v režimu Resource Manageru (`azure config mode arm`).

Následující příklad znovu nasadí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, můžete najít nápovědu na [řešení potíží s připojením SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [podrobné kroky pro řešení potíží SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pokud nelze získat přístup k aplikaci spuštěné na virtuálním počítači, můžete si také přečíst [řešení potíží s aplikací](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


