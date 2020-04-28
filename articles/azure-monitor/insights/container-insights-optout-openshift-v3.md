---
title: Postup zastavení monitorování clusteru Azure Red Hat OpenShift V3 | Microsoft Docs
description: Tento článek popisuje, jak můžete zastavit monitorování clusteru Azure Red Hat OpenShift pomocí Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: e1d3200af62ad185fa942fa2c8f7f3b4e6bfd89b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196189"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Postup zastavení monitorování clusteru Azure Red Hat OpenShift V3

Po povolení monitorování clusteru Azure Red Hat OpenShift verze 3. x můžete zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, pokud se rozhodnete, že ho už nechcete monitorovat. Tento článek ukazuje, jak toho dosáhnout pomocí Azure Resource Manager šabloně.  

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

K dispozici jsou dvě šablony Azure Resource Manager, které podporují odebrání prostředků řešení konzistentně a opakovaně ve vaší skupině prostředků. Jedna je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru OpenShift a oblasti Azure, ve které je cluster nasazený.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.65 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-template"></a>Vytvoření šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Uložte tento soubor jako **OptOutTemplate. JSON** do místní složky.

3. Do souboru vložte následující syntaxi JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Upravte hodnoty pro **aroResourceId** a **aroResourceLocation** pomocí hodnot clusteru OpenShift, který najdete na stránce **vlastností** pro vybraný cluster.

    ![Stránka vlastností kontejneru](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Uložte tento soubor jako **OptOutParam. JSON** do místní složky.

6. Jste připraveni k nasazení této šablony.

### <a name="remove-the-solution-using-azure-cli"></a>Odebrání řešení pomocí Azure CLI

Spuštěním následujícího příkazu u Azure CLI v systému Linux odeberte řešení a vyčistěte konfiguraci v clusteru.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Dokončení změny konfigurace může trvat několik minut. Po dokončení se vrátí zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Odebrání řešení pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Spusťte následující příkazy PowerShellu ve složce obsahující šablonu pro odebrání řešení a vyčištění konfigurace z clusteru.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Dokončení změny konfigurace může trvat několik minut. Po dokončení se vrátí zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Další kroky

Pokud byl pracovní prostor vytvořen jenom pro podporu monitorování clusteru a už ho nepotřebujete, musíte ho ručně odstranit. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
