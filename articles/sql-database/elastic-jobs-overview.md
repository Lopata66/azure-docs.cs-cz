---
title: Úlohy elastické databáze Azure SQL | Microsoft Docs
description: Konfigurace úloh Elastic Database pro spouštění skriptů jazyka Transact-SQL (T-SQL) v rámci jedné nebo více databází SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 7c5905716c0aada4a5070b9968c330eafaffb741
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561331"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Vytváření, konfigurace a Správa elastických úloh

V tomto článku se dozvíte, jak vytvářet, konfigurovat a spravovat elastické úlohy. Pokud jste elastické úlohy nepoužívali, [Přečtěte si další informace o konceptech automatizace úloh v Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Vytvoření a konfigurace agenta

1. Vytvořte nebo určete prázdnou databázi SQL S0 nebo vyšší. Tato databáze bude použita jako *databáze úlohy* během vytváření agenta elastické úlohy.
2. Na [portálu](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) nebo pomocí [PowerShellu](elastic-jobs-powershell.md#create-the-elastic-job-agent) vytvořte agenta elastických úloh.

   ![Vytváří se agent elastické úlohy.](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Vytváření, spouštění a správa úloh

1. Pomocí [PowerShellu](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) nebo [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution) vytvořte přihlašovací údaje pro spouštění úloh v *databázi úloh*.
2. Pomocí [PowerShellu](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) nebo [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers) definujte cílovou skupinu (databáze, pro které chcete úlohu spouštět).
3. V každé databázi, ve které se bude úloha spouštět, vytvořte přihlašovací údaje agenta úloh [(do každé databáze ve skupině přidejte příslušného uživatele nebo roli)](sql-database-control-access.md). Příklad najdete v [kurzu pro PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets).
4. Pomocí [PowerShellu](elastic-jobs-powershell.md#create-a-job) nebo [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) vytvořte úlohu.
5. Pomocí [PowerShellu](elastic-jobs-powershell.md#create-a-job-step) nebo [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) přidejte kroky úlohy.
6. Pomocí [PowerShellu](elastic-jobs-powershell.md#run-the-job) nebo [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job) spusťte úlohu.
7. Pomocí portálu, [PowerShellu](elastic-jobs-powershell.md#monitor-status-of-job-executions) nebo [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status) monitorujte stav provádění úlohy.

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Přihlašovací údaje pro spouštění úloh

Úlohy se při spuštění připojují k databázím určeným cílovou skupinou pomocí [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Pokud cílová skupina obsahuje servery nebo fondy, použijí se tyto přihlašovací údaje v oboru databáze k připojení k hlavní databázi a výčtu dostupných databází.

Nastavení správných přihlašovacích údajů pro spuštění úlohy může být trochu matoucí, proto mějte na paměti následující body:

- Přihlašovací údaje v oboru databáze se musí vytvořit v *databázi úloh*.
- **Všechny cílové databáze musí mít přihlašovací údaje s [dostatečným oprávněním](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) , aby se úloha úspěšně dokončila** (`jobuser` v diagramu níže).
- Přihlašovací údaje je možné opakovaně používat napříč úlohami a hesla přihlašovacích údajů jsou šifrovaná a zabezpečená uživatelům, kteří mají k objektům úlohy přístup jen pro čtení.

Následující obrázek by vám měl pomoct porozumět přihlašovacím údajům úloh a správně je nastavit. **Nezapomeňte vytvořit příslušného uživatele v každé databázi (ve všech *cílových uživatelských databázích*), ve které se má úloha spouštět**.

![Přihlašovací údaje k elastickým úlohám](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Tady je několik osvědčených postupů, které byste při práci s elastickými úlohami měli brát v úvahu:

- Omezte možnost používat rozhraní API na důvěryhodné osoby.
- Přihlašovací údaje by měly mít nejnižší úroveň oprávnění nezbytnou k provedení daného kroku úlohy. Další informace najdete v tématu [autorizace a oprávnění SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Při použití členu cílové skupiny serveru nebo fondu je důrazně navrženo vytvoření samostatného pověření s právy k hlavní databázi k zobrazení nebo zobrazení seznamu databází, které se používají k rozšíření seznamů databáze serverů a/nebo fondů před provedením úlohy.

## <a name="agent-performance-capacity-and-limitations"></a>Výkon, kapacita a omezení agenta

Elastické úlohy při čekání na dokončení dlouhotrvajících úloh využívají minimum výpočetních prostředků.

V závislosti na velikosti cílové skupiny databází a požadované době spuštění úlohy (počet souběžných pracovních procesů) vyžaduje agent pro *databázi úloh* různé úrovně výpočetních prostředků a výkonu (čím je více cílů a úloh, tím je potřeba více výpočetních prostředků).

V současné době je verze Preview omezená na 100 souběžných úloh.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zabránění snižování výkonu cílových databází úlohami

Pokud chcete zajistit, aby při spouštění úloh pro databáze v elastickém fondu SQL nedocházelo k přetížení prostředků, můžete pro úlohy nakonfigurovat omezení počtu databází, pro které se můžou najednou spouštět.

Nastavte počet souběžných databází, na kterých úloha běží, nastavením `sp_add_jobstep` `@max_parallelism` parametru uložené procedury v T-SQL nebo `Add-AzSqlElasticJobStep -MaxParallelism` v PowerShellu.

## <a name="best-practices-for-creating-jobs"></a>Osvědčené postupy pro vytváření úloh

### <a name="idempotent-scripts"></a>Idempotentní skripty
Skripty T-SQL úlohy musí být [idempotentní](https://en.wikipedia.org/wiki/Idempotence). **Idempotentní** znamená, že pokud se skript úspěšně provedete a spustí se znovu, výsledek bude stejný. Skript může selhat kvůli přechodným problémům se sítí. V takovém případě se úloha automaticky pokusí znovu skript spustit tolikrát, kolikrát je uvedeno v předvolbách, a pak přestane. Výsledek idempotentního skriptu je stejný, i když se úspěšně spustí dvakrát (nebo vícekrát).

Jednoduchou taktikou je před vytvořením objektu otestovat, jestli už neexistuje.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Skript se také musí být schopný úspěšně provést. Dosáhne toho tím, že logicky testuje podmínky a reaguje na případné podmínky, které najde.



## <a name="next-steps"></a>Další postup

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
