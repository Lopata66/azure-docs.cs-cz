---
title: Resetování přístupu k virtuálnímu počítači Azure Linux
description: Správa uživatelů s právy pro správu a obnovení přístupu k virtuálním počítačům se systémem Linux pomocí rozšíření VMAccess a rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073605"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Správa uživatelů s právy pro správu, SSH a kontroly nebo opravy disků na virtuálních počítačích se systémem Linux pomocí rozšíření VMAccess pomocí Azure CLI
## <a name="overview"></a>Přehled
Disk na VIRTUÁLNÍm počítači se systémem Linux zobrazuje chyby. Nechtěně jste obnovili kořenové heslo pro virtuální počítač se systémem Linux nebo omylem odstranili privátní klíč SSH. Pokud se to stalo v průběhu dnů datového centra, je potřeba, abyste si vyvolali a pak otevřeli KVM a získali se v konzole serveru. Jako tento přepínač KVM si můžete představit rozšíření Azure VMAccess, které umožňuje přístup k konzole, aby se obnovil přístup k systému Linux nebo prováděla údržbu na úrovni disku.

V tomto článku se dozvíte, jak pomocí rozšíření Azure VMAccess vyhledat nebo opravit disk, resetovat uživatelský přístup, spravovat účty správců nebo aktualizovat konfiguraci SSH v systému Linux, když běží jako Azure Resource Manager virtuálních počítačů. Pokud potřebujete spravovat klasické virtuální počítače, můžete postupovat podle pokynů uvedených v [dokumentaci k klasickému virtuálnímu počítači](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Pokud použijete rozšíření VMAccess k resetování hesla virtuálního počítače po instalaci přihlašovacího rozšíření služby AAD, budete muset znovu spustit přihlašovací rozšíření AAD a znovu povolit přihlášení AAD pro váš počítač.

## <a name="prerequisites"></a>Požadavky
### <a name="operating-system"></a>Operační systém

Rozšíření pro přístup k VIRTUÁLNÍm počítačům lze spustit u těchto distribucí systému Linux:

| Distribuce | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS a 12,04 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 +, 7.1 + |
| Oracle Linux | 6.4+, 7.0+ |
| SuSE | 11 a 12 |
| OpenSuse | openSUSE přestupné 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Způsoby použití rozšíření VMAccess
Existují dva způsoby, jak můžete rozšíření VMAccess použít na virtuálních počítačích se systémem Linux:

* Použijte rozhraní příkazového řádku Azure a požadované parametry.
* [Použijte nezpracované soubory JSON, které proces rozšíření VMAccess](#use-json-files-and-the-vmaccess-extension) a pak zachovají.

V následujících příkladech se používají příkazy [AZ VM User](/cli/azure/vm/user) . K provedení těchto kroků potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Aktualizovat klíč SSH
Následující příklad aktualizuje klíč SSH pro uživatele `azureuser` na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Poznámka:** Příkaz `az vm user update` připojí nový text veřejného klíče k souboru `~/.ssh/authorized_keys` pro uživatele s oprávněními správce na virtuálním počítači. Tato funkce nenahrazuje ani neodebírá žádné existující klíče SSH. Tato akce neodebere předchozí klíče nastavené v době nasazení nebo následné aktualizace prostřednictvím rozšíření VMAccess.

## <a name="reset-password"></a>Resetovat heslo
Následující příklad obnoví heslo pro `azureuser` uživatele na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Restartovat SSH
Následující příklad restartuje démon SSH a resetuje konfiguraci SSH na výchozí hodnoty na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Vytvoření administrativního/sudoho uživatele
Následující příklad vytvoří uživatele s názvem `myNewUser` s oprávněními **sudo** . Účet používá ke ověřování na virtuálním počítači s názvem `myVM`klíč SSH. Tato metoda je navržená tak, aby vám pomohla znovu získat přístup k virtuálnímu počítači v případě ztráty nebo zapomenutí aktuálních přihlašovacích údajů. Osvědčeným postupem je, že účty s oprávněními **sudo** by měly být omezené.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Odstranění uživatele
Následující příklad odstraní uživatele s názvem `myNewUser` na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Použití souborů JSON a rozšíření VMAccess
V následujících příkladech jsou použity nezpracované soubory JSON. Pomocí [AZ VM Extension set nastavte](/cli/azure/vm/extension) a potom zavolejte své soubory JSON. Tyto soubory JSON je taky možné volat ze šablon Azure. 

### <a name="reset-user-access"></a>Resetovat přístup uživatelů
Pokud jste ztratili přístup k kořenovému adresáři na svém VIRTUÁLNÍm počítači se systémem Linux, můžete spustit skript VMAccess a aktualizovat klíč SSH uživatele nebo heslo.

Chcete-li aktualizovat veřejný klíč SSH uživatele, vytvořte soubor s názvem `update_ssh_key.json` a přidejte nastavení v následujícím formátu. Pro parametry `username` a `ssh_key` nahraďte vlastní hodnoty:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Pokud chcete resetovat heslo uživatele, vytvořte soubor s názvem `reset_user_password.json` a přidejte nastavení v následujícím formátu. Pro parametry `username` a `password` nahraďte vlastní hodnoty:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Restartovat SSH
Pokud chcete spustit démona SSH a resetovat konfiguraci SSH na výchozí hodnoty, vytvořte soubor s názvem `reset_sshd.json`. Přidejte následující obsah:

```json
{
  "reset_ssh": true
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Správa administrativních uživatelů

Chcete-li vytvořit uživatele s oprávněními **sudo** , která k ověřování používají klíč SSH, vytvořte soubor s názvem `create_new_user.json` a přidejte nastavení v následujícím formátu. Pro parametry `username` a `ssh_key` nahraďte vlastní hodnoty. Tato metoda je navržená tak, aby vám pomohla znovu získat přístup k virtuálnímu počítači v případě ztráty nebo zapomenutí aktuálních přihlašovacích údajů. Osvědčeným postupem je, že účty s oprávněními **sudo** by měly být omezené.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Pokud chcete uživatele odstranit, vytvořte soubor s názvem `delete_user.json` a přidejte následující obsah. Pro parametr `remove_user` nahraďte vlastní hodnotu:

```json
{
  "remove_user":"myNewUser"
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Ověřit nebo opravit disk
Pomocí VMAccess můžete také ověřit a opravit disk, který jste přidali do virtuálního počítače se systémem Linux.

Pokud chcete disk ověřit a opravit, vytvořte soubor s názvem `disk_check_repair.json` a přidejte nastavení v následujícím formátu. Pro název `repair_disk`nahraďte svou vlastní hodnotou:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Spusťte skript VMAccess s:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
