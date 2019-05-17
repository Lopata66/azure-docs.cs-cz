---
title: Servery služby Azure SQL Database | Dokumentace Microsoftu
description: Další informace o serverech služby Azure SQL Database a jejich správu.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 21d4e7bd3bf7453d2c770034728aedfdaa5ab85e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790175"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Servery služby Azure SQL Database a jejich správa

## <a name="what-is-an-azure-sql-database-server"></a>Co je server Azure SQL Database

Server služby SQL Database je logická konstrukce, která funguje jako centrální administrativní bod pro více jedním nebo [ve fondu](sql-database-elastic-pool.md) databází, [přihlášení](sql-database-manage-logins.md), [pravidla brány firewall](sql-database-firewall-configure.md), [auditování pravidla](sql-database-auditing.md), [zásad detekce hrozeb](sql-database-threat-detection.md), a [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) databáze SQL serveru může být v jiné oblasti než skupinou prostředků. Než budete moct vytvořit databázi Azure SQL, musí existovat server SQL Database. Server služby SQL Database spravuje všechny databáze se vytvoří ve stejné oblasti jako databázového SQL serveru.

Server služby SQL Database se liší od instance SQL serveru, který je pravděpodobně znáte místního prostředí. Konkrétně služby SQL Database neposkytuje žádnou záruku ohledně umístění databází ve vztahu k serveru SQL Database, která spravuje a zpřístupňuje bez přístupu na úrovni instance nebo funkce. Naproti tomu instanci databáze ve spravované instanci jsou všechny společně umístěné-stejným způsobem, že máte zkušenosti s SQL serverem v místního prostředí.

Když vytvoříte server služby SQL Database, zadáte server přihlašovací účet a heslo, které má oprávnění správce pro hlavní databázi na daném serveru a všechny databáze vytvořené na tomto serveru. Tento počáteční účet je účet přihlášení SQL. Azure SQL Database podporuje ověřování SQL a ověřování Azure Active Directory pro ověřování. Informace o přihlašování a ověřování najdete v tématu [Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md). Ověřování systému Windows se nepodporuje.

Server služby SQL Database:

- Je vytvořen v rámci předplatného Azure, ale je možné ho i s obsaženými prostředky přenést do jiného předplatného.
- Je nadřazeným prostředkem pro databáze, elastické fondy a datové sklady.
- Poskytuje obor názvů pro databáze, elastické fondy a datové sklady
- Je logický kontejner se silnou sémantikou životního cyklu – odstraníte server a odstraní databáze s omezením, elastické fondy a datové sklady
- Účastní se [řízení přístupu Azure na základě rolí (RBAC)](/azure/role-based-access-control/overview) – databáze, elastické fondy a datové sklady v rámci serveru dědí přístupová práva od serveru
- Je nejvyšším prvkem identity databází, elastické fondy a datové sklady pro prostředky Azure (viz schéma adresy URL pro databáze a fondy) účely správy
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k databázi (`<serverName>`.database.windows.net).
- Poskytuje přístup k metadatům, která se vztahují k obsaženým prostředkům, přes zobrazení dynamických zpráv díky připojení k hlavní databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze - přihlášení, brány firewall, auditování, detekce hrozeb a takové
- Je omezený kvótou nadřazeného předplatného (šest serverů na předplatné, ve výchozím nastavení - [viz limity předplatného](../azure-subscription-service-limits.md))
- Poskytuje obor pro kvóty databáze a DTU nebo vCore kvóty pro prostředky, které obsahuje (jako je například 45 000 DTU)
- Je oborem správy verzí pro možnosti povolené u obsažených prostředků
- Hlavní přihlášení na úrovni serveru můžou spravovat všechny databáze na serveru.
- Může obsahovat přihlašovací údaje podobné těm, která se místně používají v instancích SQL Serveru, a udělit jim přístup k jedné nebo několika databázím na serveru spolu s omezenými právy pro správu. Další informace najdete v tématu [Přihlašovací údaje](sql-database-manage-logins.md).
- Výchozí kolaci pro všechny databáze vytvořené na serveru služby SQL Database je `SQL_LATIN1_GENERAL_CP1_CI_AS`, kde `LATIN1_GENERAL` je angličtina (Spojené státy), `CP1` je znakovou sadu 1252, `CI` je velká a malá písmena, a `AS` je diakritiky.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Spravovat servery Azure SQL, databáze a brány firewall s využitím webu Azure portal

Můžete vytvořit skupinu prostředků Azure SQL database předem nebo při vytváření samotný server. Existuje několik metod pro získání nového formuláře SQL serveru tak, že vytvoříte nový SQL server nebo jako součást vytváření nové databáze.

### <a name="create-a-blank-sql-database-server"></a>Vytvoření prázdné databáze SQL serveru

K vytvoření serveru (bez databáze) databáze SQL Azure pomocí [webu Azure portal](https://portal.azure.com), přejděte na prázdný formulář SQL server (logický server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Vytvoření prázdné nebo ukázková databáze SQL

K vytvoření databáze Azure SQL pomocí [webu Azure portal](https://portal.azure.com), přejděte na prázdný formulář databáze SQL a zadejte požadované informace. Můžete vytvořit skupinu prostředků a server služby SQL Database předem nebo při vytváření samotná databáze Azure SQL database. Můžete vytvořit prázdnou databázi nebo vytvoření ukázkové databáze založené na Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Další informace o výběru cenová úroveň pro vaši databázi, naleznete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Vytvoření managed instance najdete v tématu [vytvoření spravované instance](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Správa existujícího serveru SQL

Chcete-li spravovat existující server, přejděte na server pomocí několika metod – jako třeba konkrétní stránce databáze SQL, **SQL servery** stránky, nebo **všechny prostředky** stránky.

Chcete-li spravovat stávající databázi, přejděte na **databází SQL** stránky a klikněte na databázi, kterou chcete spravovat. Následující snímek obrazovky ukazuje, jak začít, nastavení brány firewall úrovni serveru pro databázi z **přehled** stránku pro databázi.

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Postup konfigurace vlastností výkonu u databáze, najdete v článku [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
> [!TIP]
> Azure portal rychlém startu najdete v části [vytvořit databázi Azure SQL na webu Azure Portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

K vytváření a správě serveru Azure SQL, databáze a brány firewall pomocí Azure Powershellu, použijte následující rutiny Powershellu. Pokud potřebujete instalaci nebo upgrade prostředí PowerShell, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Vytváření a správa elastických fondů najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

| Rutina | Popis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří databázi |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti pro databáze nebo přesune databázi do elastického fondu|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Vytvoří skupinu prostředků|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Vytvoří server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Vrátí informace o serverech|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Upraví vlastnosti serveru|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Odebere server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Získá pravidla brány firewall pro server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Upraví pravidla brány firewall na serveru|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| New-AzSqlServerVirtualNetworkRule | Vytvoří [ *pravidlo virtuální sítě*](sql-database-vnet-service-endpoint-rule-overview.md)založená na podsíť, která je koncový bod služby virtuální sítě. |

> [!TIP]
> Prostředí PowerShell rychlém startu najdete v části [vytvořit jednu databázi Azure SQL pomocí Powershellu](sql-database-single-database-get-started.md). Příklady skriptů Powershellu, najdete v části [použití Powershellu k vytvoření jedné databáze Azure SQL a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-powershell.md) a [sledování a škálování Azure SQL database pomocí prostředí PowerShell jednotné](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí Azure CLI

K vytváření a správě serveru Azure SQL, databáze a brány firewall se [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Vytváření a správa elastických fondů najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

| Rutina | Popis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Vytvoří databázi|
|[AZ sql db list](/cli/azure/sql/db#az-sql-db-list)|Obsahuje seznam všech databází a datových skladů na serveru, nebo všechny databáze v elastickém fondu|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Seznamy dostupnou službu cíle a omezení úložiště|
|[AZ sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Vrací databáze využití|
|[AZ sql db show](/cli/azure/sql/db#az-sql-db-show)|Získá databázi ani na datový sklad|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizace databáze|
|[AZ sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Odebere databázi|
|[az group create](/cli/azure/group#az-group-create)|Vytvoří skupinu prostředků|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Vytvoří server|
|[AZ sql server list](/cli/azure/sql/server#az-sql-server-list)|Vytvoří seznam serverů|
|[AZ sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Vrátí použití serveru|
|[AZ sql server show](/cli/azure/sql/server#az-sql-server-show)|Získá serveru|
|[aktualizace az sql server](/cli/azure/sql/server#az-sql-server-update)|Aktualizace serveru|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Odstraní server|
|[Vytvoření az sql server firewall-rule](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Vytvoří pravidlo brány firewall serveru|
|[AZ sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Obsahuje seznam pravidel brány firewall na serveru|
|[AZ sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Zobrazí podrobnosti pravidla brány firewall|
|[AZ sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje pravidlo brány firewall|
|[AZ sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Odstraní pravidlo brány firewall|

> [!TIP]
> Rychlý start Azure CLI najdete v části [vytvořit jednu databázi Azure SQL pomocí Azure CLI](sql-database-cli-samples.md). Příklad skripty rozhraní příkazového řádku Azure, najdete v části [pomocí rozhraní příkazového řádku k vytvoření jedné databáze Azure SQL a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-cli.md) a [pomocí rozhraní příkazového řádku pro monitorování a škálování izolovanou databázi Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí příkazů jazyka Transact-SQL

K vytváření a správě serveru Azure SQL, databáze a brány firewall pomocí příkazů jazyka Transact-SQL, použijte následující příkazy T-SQL. Můžete použít tyto příkazy pomocí webu Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který můžete připojit k serveru Azure SQL Database a předat příkazů jazyka Transact-SQL příkazy. Elastické fondy, přečtěte si téma [elastické fondy](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nejde vytvořit nebo odstranit server pomocí příkazů jazyka Transact-SQL.
>

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi. Musíte být připojení k hlavní databázi, a vytvořit novou databázi.|
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Upraví databázi Azure SQL. |
|[Příkaz ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Upraví službu Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (úroveň služby), cíl služby (cenová úroveň) a název elastického fondu, pokud existují pro službu Azure SQL database nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi na serveru Azure SQL Database, vrátí informace ve všech databázích. Pro službu Azure SQL Data Warehouse musí být připojené k hlavní databázi.|
|[Sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí data o využití a úložiště CPU pro službu Azure SQL Database. Data se shromažďují a agregují v pětiminutovém intervalu.|
|[Sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiku pro události připojení databáze SQL Database, nabízí přehled databáze připojení úspěchy a selhání. |
|[Sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšná připojení databáze Azure SQL Database, selhání připojení a zablokování. Tyto informace můžete použít ke sledování nebo řešení potíží s aktivitu své databáze s využitím SQL Database.|
|[příkaz sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro váš server SQL Database. Tuto uloženou proceduru lze pouze v hlavní databázi, a hlavní přihlášení na úrovni serveru. Pravidlo brány firewall na úrovni serveru můžete vytvořit pouze pomocí příkazů jazyka Transact-SQL po vytvoření prvního pravidla brány firewall na úrovni serveru uživatelem s oprávněními úrovně Azure|
|[Sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni serveru přidružené k Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Nastavení brány firewall na úrovni serveru odstraní z databáze SQL serveru. Tuto uloženou proceduru lze pouze v hlavní databázi, a hlavní přihlášení na úrovni serveru.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze pro Azure SQL Database nebo SQL Data Warehouse. Pravidla brány firewall databáze je možné nakonfigurovat pro hlavní databázi a uživatelských databází v SQL Database. Pravidla brány firewall databáze jsou užitečné při použití uživatelé databáze s omezením. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni databáze, které jsou přidružené k Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Nastavení brány firewall na úrovni databáze odebere z Azure SQL Database nebo SQL Data Warehouse. |

> [!TIP]
> Rychlý start pomocí SQL Server Management Studio na Microsoft Windows, naleznete v tématu [Azure SQL Database: Použít SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md). Rychlý start v systému macOS, Linux nebo Windows pomocí Visual Studio Code, naleznete v tématu [Azure SQL Database: Použití Visual Studio Code k připojení a dotazování dat](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí rozhraní REST API

K vytváření a správě serveru Azure SQL, databází a bran firewall, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje nový server.|
|[Servery – odstranit](https://docs.microsoft.com/rest/api/sql/servers/delete)|Odstraní systému SQL server.|
|[Servery – Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Získá serveru.|
|[Servery – seznam](https://docs.microsoft.com/rest/api/sql/servers/list)|Vrátí seznam serverů.|
|[Servery – seznam podle skupin prostředků](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Vrátí seznam serverů ve skupině prostředků.|
|[Servery – aktualizace](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje existující server.|
|[Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze – odstranit](https://docs.microsoft.com/rest/api/sql/databases/delete)|Odstraní databázi.|
|[Databáze - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam serverem](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Pravidla – brány firewall vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Vytvoří nebo aktualizuje pravidla brány firewall.|
|[Pravidla brány firewall – odstranit](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Odstraní pravidlo brány firewall.|
|[Pravidla brány firewall – Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Získá pravidla brány firewall.|
|[Pravidla brány firewall – seznam serverem](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Vrátí seznam pravidel brány firewall.|

## <a name="next-steps"></a>Další postup

- Další informace o migraci databáze SQL serveru do Azure najdete v tématu [migrace do služby Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
