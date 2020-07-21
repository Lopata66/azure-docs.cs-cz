---
title: Vlastní role Azure – Azure RBAC
description: Naučte se vytvářet vlastní role Azure pomocí řízení přístupu na základě role Azure (Azure RBAC) pro detailní řízení přístupu k prostředkům Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d973cf47ed691914b22d62e1a99315c6ea9183d8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511598"
---
# <a name="azure-custom-roles"></a>Vlastní role Azure

> [!IMPORTANT]
> Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Stejně jako předdefinované role můžete uživatelům, skupinám a instančním objektům přiřadit vlastní role na úrovni skupiny pro správu, předplatného a skupiny prostředků.

Vlastní role se dají sdílet mezi předplatnými, která důvěřují stejnému adresáři služby Azure AD. Pro každý adresář je povolený limit **5 000** vlastních rolí. (Pro Azure Německo a Azure Čína 21Vianet je limit 2 000 vlastních rolí.) Vlastní role se dají vytvořit pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API.

## <a name="custom-role-example"></a>Příklad vlastní role

Následující text ukazuje, jak vlastní role vypadá jako zobrazení pomocí Azure PowerShell ve formátu JSON. Tato vlastní role se dá použít k monitorování a restartování virtuálních počítačů.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

V následujícím seznamu vidíte stejnou vlastní roli, jak se zobrazuje pomocí Azure CLI.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
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

Když vytvoříte vlastní roli, zobrazí se v Azure Portal s ikonou oranžového prostředku.

![Ikona vlastní role](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Vlastnosti vlastní role

Následující tabulka popisuje, co znamenají vlastnosti vlastní role.

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ano | Řetězec | Zobrazované jméno vlastní role Zatímco definice role je skupina pro správu nebo prostředek na úrovni předplatného, můžete definici role použít ve více předplatných, která sdílejí stejný adresář služby Azure AD. Tento zobrazovaný název musí být jedinečný v oboru adresáře služby Azure AD. Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 128. |
| `Id`</br>`name` | Ano | Řetězec | Jedinečné ID vlastní role Pro Azure PowerShell a Azure CLI se toto ID automaticky vygeneruje při vytvoření nové role. |
| `IsCustom`</br>`roleType` | Ano | Řetězec | Označuje, zda se jedná o vlastní roli. Nastavte na `true` nebo `CustomRole` pro vlastní role. Nastavte na `false` nebo `BuiltInRole` pro předdefinované role. |
| `Description`</br>`description` | Ano | Řetězec | Popis vlastní role Může obsahovat písmena, číslice, mezery a speciální znaky. Maximální počet znaků je 1024. |
| `Actions`</br>`actions` | Ano | Řetězec [] | Pole řetězců, které určuje operace správy, které může role provést. Další informace najdete v tématu [Akce](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | No | Řetězec [] | Pole řetězců, které určují operace správy, které jsou vyloučeny z povolených `Actions` . Další informace najdete v tématu [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | Řetězec [] | Pole řetězců, které určuje datové operace, které může role provádět na vašich datech v rámci daného objektu. Pokud vytvoříte vlastní roli s `DataActions` , tuto roli nelze přiřadit v oboru skupiny pro správu. Další informace naleznete v tématu [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | No | Řetězec [] | Pole řetězců, které určují operace s daty, které jsou vyloučeny z povolených `DataActions` . Další informace najdete v tématu [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Ano | Řetězec [] | Pole řetězců, které určuje rozsahy, které jsou k dispozici pro přiřazení vlastní role. V rámci vlastní role můžete definovat jenom jednu skupinu pro správu `AssignableScopes` . Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview. Další informace najdete v tématu [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Oprávnění zástupných znaků

`Actions`, `NotActions` , `DataActions` a `NotDataActions` podporují zástupné znaky ( `*` ) pro definování oprávnění. Zástupný znak ( `*` ) rozšiřuje oprávnění na vše, co odpovídá řetězci akce, který zadáte. Předpokládejme například, že jste chtěli přidat všechna oprávnění související s Azure Cost Management a exporty. Můžete přidat všechny tyto řetězce akcí:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Místo přidávání všech těchto řetězců stačí přidat zástupný řetězec. Například následující zástupný řetězec je ekvivalentem předchozích pěti řetězců. To by také zahrnovalo všechna budoucí oprávnění k exportu, která by se mohla přidat.

```
Microsoft.CostManagement/exports/*
```

V řetězci můžete mít také několik zástupných znaků. Například následující řetězec reprezentuje všechna oprávnění dotazu pro Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="steps-to-create-a-custom-role"></a>Postup vytvoření vlastní role

Pokud chcete vytvořit vlastní roli, řiďte se základními kroky, které byste měli dodržovat.

1. Rozhodněte, jak chcete vytvořit vlastní roli.

    Vlastní role můžete vytvořit pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API.

1. Určete potřebná oprávnění.

    Při vytváření vlastní role potřebujete znát operace, které jsou k dispozici pro definování vašich oprávnění. Chcete-li zobrazit seznam operací, přečtěte si téma [operace poskytovatele prostředků Azure Resource Manager](resource-provider-operations.md). Operace přidáte do `Actions` vlastností nebo v `NotActions` [definici role](role-definitions.md). Pokud máte datové operace, přidejte je do `DataActions` `NotDataActions` vlastností nebo.

1. Vytvořte vlastní roli.

    Obvykle začínáte stávající integrovanou rolí a pak ji upravíte podle svých potřeb. Nejjednodušší způsob je použít Azure Portal. Postup vytvoření vlastní role pomocí Azure Portal najdete v tématu [Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure Portal](custom-roles-portal.md).

1. Otestujte vlastní roli.

    Jakmile máte vlastní roli, musíte ji otestovat, abyste ověřili, že funguje podle očekávání. Pokud potřebujete provést úpravy později, můžete aktualizovat vlastní roli.

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli

Stejně jako předdefinované role `AssignableScopes` Určuje vlastnost obory, které je role k dispozici pro přiřazení. `AssignableScopes`Vlastnost vlastní role také určuje, kdo může vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli.

| Úkol | Operace | Popis |
| --- | --- | --- |
| Vytvoření nebo odstranění vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělena na všech `AssignableScopes` vlastních rolích, mohou vytvořit (nebo odstranit) vlastní role pro použití v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupiny pro správu, předplatná a skupiny prostředků. |
| Aktualizace vlastní role | `Microsoft.Authorization/ roleDefinitions/write` | Uživatelé, kterým je tato operace udělena na všech `AssignableScopes` vlastních rolích, mohou aktualizovat vlastní role v těchto oborech. Například [vlastníci](built-in-roles.md#owner) a [Správci přístupu uživatelů](built-in-roles.md#user-access-administrator) skupiny pro správu, předplatná a skupiny prostředků. |
| Zobrazení vlastní role | `Microsoft.Authorization/ roleDefinitions/read` | Uživatelé, kterým je tato operace udělená v oboru, můžou zobrazit vlastní role, které jsou k dispozici pro přiřazení v daném oboru. Všechny předdefinované role umožňují, aby byly vlastní role k dispozici pro přiřazení. |

## <a name="custom-role-limits"></a>Omezení vlastních rolí

Následující seznam popisuje omezení pro vlastní role.

- Každý adresář může mít až **5000** vlastních rolí.
- Azure Německo a Azure Čína 21Vianet můžou mít pro každý adresář až 2000 vlastních rolí.
- Nelze nastavit `AssignableScopes` do kořenového oboru ( `"/"` ).
- V rámci vlastní role můžete definovat jenom jednu skupinu pro správu `AssignableScopes` . Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
- Vlastní role se `DataActions` nedají přiřadit v oboru skupiny pro správu.
- Azure Resource Manager neověřuje existenci skupiny pro správu v oboru přiřazení definice role.

Další informace o vlastních rolích a skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Formáty vstupu a výstupu

Chcete-li vytvořit vlastní roli pomocí příkazového řádku, obvykle použijte JSON k určení vlastností, které chcete použít pro vlastní roli. V závislosti na používaných nástrojích se formáty vstupu a výstupu budou mírně lišit. V této části jsou uvedeny formáty vstupu a výstupu v závislosti na nástroji.

### <a name="azure-powershell"></a>Azure PowerShell

Pokud chcete vytvořit vlastní roli pomocí Azure PowerShell, musíte zadat následující vstup.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Chcete-li aktualizovat vlastní roli pomocí Azure PowerShell, je nutné zadat následující vstup. Všimněte si, že byla `Id` přidána vlastnost. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Následující ukázka ukazuje příklad výstupu při výpisu vlastní role pomocí Azure PowerShell a příkazu [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) . 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Pokud chcete vytvořit nebo aktualizovat vlastní roli pomocí rozhraní příkazového řádku Azure, musíte zadat následující vstup. Tento formát má při vytváření vlastní role pomocí Azure PowerShell stejný formát.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Následující ukázka ukazuje příklad výstupu při výpisu vlastní role pomocí Azure CLI.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

Pokud chcete vytvořit nebo aktualizovat vlastní roli pomocí REST API, musíte zadat následující vstup. Tento formát je stejný formát, který se generuje při vytvoření vlastní role pomocí Azure Portal.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Následující ukázka ukazuje příklad výstupu při výpisu vlastní role pomocí REST API.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md)
- [Kurz: Vytvoření vlastní role Azure pomocí Azure CLI](tutorial-custom-role-cli.md)
- [Vysvětlení definic rolí Azure](role-definitions.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
