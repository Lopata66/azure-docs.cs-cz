---
title: Vytvořit vlastní role prostředků Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure pomocí Azure CLI. Jedná se o seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56584892"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Vytvoření vlastních rolí pro prostředky Azure pomocí Azure CLI

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak vytvořit a spravovat vlastní role pomocí Azure CLI.

Podrobný kurz o tom, jak vytvořit vlastní roli, najdete v tématu [kurzu: Vytvoření vlastní role pro prostředky Azure pomocí Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit vlastní role, budete potřebovat:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Výpis vlastních rolí

K zobrazení seznamu vlastních rolí, které jsou k dispozici pro přiřazení, použijte [az role definition list](/cli/azure/role/definition#az-role-definition-list). Následující příklady jsou uvedeny všechny vlastní role v rámci aktuálního předplatného.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Seznam definice vlastních rolí

Chcete-li vypsat definice vlastních rolí, použijte [az role definition list](/cli/azure/role/definition#az-role-definition-list). Toto je stejný příkaz, který používáte pro předdefinovanou roli.

```azurecli
az role definition list --name <role_name>
```

Následující příklad zobrazí *virtuálního počítače operátor* definice role:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Následující příklad vypíše pouze akce *virtuálního počítače operátor* role:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte [az role definition vytvořit](/cli/azure/role/definition#az-role-definition-create). Definice role může být popis JSON nebo cesta k souboru, který obsahuje popis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Následující příklad vytvoří vlastní roli s názvem *virtuálního počítače operátor*. Tato vlastní role přiřadí přístup pro všechny operace čtení z *Microsoft.Compute*, *Microsoft.Storage*, a *Microsoft.Network* přístup poskytovatele a přiřadí k prostředkům Pokud chcete začít, restartování a monitorovat virtuální počítače. Tuto vlastní roli je možné ve dvou předplatných. Tento příklad používá soubor JSON jako vstup.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Pokud chcete aktualizovat vlastní roli, nejprve pomocí [az role definition list](/cli/azure/role/definition#az-role-definition-list) načíst definici role. Za druhé proveďte požadované změny do definice role. Nakonec použijte [az role definition update](/cli/azure/role/definition#az-role-definition-update) se uložit definici aktualizovaná role.

```azurecli
az role definition update --role-definition <role_definition>
```

Následující příklad přidá *Microsoft.Insights/diagnosticSettings/* operace *akce* z *virtuálního počítače operátor* vlastní roli.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Pokud chcete odstranit vlastní roli, použijte [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Pokud chcete nastavit roli, kterou chcete odstranit, použijte název role nebo ID role. Pokud chcete zjistit role ID, použít [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Následující příklad odstraní *virtuálního počítače operátor* vlastní roli.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure CLI](tutorial-custom-role-cli.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Operace poskytovatele prostředků Azure Resource Manageru](resource-provider-operations.md)