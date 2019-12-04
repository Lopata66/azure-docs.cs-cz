---
title: Migrace databáze-Azure Database for PostgreSQL – jeden server
description: Popisuje, jak extrahovat databázi PostgreSQL do souboru skriptu a importovat data do cílové databáze z daného souboru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770199"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrace databáze PostgreSQL pomocí exportu a importu
K extrakci databáze PostgreSQL do souboru skriptu a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) pro import dat do cílové databáze z tohoto souboru můžete použít [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) .

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md) s pravidly brány firewall pro povolení přístupu a databáze pod ní.
- nainstalovaný nástroj příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)
- nainstalovaný nástroj příkazového řádku [psql](https://www.postgresql.org/docs/current/static/app-psql.html)

Při exportu a importu databáze PostgreSQL postupujte podle těchto kroků.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Vytvoření souboru skriptu pomocí pg_dump obsahujícího data, která mají být načtena
Pokud chcete exportovat stávající databázi PostgreSQL místně nebo na virtuálním počítači do souboru skriptu SQL, spusťte v existujícím prostředí následující příkaz:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Například pokud máte místní server a databázi s názvem **TestDB** v ní:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importovat data na cílovém Azure Database for PostgreSQL
Pomocí příkazového řádku psql a parametru--dbname (-d) můžete importovat data do serveru Azure Database for PostgreSQL a načíst data ze souboru SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Tento příklad používá nástroj psql a soubor skriptu s názvem **TestDB. SQL** z předchozího kroku k importu dat do databáze **mypgsqldb** cílového serveru **mydemoserver.Postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Další kroky
- Postup migrace databáze PostgreSQL pomocí výpisu a obnovení najdete v tématu [migrace databáze PostgreSQL pomocí výpisu a obnovení](howto-migrate-using-dump-and-restore.md).
- Další informace o migraci databází do Azure Database for PostgreSQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration). 
