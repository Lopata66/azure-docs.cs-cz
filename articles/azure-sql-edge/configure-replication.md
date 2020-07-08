---
title: Konfigurace replikace na Edge Azure SQL (Preview)
description: Přečtěte si o konfiguraci replikace do Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a33933e63cc7c15de7d60430521f810f8546988c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667875"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Konfigurace replikace na Edge Azure SQL (Preview) 

Můžete nakonfigurovat instanci Azure SQL Edge jako předplatitel nabízených oznámení pro jednosměrnou transakční replikaci nebo replikaci snímků. Tato instance nemůže fungovat jako Vydavatel nebo distributor pro konfiguraci transakční replikace. Všimněte si, že Azure SQL Edge nepodporuje slučovací replikaci, replikaci peer-to-peer ani publikování Oracle.

## <a name="supported-configurations"></a>Podporované konfigurace
  
- Instance Azure SQL Edge musí být předplatitelem nabízených oznámení pro vydavatele.
- Vydavatel a distributor můžou mít jednu z těchto akcí:
   - Instance SQL Server spuštěná v místním prostředí nebo instance SQL Server spuštěná na virtuálním počítači Azure. Další informace najdete v tématu [přehled SQL Server v Azure Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). Instance SQL Server musí používat verzi novější než SQL Server 2016.
   - Instance spravované instance Azure SQL Spravovaná instance SQL může hostovat databáze vydavatelů, distributorů a předplatitelů. Další informace najdete v tématu [replikace s SQL Database Managed instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Distribuční databáze a agenti replikace nejde umístit do instance Azure SQL Edge.  

> [!NOTE]
> Pokud se pokusíte nakonfigurovat replikaci pomocí nepodporované verze, může dojít k následujícím dvěma chybám: MSSQL_REPL20084 ("proces se nemohl připojit k odběrateli") a MSSQL_REPL40532 ("nelze otevřít server \<name> požadovaný přihlášením. Přihlášení se nezdařilo. ").  

## <a name="remarks"></a>Poznámky

Při konfiguraci replikace je důležité pochopit následující požadavky a osvědčené postupy:

- Replikaci můžete nakonfigurovat pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Můžete to provést také tak, že spustíte příkazy jazyka Transact-SQL na vydavateli pomocí SQL Server Management Studio nebo [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- Chcete-li provést replikaci do instance Azure SQL Edge, je nutné se přihlásit pomocí SQL Server ověřování.
- Replikované tabulky musí mít primární klíč.
- Jedna publikace na SQL Server může podporovat Azure SQL Edge i SQL Server (místně i SQL Server ve virtuálních počítačích Azure).  
- Správa replikace, monitorování a řešení potíží je nutné provést z instance SQL Server.  
- Podporují se jenom nabízená předplatná pro Azure SQL Edge.  
- `@subscriber_type = 0`V uložené proceduře `sp_addsubscription` pro Azure SQL Edge se podporuje jenom.  
- Azure SQL Edge nepodporuje replikaci obousměrné, okamžité, aktualizovatelné ani peer-to-peer.
- Azure SQL Edge podporuje pouze podmnožinu funkcí, které jsou k dispozici v SQL Server nebo spravované instanci SQL. Pokud se pokusíte o replikaci databáze (nebo objektů v rámci databáze), která obsahuje jednu nebo více nepodporovaných funkcí, pokus se nezdaří. Například pokud se pokusíte o replikaci databáze, která obsahuje objekty s prostorovými datovými typy, dojde k chybě. Další informace najdete v tématu [podporované funkce Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Inicializace referenčních dat v instanci Azure SQL Edge

Můžete chtít inicializovat instanci s referenčními daty, která se v průběhu času mění. Můžete například chtít, aby modely strojového učení byly na vaší instanci Azure SQL Edge vyškolené na instanci SQL Server. Tady je postup inicializace vaší instance v takovém scénáři:

1. Vytvoření publikace transakční replikace v databázi SQL Server.  
2. V SQL Server instanci použijte **Průvodce novým odběrem** nebo příkazy jazyka Transact-SQL k vytvoření odběru nabízeného oznámení pro Azure SQL Edge.  
3. Replikovanou databázi můžete na Azure SQL Edge inicializovat pomocí snímku generovaného agentem snímku, který distribuuje a doručuje agentem distribuce. Alternativně můžete provést inicializaci pomocí zálohy databáze od vydavatele. Pamatujte, že pokud záloha databáze obsahuje objekty nebo funkce, které Azure SQL Edge nepodporuje, operace obnovení se nezdařila.

## <a name="limitations"></a>Omezení

Pro odběry Azure SQL Edge nejsou podporované následující možnosti:

- Kopírovat přidružení skupin souborů  
- Kopírovat schémata dělení tabulky  
- Kopírovat schémata dělení indexů  
- Kopírovat uživatelsky definované statistiky  
- Kopírovat výchozí vazby  
- Kopírovat vazby pravidla  
- Kopírovat fulltextové indexy  
- Kopírovat XSD XML  
- Kopírovat indexy XML  
- Kopírovat oprávnění  
- Kopírovat prostorové indexy  
- Kopírovat filtrované indexy  
- Kopírovat atribut komprese dat  
- Kopírovat atribut zhuštěného sloupce  
- Kopírování `hierarchyid` datových typů FILESTREAM, nebo prostorových dat
- Převést `hierarchyid` na maximum datových typů  
- Převod prostorových datových typů na maximum  
- Kopírovat rozšířené vlastnosti  
- Kopírovat oprávnění  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízený odběr. Další informace naleznete v tématech:
  
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořte nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) pomocí názvu serveru Azure SQL Edge a IP adresy jako předplatitele (například **myEdgeinstance, 1433**) a názvu databáze v instanci Azure SQL Edge jako cílovou databázi (například **AdventureWorks**).  

## <a name="next-steps"></a>Další kroky  

- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


