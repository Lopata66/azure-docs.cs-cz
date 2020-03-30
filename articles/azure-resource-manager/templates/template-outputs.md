---
title: Výstupy v šablonách
description: Popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460020"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Výstupy v šabloně Azure Resource Manager

Tento článek popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager. Výstupy se používají, když potřebujete vrátit hodnoty z nasazených prostředků.

## <a name="define-output-values"></a>Definovat výstupní hodnoty

Následující příklad ukazuje, jak vrátit ID prostředku pro veřejnou IP adresu:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Podmíněný výstup

V části výstupy můžete podmíněně vrátit hodnotu. Obvykle se používá podmínka ve výstupech, když jste [podmíněně nasadili](conditional-resource-deployment.md) prostředek. Následující příklad ukazuje, jak podmíněně vrátit ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová adresa:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Jednoduchý příklad podmíněného výstupu naleznete v [tématu podmíněná výstupní šablona](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamický počet výstupů

V některých scénářích neznáte počet instancí hodnoty, které je třeba vrátit při vytváření šablony. Proměnný počet hodnot můžete vrátit pomocí elementu **copy.**

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Další informace najdete [v tématu Iterace výstupů v šablonách Azure Resource Manager .](copy-outputs.md)

## <a name="linked-templates"></a>Propojené šablony

Chcete-li načíst výstupní hodnotu z propojené šablony, použijte [referenční](template-functions-resource.md#reference) funkci v nadřazené šabloně. Syntaxe v nadřazené šabloně je:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Při získávání výstupní vlastnosti z propojené šablony nemůže název vlastnosti obsahovat pomlčku.

Následující příklad ukazuje, jak nastavit adresu IP v systému vyrovnávání zatížení načtením hodnoty z propojené šablony.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

`reference` Funkci nelze použít v části výstupy [vnořené šablony](linked-templates.md#nested-template). Chcete-li vrátit hodnoty pro nasazený prostředek ve vnořené šabloně, převeďte vnořenou šablonu na propojenou šablonu.

## <a name="get-output-values"></a>Získat výstupní hodnoty

Po úspěšném nasazení jsou výstupní hodnoty automaticky vráceny ve výsledcích nasazení.

Chcete-li získat výstupní hodnoty z historie nasazení, můžete použít skript.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Ukázkové šablony

Následující příklady ukazují scénáře pro použití výstupů.

|Šablona  |Popis  |
|---------|---------|
|[Kopírování proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří složité proměnné a výstupy těchto hodnot. Nenasazuje žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vyvede ID prostředku. |
|[Vyrovnávání zatížení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Odkazy na předchozí šablonu. Při vytváření správce zatížení použije ID prostředku ve výstupu. |

## <a name="next-steps"></a>Další kroky

* Informace o dostupných vlastnostech pro výstupy najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
