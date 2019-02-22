---
title: Zamknout prostředky Azure, které zabraňují změně | Dokumentace Microsoftu
description: Zabraňte uživatelům v aktualizaci nebo odstranění důležitých prostředků Azure s použitím zámek pro všechny uživatele a role.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: 83518825c91cdd727b3d4fb9ecc86d51dea8fc26
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649165"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Uzamčení prostředků, aby se zabránilo neočekávaným změnám 

Jako správce budete muset zamknout předplatné, skupinu prostředků nebo prostředek Neumožnit dalším uživatelům ve vaší organizaci omylem odstranit nebo upravit důležitých prostředků. Zámek můžete nastavit na úroveň **CanNotDelete** nebo **ReadOnly**. Na portálu, se nazývají zámků **odstranit** a **jen pro čtení** v uvedeném pořadí.

* **CanNotDelete** znamená, že oprávnění uživatelé stále může číst a upravit prostředek, ale jejich nelze odstranit prostředek. 
* **Jen pro čtení** znamená, že oprávnění uživatelé mohou číst prostředek, ale nemůže odstranit nebo aktualizovat prostředek. Použití tohoto uzamknout je podobná omezení všem oprávněným uživatelům oprávnění udělená **čtečky** role. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Použití zámků

Při použití zámku v nadřazeném oboru dědí všechny prostředky v daném oboru stejný zámku. I prostředky, které přidáte později dědit Zámek z nadřazeného objektu. Nejvíc omezující zámku v dědičnosti má přednost.

Na rozdíl od řízení přístupu podle role pomocí zámky pro správu platí omezení na všech uživatelů a rolí. Další informace o nastavení oprávnění uživatelů a rolí, najdete v článku [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).

Zámky Resource Manageru platí pouze pro operace, ke kterým dochází v rovině správy, který se skládá z operací odesílat `https://management.azure.com`. Zámky Neomezovat, jak prostředky provádět vlastní funkce. Změn prostředků jsou omezené, ale nejsou operace prostředků s omezeným přístupem. Například brání zámek ReadOnly u SQL Database můžete odstranit nebo upravit databázi, ale to není by vám bránily v vytvoření, aktualizaci a odstraňování dat v databázi. Data transakce se nepovoluje, protože tyto operace se neodesílají na `https://management.azure.com`.

Použití **jen pro čtení** může vést k neočekávaným výsledkům, protože některé operace, které se zdají být načten operace ve skutečnosti vyžadují další akce. Například, že umístíte **jen pro čtení** výpis klíčů všem uživatelům zabrání zámek účtu úložiště. Seznam, který klíče operace probíhá prostřednictvím funkce požadavek POST, protože vrácený klíče jsou k dispozici pro operace zápisu. Další příklad umístění **jen pro čtení** zámek na prostředek služby App Service brání Průzkumníka serveru Visual Studia v zobrazení souborů pro prostředek, protože danou interakci vyžaduje oprávnění k zápisu.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Kdo může vytvářet nebo odstranit zámky pro ve vaší organizaci
Pokud chcete vytvořit nebo odstranit zámky pro správu, musí mít přístup k `Microsoft.Authorization/*` nebo `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Šablona

Při použití šablony Resource Manageru k nasazení zámek, použijte jiné hodnoty pro název a typ v závislosti na rozsahu zámku.

Při použití zámkem, který **prostředků**, použijte následující formáty:

* Název – `{resourceName}/Microsoft.Authorization/{lockName}`
* Typ – `{resourceProviderNamespace}/{resourceType}/providers/locks`

Při použití zámkem, který **skupiny prostředků** nebo **předplatné**, použijte následující formáty:

* Název – `{lockName}`
* Typ – `Microsoft.Authorization/locks`

Následující příklad ukazuje šablonu, která vytvoří plán služby app service, webu a zámek na webové stránce. Typ prostředku zámku je prostředků typu prostředku, který chcete zamknout a **/providers/ zámky**. Název zámku se vytváří zřetězením názvu prostředku s **/Microsoft.Authorization/** a název zámku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Příklad nastavení zámku na skupinu prostředků, najdete v části [vytvořte skupinu prostředků a uzamkne ji](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Zámek je nasazené prostředky v prostředí Azure PowerShell s použitím [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) příkazu.

Uzamknout prostředek, zadejte název prostředku, jeho typ prostředku a jeho název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Zamknout skupinu prostředků, zadejte název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Chcete-li získat informace o zámku, použijte [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock). K získání všech zámků ve vašem předplatném, použijte:

```azurepowershell-interactive
Get-AzResourceLock
```

K získání všech zámků pro určitý prostředek, použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

K získání všech zámků pro skupinu prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Pokud chcete odstranit zámek, použijte:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Zámek je nasazení prostředků pomocí Azure CLI pomocí [az lock vytvořit](/cli/azure/lock#az-lock-create) příkazu.

Uzamknout prostředek, zadejte název prostředku, jeho typ prostředku a jeho název skupiny prostředků.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Zamknout skupinu prostředků, zadejte název skupiny prostředků.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Chcete-li získat informace o zámku, použijte [az lock list](/cli/azure/lock#az-lock-list). K získání všech zámků ve vašem předplatném, použijte:

```azurecli
az lock list
```

K získání všech zámků pro určitý prostředek, použijte:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

K získání všech zámků pro skupinu prostředků, použijte:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Pokud chcete odstranit zámek, použijte:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Můžeš nasazených prostředků s [rozhraní REST API pro zámky pro správu](https://docs.microsoft.com/rest/api/resources/managementlocks). Rozhraní REST API umožňuje vytvářet a odstraňovat zámky a načtení informací o existující zámky.

Pokud chcete vytvořit zámek, spusťte:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Rozsah může být předplatné, skupinu prostředků nebo prostředek. Název zámku je, cokoli, co chcete volat zámek. Pro verzi api-version, použijte **2016-09-01**.

V požadavku obsahovat objekt JSON, který určuje vlastnosti pro zámek.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Další postup
* Další informace o logické uspořádání vašich prostředků najdete v tématu [použití značek k uspořádání prostředků](resource-group-using-tags.md)
* Můžete použít omezení a pravidla týkající se předplatného pomocí vlastních zásad. Další informace najdete v tématu [Co je Azure Policy?](../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

