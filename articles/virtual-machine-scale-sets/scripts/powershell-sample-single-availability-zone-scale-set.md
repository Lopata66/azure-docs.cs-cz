---
title: Ukázky Azure PowerShellu – Škálovací sada s jednou zónou | Microsoft Docs
description: Ukázky Azure PowerShellu
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: c46f295873751b2bee870da280fb432eda892460
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467154"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Vytvoření škálovací sady virtuálních počítačů s jednou zónou pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 v jedné zóně dostupnosti. Po spuštění skriptu můžete k virtuálnímu počítači přistupovat přes protokol RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Vytvoří škálovací sadu virtuálních počítačů a všechny podpůrné prostředky, včetně virtuální sítě, nástroje pro vyrovnávání zatížení a pravidel překladu adres. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Získá informace o škálovací sadě virtuálních počítačů. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Přidá do virtuálního počítače rozšíření vlastních skriptů pro instalaci základní webové aplikace. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aktualizuje model škálovací sady virtuálních počítačů, aby se použilo rozšíření virtuálního počítače. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../powershell-samples.md).