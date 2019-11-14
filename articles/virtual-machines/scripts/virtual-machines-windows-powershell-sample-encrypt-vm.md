---
title: Ukázkový skript Azure PowerShellu – Šifrování virtuálního počítače s Windows
description: Ukázkový skript Azure PowerShellu – Šifrování virtuálního počítače s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: c16c212cdf5854110a3fae1bf7fbb01592137629
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038908"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Šifrování virtuálního počítače s Windows pomocí Azure PowerShellu

Tento skript vytvoří zabezpečenou službu Azure Key Vault, šifrovací klíče, instanční objekt Azure Active Directory a virtuální počítač s Windows. Virtuální počítač se pak zašifruje pomocí šifrovacího klíče ze služby Key Vault a přihlašovacích údajů instančního objektu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky: |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | Vytvoří službu Azure Key Vault pro ukládání zabezpečených dat, jako jsou šifrovací klíče. |
| [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Vytvoří šifrovací klíč ve službě Key Vault. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | Vytvoří instanční objekt Azure Active Directory pro zabezpečené ověřování a řízení přístupu k šifrovacím klíčům. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Nastaví oprávnění ke službě Key Vault tak, že instančnímu objektu udělí přístup k šifrovacím klíčům. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině zabezpečení sítě. Tento příkaz také otevře port 80 a nastaví přihlašovací údaje pro správu. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Získá požadované informace o službě Key Vault. |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | Povolí šifrování virtuálního počítače pomocí přihlašovacích údajů instančního objektu a šifrovacího klíče. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Zobrazí stav procesu šifrování virtuálního počítače. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
