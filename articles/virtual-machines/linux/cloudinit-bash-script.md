---
title: Použití Cloud-init ke spuštění skriptu bash v virtuálním počítači Linux v Azure
description: Použití Cloud-init ke spuštění skriptu bash na virtuálním počítači Linux během vytváření pomocí Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969218"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Použití Cloud-init ke spuštění skriptu bash na virtuálním počítači Linux v Azure
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) spustit stávající skript bash na virtuálním počítači (VM) se systémem Linux nebo ve službě Virtual Machine Scale Sets (VMSS) v době zřizování v Azure. Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure. Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="run-a-bash-script-with-cloud-init"></a>Spuštění skriptu bash pomocí Cloud-init
Pomocí Cloud-init nemusíte převádět stávající skripty do cloudové konfigurace. Cloud-init akceptuje více vstupních typů, jedna z nich je bash skript.

Pokud jste ke spouštění skriptů použili rozšíření Azure Custom Script pro Linux, můžete je migrovat na použití Cloud-init. Rozšíření Azure ale obsahuje integrované vytváření sestav, které vám upozorní na selhání skriptování. nasazení image s inicializací cloudu se nezdaří, pokud se skript nezdaří.

Chcete-li zobrazit tuto funkci v akci, vytvořte jednoduchý skript bash pro testování. Podobně jako soubor Cloud- `#cloud-config` init musí být tento skript místní, ke kterému budete mít k disazure CLI příkazy pro zřízení virtuálního počítače.  V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor simple_bash.sh` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor `--custom-data simple_bash.sh` skriptu bash následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ověření spuštění skriptu bash
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazeného ve výstupu z předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Přejděte do adresáře **adresáře/TMP** a ověřte, zda existuje soubor MyScript. txt a zda obsahuje příslušný text v něm.  Pokud tomu tak není, můžete si prohlédnout **/var/log/Cloud-init.log** , kde najdete další podrobnosti.  Vyhledejte následující položku:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
