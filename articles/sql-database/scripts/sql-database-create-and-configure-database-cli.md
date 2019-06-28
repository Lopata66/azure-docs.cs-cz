---
title: Příklad rozhraní příkazového řádku – Vytvoření databáze Azure SQL | Microsoft Docs
description: Pomocí tohoto ukázkového skriptu Azure CLI vytvoříte databázi SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
manager: craigg
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 2a5fb6fcd9b1f0c327df23741284ec0d5fc26c71
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447879"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Použití rozhraní příkazového řádku k vytvoření jedné databáze Azure SQL a konfiguraci pravidla brány firewall

Tento ukázkový skript Azure CLI vytvoří databázi Azure SQL a nakonfiguruje pravidlo brány firewall na úrovni serveru. Po úspěšném spuštění skriptu bude služba SQL Database přístupná ze všech služeb Azure a nakonfigurovaných IP adres.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Použijte následující příkaz k odebrání skupiny prostředků a všechny prostředky, které s ním spojená.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server služby SQL Database, který je hostitelem elastický fond nebo izolovanou databázi. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Vytvoří pravidlo brány firewall umožňující přístup pro všechny izolované databáze elastické fondy na serveru SQL Database ze zadaného rozsahu IP adres. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | Vytvoří izolovanou databázi nebo elastický fond. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
