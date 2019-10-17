---
title: Správa prostředků Azure pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Ke správě prostředků použijte Azure CLI a Azure Resource Manager. Ukazuje, jak nasadit a odstranit prostředky.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d3c3ca4a95cff8b9a81be8e75b011ca83799dcaa
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390387"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Správa prostředků Azure pomocí Azure CLI

Naučte se používat Azure CLI s [Azure Resource Manager](resource-group-overview.md) ke správě prostředků Azure. Informace o správě skupin prostředků najdete v tématu [Správa skupin prostředků Azure pomocí Azure CLI](./manage-resource-groups-cli.md).

Další články o správě prostředků:

- [Správa prostředků Azure pomocí Azure Portal](./manage-resources-portal.md)
- [Správa prostředků Azure pomocí Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Prostředky Azure můžete nasadit přímo pomocí Azure CLI nebo nasadit šablonu Správce prostředků pro vytvoření prostředků Azure.

### <a name="deploy-a-resource"></a>Nasazení prostředku

Následující skript vytvoří účet úložiště.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Nasazení šablony

Následující skript vytvoří nasazení šablony pro rychlý Start pro vytvoření účtu úložiště. Další informace najdete v tématu [rychlý Start: vytváření Azure Resource Manager šablon pomocí Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny. Další informace najdete v tématu [Vytvoření skupiny prostředků a nasazení prostředků](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Nasazení prostředků do více předplatných nebo skupin prostředků

Obvykle se všechny prostředky v šabloně nasazují do jedné skupiny prostředků. Existují však situace, kdy chcete nasadit sadu prostředků dohromady, ale umístit je do různých skupin prostředků nebo předplatných. Další informace najdete v tématu [nasazení prostředků Azure do několika předplatných nebo skupin prostředků](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Odstranění prostředků

Následující skript ukazuje, jak odstranit účet úložiště.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](./resource-group-delete.md).

## <a name="move-resources"></a>Přesunutí prostředků

Následující skript ukazuje, jak odebrat účet úložiště z jedné skupiny prostředků do jiné skupiny prostředků.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript zamkne účet úložiště, takže účet nejde odstranit.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Následující skript získá všechny zámky pro účet úložiště:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Následující skript odstraní zámek účtu úložiště:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označení pomáhá organizovat skupinu prostředků a prostředky logicky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](./resource-group-overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](./resource-group-authoring-templates.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](/azure/azure-resource-manager/).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).
