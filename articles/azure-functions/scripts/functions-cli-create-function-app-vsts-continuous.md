---
title: Vytvoření aplikace Function App s nasazením DevOps – Azure CLI
description: Vytvoření aplikace funkcí a nasazení kódu funkce z Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: d10c3987aef6e0a32081acd17425517f5109c4bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75922640"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Vytvoření funkce v Azure nasazené z Azure DevOps

V tomto tématu se dozvíte, jak pomocí Azure Functions vytvořit aplikaci s funkcemi bez [serveru](https://azure.microsoft.com/solutions/serverless/) s využitím [plánu spotřeby](../functions-scale.md#consumption-plan). Aplikace Function App, která je kontejnerem pro vaše funkce, je průběžně nasazena z úložiště Azure DevOps. 

K dokončení tohoto tématu potřebujete:

* Úložiště Azure DevOps obsahující váš projekt aplikace funkcí, ke kterému máte oprávnění správce
* [Token PAT](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) pro přístup k vašemu úložišti Azure DevOps

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud raději používáte Azure CLI místně, musíte nainstalovat a používat verzi 2.0 nebo novější. Verzi Azure CLI zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

Tato ukázka vytvoří aplikaci funkcí Azure a nasadí kód funkce z Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, účtu úložiště, aplikace funkcí a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště vyžadovaný aplikací funkcí. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Vytvoří aplikaci funkcí v [plánu spotřeby](../functions-scale.md#consumption-plan)bez serveru. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Přidruží aplikaci funkcí k úložišti Git nebo Mercurial. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu Azure Functions najdete v [dokumentaci ke službě Azure Functions](../functions-cli-samples.md).
