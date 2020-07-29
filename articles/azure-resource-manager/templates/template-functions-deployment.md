---
title: Funkce šablon – nasazení
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení informací o nasazení.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677810"
---
# <a name="deployment-functions-for-arm-templates"></a>Funkce nasazení pro šablony ARM

Správce prostředků poskytuje následující funkce pro získání hodnot vztahujících se k aktuálnímu nasazení šablony Azure Resource Manager (ARM):

* [prostředí](#deployment)
* [hlediska](#environment)
* [ukazatelů](#parameters)
* [proměnné](#variables)

Pokud chcete získat hodnoty z prostředků, skupin prostředků nebo předplatných, přečtěte si téma [funkce prostředků](template-functions-resource.md).

## <a name="deployment"></a>nasazení

`deployment()`

Vrátí informace o aktuální operaci nasazení.

### <a name="return-value"></a>Vrácená hodnota

Tato funkce vrací objekt, který je předán během nasazování. Vlastnosti vráceného objektu se liší v závislosti na tom, zda jste:

* nasazení šablony, která je místní soubor nebo nasazení šablony, která je vzdáleným souborem, ke kterému se přistupoval prostřednictvím identifikátoru URI.
* nasazení do skupiny prostředků nebo nasazení do jednoho z dalších oborů ([předplatné Azure](deploy-to-subscription.md), [skupina pro správu](deploy-to-management-group.md)nebo [tenant](deploy-to-tenant.md)).

Když nasadíte místní šablonu do skupiny prostředků: funkce vrátí následující formát:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Když nasazujete vzdálenou šablonu do skupiny prostředků: funkce vrátí následující formát:

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Když nasadíte do předplatného Azure, skupiny pro správu nebo tenanta, návratový objekt obsahuje `location` vlastnost. Vlastnost Location je obsažena při nasazení místní šablony nebo externí šablony. Formát je následující:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Poznámky

Nasazení () můžete použít k propojení na jinou šablonu na základě identifikátoru URI nadřazené šablony.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Pokud šablonu znovu nasadíte z historie nasazení na portálu, šablona bude nasazena jako místní soubor. `templateLink`Vlastnost není ve funkci nasazení vrácena. Pokud vaše šablona spoléhá na `templateLink` Vytvoření odkazu na jinou šablonu, nepoužívejte portál k opětovnému nasazení. Místo toho použijte příkazy, které jste použili k původnímu nasazení šablony.

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) vrátí objekt nasazení:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

Předchozí příklad vrátí následující objekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>environment

`environment()`

Vrátí informace o prostředí Azure používaném pro nasazení.

### <a name="return-value"></a>Vrácená hodnota

Tato funkce vrátí vlastnosti aktuálního prostředí Azure. Následující příklad ukazuje vlastnosti pro globální Azure. Cloudy svrchovaného května můžou vracet mírně odlišné vlastnosti.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Příklad

Následující příklad šablony vrátí objekt prostředí.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Předchozí příklad vrátí následující objekt při nasazení na globální Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Vrátí hodnotu parametru. Zadaný název parametru musí být definován v oddílu Parameters této šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |řetězec |Název parametru, který se má vrátit. |

### <a name="return-value"></a>Vrácená hodnota

Hodnota zadaného parametru.

### <a name="remarks"></a>Poznámky

Obvykle použijete parametry pro nastavení hodnot prostředků. Následující příklad nastaví název webu na hodnotu parametru předaného během nasazení.

```json
"parameters": {
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) ukazuje zjednodušené použití funkce Parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | Řetězec | možnost 1 |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"One": "a", "Two": "b"} |
| arrayOutput | Pole | [1, 2, 3] |
| crossOutput | Řetězec | možnost 1 |

Další informace o použití parametrů naleznete v tématu [Parameters in Azure Resource Manager Template](template-parameters.md).

## <a name="variables"></a>proměnné

`variables(variableName)`

Vrátí hodnotu proměnné. Zadaný název proměnné musí být definován v oddílu Variables šablony.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| variableName |Yes |Řetězec |Název proměnné, která se má vrátit. |

### <a name="return-value"></a>Vrácená hodnota

Hodnota zadané proměnné.

### <a name="remarks"></a>Poznámky

Obvykle používáte proměnné pro zjednodušení šablony vytvořením složitých hodnot pouze jednou. Následující příklad vytvoří jedinečný název pro účet úložiště.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Příklad

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) vrací různé hodnoty proměnných.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Name | Typ | Hodnota |
| ---- | ---- | ----- |
| exampleOutput1 | Řetězec | MojePromenna |
| exampleOutput2 | Pole | [1, 2, 3, 4] |
| exampleOutput3 | Řetězec | MojePromenna |
| exampleOutput4 |  Objekt | {"vlastnost1": "hodnota1"; "Vlastnost2": "hodnota2"} |

Další informace o použití proměnných naleznete v tématu [proměnné v šabloně Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
