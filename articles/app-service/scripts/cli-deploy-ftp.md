---
title: 'CLI: nasazení souborů aplikace pomocí FTP'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak vytvořit aplikaci a jak nasadit soubory s využitím protokolu FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 320f293073ce0a97cf4b73b420f38d91c5206013
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688364"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Vytvoření aplikace App Service a nasazení souborů s využitím FTP pomocí Azure CLI

Tento ukázkový skript vytvoří aplikaci v App Service se souvisejícími prostředky a pak nasadí statickou stránku HTML pomocí FTP. K nahrání přes protokol FTP skript jako příklad používá [cURL](https://en.wikipedia.org/wiki/CURL). K nahrání souborů můžete použít jakýkoli nástroj FTP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu 

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří aplikaci App Service. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az-webapp-deployment-list-publishing-profiles) | Získejte podrobnosti o dostupných profilech nasazení aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
