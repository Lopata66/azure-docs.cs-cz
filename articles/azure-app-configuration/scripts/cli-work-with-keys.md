---
title: Ukázkový skript Azure CLI – práce s klíčovými hodnotami v App Configuration Storu
titleSuffix: Azure App Configuration
description: Použití skriptu Azure CLI k vytváření, zobrazování, aktualizaci a odstraňování hodnot klíčů z úložiště konfigurace aplikace
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54ad9f389070e1d5d280517a2f6c41e9a0f8f33e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929986"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Práce s klíčovými hodnotami v úložišti konfigurace aplikace Azure

Tento ukázkový skript ukazuje, jak:
* Vytvoří novou dvojici klíč-hodnota.
* Vypíše všechny existující páry klíč-hodnota.
* Aktualizace hodnoty nově vytvořeného klíče
* Odstraní novou dvojici klíč-hodnota.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.
## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

V této tabulce jsou uvedené příkazy, které se používají v našem ukázkovém skriptu. 

| Příkaz | Poznámky |
|---|---|
| [AZ appconfig KV set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Vytvoří nebo aktualizuje pár klíč-hodnota. |
| [AZ appconfig KV list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Vypíše páry klíč-hodnota v úložišti konfigurace aplikace. |
| [AZ appconfig KV DELETE](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Odstraní pár klíč-hodnota. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro konfiguraci aplikace najdete v ukázkách rozhraní příkazového [řádku konfigurace Azure](../cli-samples.md).
