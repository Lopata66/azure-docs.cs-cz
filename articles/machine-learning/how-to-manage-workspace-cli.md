---
title: Vytváření pracovních prostorů pomocí Azure CLI
titleSuffix: Azure Machine Learning
description: Naučte se používat rozhraní příkazového řádku Azure a vytvořit nový pracovní prostor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1cc280dc12fcb462e11a568910eef053e4bdac50
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319690"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Vytvoření pracovního prostoru pro Azure Machine Learning pomocí Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit pracovní prostor Azure Machine Learning pomocí Azure CLI. Rozhraní příkazového řádku Azure nabízí příkazy pro správu prostředků Azure. Rozšíření Machine Learning pro rozhraní příkazového řádku poskytuje příkazy pro práci s Azure Machine Learning prostředky.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Použijete-li [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), k rozhraní příkazového řádku se dostanete v prohlížeči a v cloudu.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Připojení rozhraní příkazového řádku k předplatnému Azure

> [!IMPORTANT]
> Pokud používáte Azure Cloud Shell, můžete tuto část přeskočit. Cloud Shell se automaticky ověřuje pomocí účtu, který se přihlašujete k předplatnému Azure.

Existuje několik způsobů, jak můžete z CLI ověřit předplatné Azure. Nejzákladnější je interaktivní ověřování pomocí prohlížeče. Chcete-li provést interaktivní ověřování, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Další metody ověřování najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instalace rozšíření Machine Learning

Pokud chcete nainstalovat rozšíření Machine Learning, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning spoléhá na tyto služby nebo entity Azure:

> [!IMPORTANT]
> Pokud nezadáte existující službu Azure, vytvoří se během vytváření pracovního prostoru automaticky. Vždy je nutné zadat skupinu prostředků. Při připojování vlastního účtu úložiště se ujistěte, že splňuje následující kritéria:
>
> * Účet _úložiště není účtem_ premium (Premium_LRS a Premium_GRS).
> * Funkce Azure Blob a Azure File jsou povolené.
> * Hierarchický obor názvů (ADLS Gen 2) je zakázaný.
>
> Tyto požadavky jsou určené jenom pro _výchozí_ účet úložiště, který používá pracovní prostor.

| Služba | Parametr pro určení existující instance |
| ---- | ---- |
| **Skupina prostředků Azure** | `-g <resource-group-name>`
| **Účet služby Azure Storage** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pracovní prostor Azure Machine Learning musí být vytvořený v rámci skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Pokud chcete __vytvořit novou skupinu prostředků__, použijte následující příkaz. Nahraďte `<resource-group-name>` názvem, který se má použít pro tuto skupinu prostředků. Nahraďte `<location>` oblastí Azure, kterou chcete použít pro tuto skupinu prostředků:

> [!TIP]
> Vyberte oblast, ve které je Azure Machine Learning k dispozici. Informace najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpověď z tohoto příkazu je podobná následujícímu kódu JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Další informace o práci se skupinami prostředků najdete v tématu [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Automaticky vytvářet požadované prostředky

Pokud chcete vytvořit nový pracovní prostor, ve kterém __se služby vytvoří automaticky__, použijte následující příkaz:

> [!TIP]
> Příkazy v této části vytvoří pracovní prostor základní edice. Chcete-li vytvořit pracovní prostor organizace, použijte `--sku enterprise` přepínač s `az ml workspace create` příkazem. Další informace o edicích Azure Machine Learning najdete v tématu [co je Azure Machine Learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> V názvu pracovního prostoru se nerozlišují malá a velká písmena.

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Použití existujících prostředků

Pokud chcete vytvořit pracovní prostor, který používá stávající prostředky, musíte zadat ID pro prostředky. Pro získání ID pro služby použijte následující příkazy:

> [!IMPORTANT]
> Nemusíte zadávat všechny existující prostředky. Můžete zadat jednu nebo více. Můžete například zadat existující účet úložiště a pracovní prostor vytvoří další prostředky.

+ **Účet Azure Storage**:`az storage account show --name <storage-account-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID účtu úložiště:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Pokud chcete použít existující účet Azure Storage, nemůže to být účet Premium (Premium_LRS a Premium_GRS). Nemůže mít také hierarchický obor názvů (používá se s Azure Data Lake Storage Gen2). Ve _výchozím_ účtu úložiště pracovního prostoru není podporován ani obor názvů Premium Storage ani hierarchický obor názvů. Můžete použít Storage úrovně Premium nebo hierarchický obor názvů s účty úložiště, _které nejsou výchozí_ .

+ **Application Insights Azure**:

    1. Instalace rozšíření Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Získejte ID vaší služby Application Insight:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpověď z tohoto příkazu je podobná následujícímu textu a je ID vaší služby Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID vašeho trezoru klíčů:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID pro registr kontejneru:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > V registru kontejneru musí být povolen [účet správce](/azure/container-registry/container-registry-authentication#admin-account) , aby jej bylo možné použít s pracovním prostorem Azure Machine Learning.

Jakmile budete mít ID prostředků, které chcete používat s pracovním prostorem, použijte základní `az workspace create -w <workspace-name> -g <resource-group-name>` příkaz a přidejte parametry a ID pro existující prostředky. Například následující příkaz vytvoří pracovní prostor, který používá existující registr kontejnerů:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Zobrazit seznam pracovních prostorů

Pokud chcete zobrazit seznam všech pracovních prostorů pro vaše předplatné Azure, použijte následující příkaz:

```azurecli-interactive
az ml workspace list
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Další informace najdete v dokumentaci [AZ ml Workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Získat informace o pracovním prostoru

Chcete-li získat informace o pracovním prostoru, použijte následující příkaz:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Další informace najdete v tématu o tom, jak [zobrazit dokumentaci AZ ml Workspace](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Aktualizovat pracovní prostor

Chcete-li aktualizovat pracovní prostor, použijte následující příkaz:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Další informace najdete v dokumentaci [AZ ml Workspace Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Sdílení pracovního prostoru s jiným uživatelem

Pokud chcete sdílet pracovní prostor s jiným uživatelem v předplatném, použijte následující příkaz:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Další informace o řízení přístupu na základě rolí (RBAC) s Azure Machine Learning najdete v tématu [Správa uživatelů a rolí](how-to-assign-roles.md).

Další informace najdete v dokumentaci ke [sdílení pracovního prostoru AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Synchronizace klíčů pro závislé prostředky

Pokud změníte přístupové klíče pro některý z prostředků, které váš pracovní prostor používá, trvá to přibližně hodinu, než se pracovní prostor synchronizuje s novým klíčem. Pokud chcete vynutit okamžitou synchronizaci nových klíčů v pracovním prostoru, použijte následující příkaz:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Další informace o změně klíčů najdete v tématu [obnovení přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

Další informace najdete v dokumentaci [AZ ml pracovní prostor Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Pokud chcete pracovní prostor odstranit poté, co už ho nepotřebujete, použijte tento příkaz:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Odstranění pracovního prostoru neodstraní přehled aplikací, účet úložiště, Trezor klíčů nebo registr kontejnerů, které používá pracovní prostor.

Můžete také odstranit skupinu prostředků, která odstraní pracovní prostor a všechny ostatní prostředky Azure ve skupině prostředků. Pokud chcete odstranit skupinu prostředků, použijte následující příkaz:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Další informace najdete v tématu [AZ ml Workspace Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) Document.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření Azure CLI pro Machine Learning najdete v dokumentaci [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) .
