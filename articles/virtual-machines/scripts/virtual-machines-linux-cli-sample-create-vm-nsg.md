---
title: 'Ukázka CLI: vytvoření dvou virtuálních počítačů s interním a externím NSG'
description: Vytvoření dvou virtuálních počítačů s interními a externími NSG pro zabezpečení síťového provozu pomocí Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b4339ab2c512a96614158f673cb07b60184f5f71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459946"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Zabezpečený síťový provoz mezi virtuálními počítači pomocí NSG

Tento skript vytvoří dva virtuální počítače a zabezpečí příchozí provoz do obou z nich. Jeden virtuální počítač je přístupný na internetu a má nakonfigurovanou skupinu zabezpečení sítě (NSG), která povoluje provoz na portu 22 a 80. Druhý virtuální počítač není přístupný na internetu a má nakonfigurovanou skupinu NSG, která povoluje pouze provoz z prvního virtuálního počítače.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a podsíť. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Vytvoří podsíť. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Vrátí informace o pravidle skupiny zabezpečení sítě. V této ukázce se název pravidla uloží do proměnné pro použití v pozdější části skriptu. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Aktualizuje pravidlo NSG. V této ukázce se back-endové pravidlo aktualizuje tak, aby přes něj procházel pouze provoz z front-endové podsítě. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
