---
title: Přehled agenta virtuálního počítače Azure | Dokumentace Microsoftu
description: Přehled agenta virtuálního počítače Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: d17d7c9d7b57e6ca040e4f81c9665789c8bc26e2
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483246"
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled služby Azure agenta virtuálního počítače
Agent virtuálního počítače Microsoft Azure (VM Agent) je zabezpečeného a nenáročného proces, který spravuje interakci virtuálních počítačů (VM) s Kontrolerem prostředků infrastruktury Azure. Agent virtuálního počítače má primární roli při povolování a spouštění rozšíření virtuálního počítače Azure. Rozšíření virtuálních počítačů povolit konfiguraci po nasazení virtuálního počítače, jako je například instalací a konfigurací softwaru. Rozšíření virtuálních počítačů povolit také funkce pro obnovení, jako je resetování hesla pro správu virtuálního počítače. Rozšíření virtuálních počítačů nelze spustit bez agenta virtuálního počítače Azure.

Tento článek podrobně popisuje instalaci, detekci a odebrání agenta virtuálního počítače Azure.

## <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače

### <a name="azure-marketplace-image"></a>Image z Azure Marketplace

Agent virtuálního počítače Azure je nainstalovaný ve výchozím nastavení na virtuálních počítačích Windows nasazené z image Azure Marketplace. Při nasazování image Azure Marketplace z portálu, Powershellu, rozhraní příkazového řádku nebo šablony Azure Resource Manageru, je také nainstalován Agent virtuálního počítače Azure.

Balíček Windows hostovaného agenta je rozdělen do dvou částí:

- Zřizování agenta (PA)
- Agent hosta Windows (WinGA)

Ke spuštění virtuálního počítače musí mít nainstalovaný na virtuálním počítači PA, ale WinGA nemusí být nainstalovaný. Na virtuální počítač nasadit času, můžete vybrat neinstalovat WinGA. Následující příklad ukazuje, jak vybrat *provisionVmAgent* možnost pomocí šablony Azure Resource Manageru:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Pokud nemáte nainstalovaní agenti, nelze použít některé služby Azure, jako je Azure Backup nebo zabezpečení Azure. Tyto služby vyžadují rozšíření k instalaci. Pokud jste nasadili virtuální počítač bez WinGA, můžete nainstalovat nejnovější verzi agenta později.

### <a name="manual-installation"></a>Ruční instalace
Agent virtuálního počítače Windows můžete ručně nainstalovat balíček Instalační služby systému Windows. Ruční instalace může být nutné, když vytvoříte vlastní image virtuálního počítače, který je nasazený do Azure. Pro ruční instalaci agenta virtuálního počítače Windows [stáhnout instalační program agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789).

Poklikáním soubor Instalační služby systému Windows lze nainstalovat agenta virtuálního počítače. Pro automatizované nebo bezobslužné instalace agenta virtuálního počítače spusťte následující příkaz:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Zjišťování agenta virtuálního počítače

### <a name="powershell"></a>PowerShell

Modul Powershellu pro Azure Resource Manager lze použít k načtení informací o virtuálních počítačích Azure. Pokud chcete zobrazit informace o virtuálním počítači, jako je například stav zřizování agenta virtuálního počítače Azure, použijte [rutiny Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Následujícímu zhuštěnému příkladu výstup ukazuje *ProvisionVMAgent* vlastnost vnořit do *OSProfile*. Tuto vlastnost lze použít k určení, pokud agent virtuálního počítače byla nasazena do virtuálního počítače:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Tento skript je možné se seznam stručné názvy virtuálních počítačů a stav agenta virtuálního počítače:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ručního zjišťování

Při přihlášení k virtuálnímu počítači s Windows, Správce úloh je možné ke kontrole spuštěné procesy. Vyhledat agenta virtuálního počítače Azure, otevřete Správce úloh, klikněte na tlačítko *podrobnosti* kartu a najděte název procesu **WindowsAzureGuestAgent.exe**. Přítomnost tento proces se označuje, že je nainstalovaný agent virtuálního počítače.


## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního počítače
Dojde k automatickému upgradu Azure VM Agent pro Windows. Při nasazování nových virtuálních počítačů do Azure, obdrží nejnovější agent virtuálního počítače během zřizování virtuálních počítačů. Vlastních imagí virtuálních počítačů je třeba ručně aktualizovat zahrnout nového agenta virtuálního počítače v okamžiku vytvoření bitové kopie.


## <a name="next-steps"></a>Další postup
Další informace o rozšíření virtuálních počítačů najdete v tématu [přehled funkcí a rozšíření virtuálních počítačů Azure](overview.md).
