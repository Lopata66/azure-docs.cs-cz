---
title: Výstupy v šablonách
description: Popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: ec96b45cdc5ccf488d46c2d8da03caf16d002dfa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622845"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Výstupy v šabloně Azure Resource Manager

Tento článek popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager. Výstupy použijete, když potřebujete vrátit hodnoty z nasazených prostředků.

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

V části výstupy můžete podmíněně vracet hodnotu. V případě [podmíněného nasazení](conditional-resource-deployment.md) prostředku se obvykle používá podmínka ve výstupech. Následující příklad ukazuje, jak podmíněně vracet ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Jednoduchý příklad podmíněného výstupu naleznete v tématu [podmíněná výstupní šablona](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamický počet výstupů

V některých scénářích neznáte počet instancí hodnoty, které potřebujete vrátit při vytváření šablony. Můžete vrátit proměnný počet hodnot pomocí elementu **copy** .

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

Další informace najdete v tématu [výstup iterace v šablonách Azure Resource Manager](copy-outputs.md).

## <a name="linked-templates"></a>Propojené šablony

K načtení výstupní hodnoty z propojené šablony použijte [odkazovou](template-functions-resource.md#reference) funkci v nadřazené šabloně. Syntaxe v nadřazené šabloně je:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Při získávání výstupu vlastnost z propojené šablony, název vlastnosti nemůže obsahovat čárku.

Následující příklad ukazuje, jak nastavit IP adresu v nástroji pro vyrovnávání zatížení načtením hodnoty z propojené šablony.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Funkci `reference` nelze použít v oddílu Outputs [vnořené šablony](linked-templates.md#nested-template). Na návratové hodnoty pro nasazený prostředek ve vnořené šablony, převeďte vnořené šablony na propojenou šablonu.

## <a name="get-output-values"></a>Získat výstupní hodnoty

Po úspěšném nasazení se výstupní hodnoty automaticky vrátí do výsledků nasazení.

Chcete-li získat výstupní hodnoty z historie nasazení, můžete použít skript.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Příklad šablony

Následující příklady ukazují scénáře použití výstupů.

|Šablona  |Popis  |
|---------|---------|
|[Kopírovat proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří proměnné komplexní a vrací tyto hodnoty. Nenasadí žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Obsahuje odkazy na předchozí šablonu postupem. Při vytváření nástroje pro vyrovnávání zatížení, používá ID prostředku ve výstupu. |

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech výstupů naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
