---
title: Vytvoření zámku prostředků pro databázi a kontejner rozhraní API pro Azure Cosmos DB Core (SQL)
description: Vytvoření zámku prostředků pro databázi a kontejner rozhraní API pro Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 6c1795c5db84fbd8f8278534520234159caea33a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048990"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Vytvoření zámku prostředků pro databázi rozhraní API pro Azure Cosmos DB Core (SQL) a kontejner pomocí Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Cosmos DB SDK, všech nástrojů, které se připojují prostřednictvím klíčů účtu nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve uzamčen s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az-lock-create) | Vytvoří zámek. |
| [AZ Lock list](/cli/azure/lock#az-lock-list) | Vypíše informace o zámku. |
| [AZ Lock show](/cli/azure/lock#az-lock-show) | Zobrazit vlastnosti zámku. |
| [AZ LOCK Delete](/cli/azure/lock#az-lock-delete) | Odstraní zámek. |

## <a name="next-steps"></a>Další kroky

-[Uzamknout prostředky, aby nedocházelo k neočekávaným změnám](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku.

-[Azure Cosmos DB úložiště GitHubu CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
