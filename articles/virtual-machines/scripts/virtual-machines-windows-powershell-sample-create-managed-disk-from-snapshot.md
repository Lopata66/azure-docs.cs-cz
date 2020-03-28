---
title: Vytvoření spravovaného disku ze snímku – ukázka prostředí PowerShell
description: 'Ukázkový skript Azure PowerShellu: Vytvoření spravovaného disku ze snímku'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: e6652f8ab8e1134d7f348eaa7c8fc20f1a41a46f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75368546"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Vytvoření spravovaného disku ze snímku pomocí PowerShellu

Tento skript vytvoří spravovaný disk ze snímku. Použijte ho k obnovení virtuálního počítače ze snímků disku s operačním systémem a datového disku. Z odpovídajících snímků vytvořte disk s operačním systémem a datový disk a pak připojením spravovaných disků vytvořte nový virtuální počítač. Připojením datových disků vytvořených ze snímků můžete také obnovit datové disky existujícího virtuálního počítače.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku ze snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Získat-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Načte vlastnosti snímku.  |
| [Nový-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Vytvoří konfiguraci disku, která se použije při vytvoření disku. Bude obsahovat ID prostředku nadřazeného snímku, stejné umístění, jako má nadřazený snímek, a typ úložiště.  |
| [Nový-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Vytvoří disk pomocí konfigurace disku, názvu disku a názvu skupiny prostředků, které použije jako parametry. |


## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače ze spravovaného disku](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
