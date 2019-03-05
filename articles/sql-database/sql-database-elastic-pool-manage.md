---
title: Vytvářet a spravovat elastické fondy – Azure SQL database | Dokumentace Microsoftu
description: Vytvoření a správa elastických fondů Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e35de707abe04702201969fdfd008fc9713fc391
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309736"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Vytvoření a správa elastických fondů Azure SQL Database

S elastickým fondem určení množství prostředků, které vyžaduje elastického fondu pro zpracování úloh z jeho databází a množství prostředků pro každou databázi ve fondu.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure portal: Spravovat elastické fondy a databáze ve fondu

Všechna nastavení fondu najdete na jednom místě: **konfigurace fondu** okno. Tady získáte najít elastického fondu na portálu a klikněte na **konfigurace fondu** buď z horní části okna nebo v nabídce prostředků na levé straně.

Odsud můžete provést libovolné z následujících změn a uložte si je všechny v jedné dávce:

1. Změnit úroveň služby, fondu
2. Výkon (DTU nebo virtuálních jader) a úložiště škálovat směrem nahoru nebo dolů
3. Přidat nebo odebrat databáze z fondu
4. Nastavte minimální (zaručeno, že) a maximální výkonnostní limit pro databáze ve fondech
5. Zkontrolujte souhrn nákladů, chcete-li zobrazit všechny změny na faktuře v důsledku nový výběr

![Okno Konfigurace elastického fondu](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Spravovat elastické fondy a databáze ve fondu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete vytvářet a spravovat databázi SQL elastické fondy a databáze ve fondu pomocí prostředí Azure PowerShell, použijte následující rutiny Powershellu. Pokud potřebujete instalaci nebo upgrade prostředí PowerShell, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud chcete vytvářet a spravovat servery SQL Database pro elastický fond, přečtěte si téma [vytvořit a spravovat servery SQL Database](sql-database-servers.md). Vytvoření a Správa pravidel brány firewall najdete v tématu [vytvoření a Správa pravidel brány firewall pomocí prostředí PowerShell](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell).

> [!TIP]
> Příklady skriptů Powershellu, najdete v části [vytváření elastických fondů a přesun databází mezi fondy a mimo fondu pomocí prostředí PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) a [pomocí Powershellu pro monitorování a škálování elastického fondu SQL ve službě Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Vytvoří elastický fond.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Získá elastických fondů a jejich hodnot vlastností.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Upraví vlastnosti elastického fondu pro příklad, použijte **StorageMB** vlastnost změnit maximální velikost úložiště elastického fondu.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Odstraní elastického fondu.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Získá stav operací v elastickém fondu|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří novou databázi v existující fond nebo izolovanou databázi. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti pro databáze nebo přesune databázi do, z nebo mezi elastickými fondy.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|

> [!TIP]
> Vytvoření mnoha databází v elastickém fondu může trvat dobu, pokud se provádí pomocí portálu nebo rutiny prostředí PowerShell jednu databázi po jednom. K automatizaci vytváření elastického fondu, naleznete v tématu [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Spravovat elastické fondy a databáze ve fondu

Můžete vytvářet a spravovat elastické fondy SQL Database s [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Příklad skripty rozhraní příkazového řádku Azure, najdete v části [pomocí rozhraní příkazového řádku pro přesun databáze Azure SQL v elastickém fondu SQL](scripts/sql-database-move-database-between-pools-cli.md) a [pomocí Azure CLI pro škálování elastického fondu SQL ve službě Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[Vytvoření az sql elastic-pool](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Vytvoří elastický fond.|
|[AZ sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Vrátí seznam hodnot elastické fondy na serveru.|
|[AZ sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Vrátí seznam databází v elastickém fondu.|
|[AZ sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Zahrnuje také nastavení DTU fondu k dispozici, omezení úložiště a nastavení na databázi. Pokud chcete snížit úroveň podrobností, omezení další úložiště a na databázi je ve výchozím nastavení skryje nastavení.|
|[AZ sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje elastického fondu.|
|[AZ sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Odstraní elastického fondu.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Správa databáze ve fondu

Vytvoření a přesunutí databází v rámci existující elastických fondů nebo který vrací informace o elastického fondu SQL Database pomocí jazyka Transact-SQL, použijte následující příkazy T-SQL. Můžete použít tyto příkazy pomocí webu Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který můžete připojit k serveru Azure SQL Database a předat příkazů jazyka Transact-SQL příkazy. Vytvoření a Správa pravidel brány firewall pomocí jazyka T-SQL najdete v tématu [Správa pravidel brány firewall pomocí příkazů jazyka Transact-SQL](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Nejde vytvořit, aktualizovat nebo odstranit elastický fond Azure SQL Database pomocí jazyka Transact-SQL. Můžete přidat nebo odebrat databáze z elastického fondu a zobrazení dynamické správy můžete použít k vrácení informací o existujících elastických fondů.
>

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi v existující fond nebo izolovanou databázi. Musíte být připojení k hlavní databázi, a vytvořit novou databázi.|
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přesunutí databáze do, z nebo mezi elastickými fondy.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[Sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Vrátí statistiky o využití prostředků pro elastické fondy na serveru SQL Database. Pro každý elastický fond je jeden řádek pro každou 15 sekundu reporting okno (čtyři řádky za minutu). To zahrnuje využití procesoru, vstupně-výstupních operací, protokolu, spotřebu úložiště a využití souběžný požadavek/relace všechny databáze ve fondu.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (úroveň služby), cíl služby (cenová úroveň) a název elastického fondu, pokud existují pro službu Azure SQL database nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi na serveru Azure SQL Database, vrátí informace ve všech databázích. Pro službu Azure SQL Data Warehouse musí být připojené k hlavní databázi.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Spravovat elastické fondy a databáze ve fondu

Můžete vytvářet a spravovat databázi SQL elastické fondy a databáze ve fondu, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Elastické fondy – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Vytvoří nový elastický fond nebo aktualizuje existující elastického fondu.|
|[Elastické fondy – odstranit](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Odstraní elastického fondu.|
|[Elastické fondy – Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Získá elastického fondu.|
|[Elastické fondy – seznam serverem](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Vrátí seznam hodnot elastické fondy na serveru.|
|[Elastické fondy – aktualizace](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Aktualizace existujícího elastického fondu.|
|[Aktivity elastického fondu](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Vrátí aktivity elastického fondu.|
|[Elastický fond v činnosti databáze](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Vrátí aktivity u databází v elastickém fondu.|
|[Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam serverem](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje existující databázi.|

## <a name="next-steps"></a>Další postup

* Video najdete v tématu [videokurz Microsoft Virtual Academy o možnostech elastické databáze SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Kurz SaaS využívajících elastické fondy najdete v tématu [Úvod do aplikací SaaS aplikace Wingtip](sql-database-wtp-overview.md).
