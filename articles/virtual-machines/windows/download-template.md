---
title: Stažení šablony pro virtuální počítač Azure
description: Stáhněte si šablonu pro virtuální počítač pomocí portálu nebo PowerShellu.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82085378"
---
# <a name="download-the-template-for-a-vm"></a>Stažení šablony pro virtuální počítač
Když vytvoříte virtuální počítač v Azure pomocí portálu nebo PowerShellu, automaticky se vytvoří šablona Správce prostředků. Tuto šablonu můžete použít k rychlému duplikování nasazení. Šablona obsahuje informace o všech prostředcích ve skupině prostředků. V případě virtuálního počítače to znamená, že šablona obsahuje vše, co je vytvořeno v rámci podpory virtuálního počítače v dané skupině prostředků, včetně síťových prostředků.

## <a name="download-the-template-using-the-portal"></a>Stažení šablony pomocí portálu
1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **Virtual Machines**.
3. Ze seznamu vyberte virtuální počítač.
4. Vyberte **Exportovat šablonu**.
5. V nabídce v horní části vyberte **Stáhnout** a uložte soubor. zip do svého místního počítače.
6. Otevřete soubor. zip a extrahujte soubory do složky. Soubor. zip obsahuje:
   
   * parameters.jsna
   * template.jsna

template.jssoubor je šablona.

## <a name="download-the-template-using-powershell"></a>Stažení šablony pomocí PowerShellu
Soubor šablony. JSON můžete také stáhnout pomocí rutiny [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Pomocí `-path` parametru můžete zadat název souboru a cestu k souboru. JSON. Tento příklad ukazuje, jak stáhnout šablonu pro skupinu prostředků s názvem **myResourceGroup** do složky **C:\users\public\downloads** na místním počítači.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Další kroky
Další informace o nasazení prostředků pomocí šablon naleznete v tématu [Správce prostředků návodu k šablonám](../../azure-resource-manager/resource-manager-template-walkthrough.md).

