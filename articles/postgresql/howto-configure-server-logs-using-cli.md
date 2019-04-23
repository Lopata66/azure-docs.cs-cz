---
title: Konfigurace a získat přístup k protokolům server for PostgreSQL pomocí Azure CLI
description: Tento článek popisuje, jak nakonfigurovat a přístup k protokolům serveru ve službě Azure Database for PostgreSQL pomocí příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 3ca77c1ffa5f5a3f384009299701f4aa674baf59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421180"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a získat přístup k protokolům serveru pomocí příkazového řádku Azure
Můžete stáhnout protokoly chyb serveru PostgreSQL pomocí rozhraní příkazového řádku (Azure CLI). Přístup k protokolům transakce však není podporován. 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-azure-cli.md)
- [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurace protokolování pro službu Azure Database for PostgreSQL
Můžete nakonfigurovat server pro přístup k dotazu protokoly a protokoly chyb. Protokoly chyb může mít informace o automatické takový, připojení a kontrolního bodu.
1. Zapnutí protokolování.
2. Pokud chcete povolit protokolování dotazů, aktualizujte **protokolu\_příkaz** a **protokolu\_min\_doba trvání\_příkaz**.
3. Aktualizujte dobu uchování.

Další informace najdete v tématu [přizpůsobení parametrů konfigurace serveru](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Protokoly seznamu pro službu Azure Database for postgresql – serveru
Chcete-li seznam souborů protokolu k dispozici pro váš server, spusťte [az postgres server-logs list](/cli/azure/postgres/server-logs) příkazu.

Můžete zobrazit seznam souborů protokolu pro server **mydemoserver.postgres.database.azure.com** ve skupině prostředků **myresourcegroup**. Potom směrovat seznam souborů protokolů do textového souboru s názvem **protokolu\_soubory\_seznam.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Místní stažení protokolů ze serveru
S [az postgres server-logs Stáhnout](/cli/azure/postgres/server-logs) příkazu si můžete stáhnout jednotlivých protokolových souborů pro váš server. 

Použijte následující příklad ke stažení souboru protokolu specifické pro server **mydemoserver.postgres.database.azure.com** ve skupině prostředků **myresourcegroup** do vašeho místního prostředí.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Další postup
- Další informace o protokolech serveru najdete v tématu [protokolů serveru ve službě Azure Database for PostgreSQL](concepts-server-logs.md).
- Další informace o parametrech serveru najdete v tématu [přizpůsobení parametrů konfigurace serveru pomocí Azure CLI](howto-configure-server-parameters-using-cli.md).
