---
title: Jak označit prostředek virtuálního počítače Windows v Azure | Dokumentace Microsoftu
description: Další informace o označování Windows virtuální počítač vytvořený v Azure s využitím modelu nasazení Resource Manageru
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: eef4681626c5e0aa0c5d8a67dbd0d19bcfd7121e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108287"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak označit virtuální počítač s Windows v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač s Windows v Azure prostřednictvím modelu nasazení Resource Manager. Značky jsou páry klíč/hodnota definovaná uživatelem, které je možné použít přímo na prostředek nebo skupinu prostředků. Azure v současné době podporuje až 15 značek na prostředek a skupinu prostředků. Značky může být umístěné na zdroje v době vytvoření nebo přidat do existujícího prostředku. Všimněte si, že značky jsou podporovány pro prostředky vytvořené prostřednictvím modelu nasazení Resource Manager pouze. Pokud chcete označit virtuální počítač s Linuxem, přečtěte si téma [jak označit virtuální počítač s Linuxem v Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Označování pomocí Powershellu
Pokud chcete vytvořit, přidání a odstranění značky prostřednictvím prostředí PowerShell, je potřeba nejdřív nastavit vaši [prostředí PowerShell s Azure Resource Managerem][PowerShell environment with Azure Resource Manager]. Po dokončení instalace můžete umístit značky u prostředků Compute, Network a Storage při vytváření nebo po vytvoření prostředku pomocí Powershellu. Tento článek se zaměří na zobrazení a úpravy značky umístěn na virtuálních počítačích.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Nejprve přejděte k virtuálnímu počítači prostřednictvím `Get-AzVM` rutiny.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Pokud váš virtuální počítač už obsahuje značky, zobrazí se všechny značky pro váš prostředek:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Pokud chcete přidat značky prostřednictvím prostředí PowerShell, můžete použít `Set-AzResource` příkazu. Poznámka: při aktualizaci značky prostřednictvím prostředí PowerShell, značky se aktualizují jako celek. Proto pokud chcete přidat jednu značku k prostředku, který již má značky, je potřeba zahrnout všechny značky, které chcete umístit na prostředek. Níže je příklad toho, jak přidat další značky na prostředek prostřednictvím rutin Powershellu.

Tato rutina první nastaví všechny uvedené značky, umístí na *MyTestVM* k *$tags* proměnné, používat `Get-AzResource` a `Tags` vlastnost.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Druhý příkaz zobrazí značky pro danou proměnnou.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

V třetím příkazu přidá další značka, které *$tags* proměnné. Všimněte si použití **+=** připojit nový pár klíč/hodnota do *$tags* seznamu.

        PS C:\> $tags += @{Location="MyLocation"}

Čtvrtý příkaz nastaví všechny značky, které jsou definovány v *$tags* proměnné pro daný prostředek. V takovém případě je MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Pátý příkaz zobrazí všechny značky na prostředek. Jak je vidět, *umístění* je teď definovaný jako značku s *MyLocation* jako hodnotu.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Další informace o označování pomocí prostředí PowerShell, podívejte se [rutiny Azure Resource][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další postup
* Další informace o označování prostředků Azure najdete v tématu [přehled Azure Resource Manageru] [ Azure Resource Manager Overview] a [pomocí značek k uspořádání prostředků Azure] [ Using Tags to organize your Azure Resources].
* Jak značky vám pomůže se správou vašeho využití prostředků Azure najdete v tématu [vysvětlení vašeho vyúčtování Azure] [ Understanding your Azure Bill] a [získání přehledů o spotřebě prostředků Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
