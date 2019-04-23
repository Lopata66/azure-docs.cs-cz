---
title: Vytvoření centra událostí se skupina uživatelů – Azure Event Hubs | Dokumentace Microsoftu
description: Vytvořte obor názvů služby Event Hubs pomocí centra událostí a příjemce skupiny pomocí šablon Azure Resource Manageru
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: d5dc65dc225d11a996d9b9d3c329151a17321fb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343490"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Rychlý start: Vytvoření centra událostí pomocí šablony Azure Resource Manageru
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte Centrum událostí pomocí šablony Azure Resource Manageru. Použijete šablony Azure Resource Manageru k vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md)s jedním centrem událostí a jedné skupiny uživatelů. Tento článek ukazuje, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates]. Syntaxi JSON a vlastnosti, které chcete použít v šabloně najdete v tématu [typy prostředků Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

> [!NOTE]
> Úplnou šablonu najdete v článku [šablony skupiny centra a příjemce události] [ Event Hub and consumer group template] na Githubu. Tato šablona vytvořena skupina uživatelů kromě obor názvů služby event hub a centra událostí. Nejnovější šablony můžete zkontrolovat tak, že přejdete do galerie [Šablony Azure pro rychlý start][Azure Quickstart Templates] a vyhledáte Event Hubs.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud chcete použít **prostředí Azure PowerShell** k nasazení šablony Resource Manageru [instalace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pokud chcete použít **rozhraní příkazového řádku Azure** k nasazení šablony Resource Manageru [instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Vytvořit kód JSON šablony Resource Manageru
Vytvořte soubor JSON s názvem MyEventHub.json s následujícím obsahem a uložte ho do složky (například: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Vytvoření parametry JSON
Vytvořte soubor JSON s názvem MyEventHub-Parameters.json, který obsahuje parametry pro šablonu Azure Resource Manageru. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Pokud chcete nasadit šablonu pomocí Azure Powershellu

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
1. Spuštění prostředí Azure PowerShell

2. Spuštěním následujícího příkazu se přihlaste k Azure:

   ```azurepowershell
   Login-AzAccount
   ```
3. Pokud máte nastavení aktuálního kontextu předplatného tyto příkazy:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>Zřízení prostředků
Nasazení/zřizovat prostředky pomocí Azure Powershellu, přejděte do složky C:\EventHubsQuickStart\ARM\, spusťte následující příkazy:

> [!IMPORTANT]
> Zadejte název skupiny prostředků Azure jako hodnotu pro $resourceGroupName před spuštěním příkazů. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Použití Azure CLI k nasazení šablony

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud spouštíte příkazy ve službě Cloud Shell, následující kroky nemusíte provádět. Pokud používáte rozhraní příkazového řádku místně, provedením následujících kroků se přihlaste k Azure a nastavte své aktuální předplatné:

Spuštěním následujícího příkazu se přihlaste k Azure:

```azurecli
az login
```

Nastavte kontext na aktuální předplatné. Nahraďte `MyAzureSub` názvem předplatného Azure, které chcete použít:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Zřízení prostředků
Nasazení prostředků pomocí rozhraní příkazového řádku Azure, přejděte do složky, C:\EventHubsQuickStart\ARM\ a spusťte následující příkazy:

> [!IMPORTANT]
> Zadejte název pro skupinu prostředků Azure ve skupině az vytvořit příkaz. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Blahopřejeme! Použijete šablony Azure Resource Manageru k vytvoření oboru názvů Event Hubs a centra událostí v daném oboru názvů.

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili obor názvů služby Event Hubs a použili jste ukázkové aplikace k odesílání a přijímání událostí z centra událostí. Podrobné pokyny týkající se odesílání událostí do (nebo) přijímat události z centra událostí, najdete v článku **odesílat a přijímat události** kurzy: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (pouze pro odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (pouze reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
