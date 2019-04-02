---
title: Ukázkový – použít značku a její výchozí hodnotu
description: Tato definice zásady ukázka připojí zadaný název značky a hodnoty, pokud, který není k dispozici značky.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 36947eae2c134761f61459dac37b324c03bd85dd
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804017"
---
# <a name="sample---apply-tag-and-its-default-value"></a>Ukázkový – použít značku a její výchozí hodnotu

Tyto zásady připojí název a hodnotu zadané značky v případě, že se žádná značka neposkytne. Zadáte název a hodnotu značky, které se mají použít.

K nasazení této ukázkové zásady můžete použít:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Ukázková zásada

### <a name="policy-definition"></a>Definice zásady

Kompletní složená definice zásady JSON, která se používá v rozhraní REST API, pro tlačítka Nasadit do Azure a při ručním vytváření na portálu.

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> Pokud zásadu vytváříte ručně na portálu, použijte části **properties.parameters** a **properties.policyRule** uvedené výše. Obě části zabalte pomocí složených závorek `{}`, aby byl kód JSON platný.

### <a name="policy-rules"></a>Pravidla zásady

Kód JSON definující pravidla zásady, který se používá v Azure CLI a Azure PowerShellu.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zásad

Kód JSON definující parametry zásady, který se používá Azure CLI a Azure PowerShellu.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Název |Type |Pole |Popis |
|---|---|---|---|
|tagName |String |tags |Název značky, třeba costCenter|
|tagValue |String |tags |Hodnota značky, třeba headquarter|

Při vytváření přiřazení přes PowerShell nebo Azure CLI je možné předat hodnoty parametrů ve formátu JSON buď v řetězci, nebo prostřednictvím souboru pomocí parametru `-PolicyParameter` (PowerShell) nebo `--params` (Azure CLI).
PowerShell podporuje také parametr `-PolicyParameterObject`, který vyžaduje, aby se rutině předala zatřiďovací tabulka názvů a hodnot, kde **název** je název parametru a **hodnota** je jedna hodnota nebo pole hodnot, které se předávají během přiřazení.

V tomto ukázkovém parametru se definuje vlastnost _tagName_ s hodnotou **costCenter** a vlastnost _tagValue_ s hodnotou **headquarter**.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>portál Azure

[![Nasazení ukázkové zásady Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![nasazení ukázkové zásady pro Azure Government](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Odebrání pomocí Azure PowerShellu

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Vysvětlení Azure PowerShellu

Ve skriptech nasazení a odebrání se používají následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Vytvoří novou definici Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Získá jednu skupinu prostředků. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Odebere stávající přiřazení Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Odebere stávající definici Azure Policy. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Odebrání pomocí Azure CLI

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Vysvětlení Azure CLI

| Příkaz | Poznámky |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Vytvoří novou definici Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Získá jednu skupinu prostředků. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Odebere stávající přiřazení Azure Policy. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Odebere stávající definici Azure Policy. |

Při práci s rozhraním REST API Resource Manageru můžete použít několik nástrojů, jako je [ARMClient](https://github.com/projectkudu/ARMClient) nebo PowerShell. Příklad volání rozhraní REST API z PowerShellu najdete v části **Aliasy** v článku věnovaném [struktuře definic zásad](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>REST API

### <a name="deploy-with-rest-api"></a>Nasazení pomocí rozhraní REST API

- Vytvořte definici zásady (v oboru předplatného). Jako text požadavku použijte JSON s [definicí zásady](#policy-definition).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- Vytvořte přiřazení zásady (v oboru skupiny prostředků).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  Jako text požadavku použijte následující příklad JSON:

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Odebrání pomocí rozhraní REST API

- Odebrání přiřazení zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- Odebrání definice zásady

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Vysvětlení rozhraní REST API

| Služba | Skupina | Operace | Poznámky |
|---|---|---|---|
| Správa prostředků | Definice zásad | [Vytvoření](/rest/api/resources/policydefinitions/createorupdate) | Vytvoří v předplatném novou definici Azure Policy. Ve zkratce: [Vytvořit ve skupině pro správu](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Správa prostředků | Přiřazení zásad | [Vytvoření](/rest/api/resources/policyassignments/create) | Vytvoří nové přiřazení Azure Policy. V tomto příkladu zadáme definici, příkaz ale může převzít také iniciativu. |
| Správa prostředků | Přiřazení zásad | [Odstranění](/rest/api/resources/policyassignments/delete) | Odebere stávající přiřazení Azure Policy. |
| Správa prostředků | Definice zásad | [Odstranění](/rest/api/resources/policydefinitions/delete) | Odebere stávající definici Azure Policy. Ve zkratce: [Odstranit ve skupině pro správu](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Další postup

- Prohlédněte si další [ukázky pro Azure Policy](index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).