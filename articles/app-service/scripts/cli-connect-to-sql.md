---
title: 'CLI: připojení aplikace k databázi SQL'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak připojit aplikaci k databázi SQL.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: a0634cff6060e47ea059508f5f14abcaa679b53f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688457"
---
# <a name="connect-an-app-service-app-to-a-sql-database-using-cli"></a>Připojení aplikace App Service k databázi SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript vytvoří databázi SQL Azure a aplikaci App Service. Pak propojí databázi SQL s aplikací pomocí nastavení aplikace.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, App Service aplikace, SQL Database a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří aplikaci App Service. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) | Vytvoří server služby SQL Database.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) | Vytvoří na serveru služby SQL Database novou databázi. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) | Vygeneruje připojovací řetězec k databázi. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Vytvoří nebo aktualizuje nastavení aplikace pro App Service aplikaci. Nastavení aplikace se pro vaši aplikaci zveřejní jako proměnné prostředí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
