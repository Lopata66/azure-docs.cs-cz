---
title: Ukázkový skript Azure CLI – vytvoření účtu Batch – služba Batch
description: Tento skript vytvoří účet Azure Batch v režimu služby Batch a předvede dotazování a aktualizaci různých vlastností účtu.
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
ms.openlocfilehash: ee733b492e1d89c58336003bcb4be72f79b9e403
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449741"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Příklad rozhraní příkazového řádku: Vytvoření účtu Batch v režimu služby Batch

Tento skript vytvoří účet Azure Batch v režimu služby Batch a předvede dotazování a aktualizaci různých vlastností účtu. Při vytvoření účtu Batch ve výchozím režimu služby Batch se jeho výpočetní uzly přiřadí interně službou Batch. Na přidělené výpočetní uzly se vztahuje samostatná kvóta virtuálních procesorů (jader) a účet se může ověřovat přes přihlašovací údaje sdíleného klíče nebo token Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Vytvoří účet Batch. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Aktualizuje vlastnosti účtu Batch.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Načte podrobnosti o zadaném účtu Batch.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Načte přístupové klíče zadaného účtu Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Provede ověření v zadaném účtu Batch pro další práci s rozhraním příkazového řádku.  |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
