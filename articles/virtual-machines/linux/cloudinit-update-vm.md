---
title: Použití Cloud-init na virtuálním počítači se systémem Linux v Azure
description: Jak pomocí Cloud-init aktualizovat a nainstalovat balíčky na virtuálním počítači Linux během vytváření pomocí Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969145"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Použití Cloud-init k aktualizaci a instalaci balíčků na virtuálním počítači Linux v Azure
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) aktualizovat balíčky na virtuálním počítači (VM) se systémem Linux nebo ve službě Virtual Machine Scale Sets při zřizování v Azure. Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure. Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="update-a-vm-with-cloud-init"></a>Aktualizace virtuálního počítače pomocí Cloud-init
Z bezpečnostních důvodů možná budete chtít nakonfigurovat virtuální počítač tak, aby při prvním spuštění používal nejnovější aktualizace. Protože Cloud-init funguje napříč různými distribucey Linux, není nutné pro správce balíčků zadávat `apt` ani `yum`. Místo toho definujete `package_upgrade` a povolíte procesu Cloud-init určení vhodného mechanismu pro distribuce, který se používá. Tento pracovní postup umožňuje použít stejné skripty Cloud-init napříč distribuce.

Chcete-li zobrazit proces upgradu v akci, vytvořte v aktuálním prostředí soubor s názvem *cloud_init_upgrade. txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_upgrade.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor Cloud-init pomocí `--custom-data cloud_init_upgrade.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazeného ve výstupu z předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Spusťte nástroj pro správu balíčků a vyhledejte aktualizace.

```bash
sudo yum update
```

V případě, že je u Cloud-init zaškrtnuté a nainstalované aktualizace při spuštění, nemusíte používat žádné další aktualizace.  Můžete se podívat na proces aktualizace, počet změněných balíčků a instalaci `httpd` spuštěním `yum history` a zkontrolovat výstup podobný tomu níže.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
