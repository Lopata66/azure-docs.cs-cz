---
title: Použití Cloud-init k nastavení názvu hostitele pro virtuální počítač Linux v Azure
description: Použití Cloud-init k přizpůsobení virtuálního počítače se systémem Linux během vytváření pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: adac9dc41aaba7ce6bfd9f01917d647174b41282
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036716"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Použití Cloud-init k nastavení názvu hostitele pro virtuální počítač Linux v Azure
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat konkrétní název hostitele na virtuálním počítači (VM) nebo Virtual Machine Scale Sets (VMSS) v době zřizování v Azure. Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure. Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="set-the-hostname-with-cloud-init"></a>Nastavení názvu hostitele pomocí Cloud-init
Ve výchozím nastavení je název hostitele stejný jako název virtuálního počítače při vytváření nového virtuálního počítače v Azure.  Pokud chcete spustit skript Cloud-init pro změnu tohoto výchozího názvu hostitele při vytváření virtuálního počítače v Azure pomocí [AZ VM Create](/cli/azure/vm), zadejte soubor Cloud-init s přepínačem `--custom-data`.  

Chcete-li zobrazit proces upgradu v akci, vytvořte v aktuálním prostředí soubor s názvem *cloud_init_hostname. txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_hostname.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
hostname: myhostname
```

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor Cloud-init pomocí `--custom-data cloud_init_hostname.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po vytvoření Azure CLI zobrazí informace o virtuálním počítači. Použijte `publicIpAddress` k vašemu VIRTUÁLNÍmu počítači pomocí SSH. Následujícím způsobem zadejte vlastní adresu:

```bash
ssh <publicIpAddress>
```

Chcete-li zobrazit název virtuálního počítače, použijte příkaz `hostname` následujícím způsobem:

```bash
hostname
```

Virtuální počítač by měl nahlásit název hostitele jako hodnotu nastavenou v souboru Cloud-init, jak je znázorněno v následujícím příkladu výstupu:

```bash
myhostname
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
