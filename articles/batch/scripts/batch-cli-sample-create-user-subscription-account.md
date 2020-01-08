---
title: Ukázkový skript Azure CLI – vytvoření účtu Batch – předplatné uživatele
description: Tento skript vytvoří účet Azure Batch v režimu předplatného uživatele. Tento účet přiděluje výpočetní uzly do předplatného.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 55429e0aafe978cfa6861d73b132ebcee26de493
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449696"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Příklad rozhraní příkazového řádku: Vytvoření účtu Batch v režimu předplatného uživatele

Tento skript vytvoří účet Azure Batch v režimu předplatného uživatele. Účet, který přiděluje výpočetní uzly do vašeho předplatného, musí být ověřený přes token Azure Active Directory. Přidělené výpočetní uzly se počítají do kvóty virtuálních procesorů (jader) vašeho předplatného. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az role assignment create](/cli/azure/role) | Vytvoří nové přiřazení role pro uživatele, skupinu nebo instanční objekt. |
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | Vytvoří trezor klíčů. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | Aktualizuje zásady zabezpečení zadaného trezoru klíčů. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
