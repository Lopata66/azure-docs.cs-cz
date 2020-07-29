---
title: Klasifikace úloh
description: Pokyny k používání klasifikace pro správu souběžnosti, důležitosti a výpočetních prostředků pro dotazy ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 266eebc8322b5fc648180c0524abc973a4b60373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212373"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Klasifikace úloh Azure synapse Analytics

Tento článek vysvětluje proces klasifikace úloh přiřazení skupiny úloh a důležitosti pro příchozí žádosti s synapse fondy SQL v Azure synapse.

## <a name="classification"></a>Classification

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klasifikace správy úloh umožňuje, aby byly zásady úloh aplikovány na požadavky prostřednictvím přiřazení [tříd prostředků](resource-classes-for-workload-management.md#what-are-resource-classes) a [důležitosti](sql-data-warehouse-workload-importance.md).

I když existuje mnoho způsobů klasifikace úloh datového skladu, nejjednodušší a nejběžnější klasifikace je zatížení a dotazování. Data se načítají pomocí příkazů INSERT, Update a DELETE.  Dotazování na data pomocí příkazu vybrat. Řešení datového skladu bude často obsahovat zásady úloh pro aktivity načítání, jako je například přiřazení vyšší třídy prostředků k více prostředkům. Na dotazy, jako je nižší důležitost v porovnání s aktivitami načtení, se můžou vztahovat jiné zásady zatížení.

Můžete také podklasifikovat zatížení a úlohy dotazů. Podklasifikace nabízí větší kontrolu nad úlohami. Například úlohy dotazů se můžou skládat z aktualizačních datových krychlí, dotazů na řídicích panelů nebo dotazů ad hoc. Každou z těchto úloh dotazu můžete klasifikovat pomocí různých tříd prostředků nebo nastavení důležitosti. Zatížení může také těžit z podklasifikace. Velké transformace lze přiřadit k větším třídám prostředků. Vyšší důležitost se dá použít k zajištění toho, aby se klíčová data o prodeji využívala před povětrnostními daty nebo datovým kanálem.

Ne všechny příkazy jsou klasifikovány, protože nevyžadují prostředky nebo potřebují význam pro ovlivnění provádění.  Příkazy DBCC, BEGIN, COMMIT a transakce vrácení zpět nejsou klasifikovány.

## <a name="classification-process"></a>Proces klasifikace

Klasifikace pro synapse fond SQL ve službě Azure synapse se dosáhla v dnešní době přiřazením uživatelů k roli, která má přiřazenou odpovídající třídu prostředků pomocí [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Možnost charakterizovat požadavky nad rámec přihlášení k třídě prostředků je omezená touto funkcí. K dispozici je širší metoda klasifikace, která je teď dostupná pomocí syntaxe pro [Vytvoření třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Pomocí této syntaxe mohou uživatelé synapse ve fondu SQL přiřadit důležitost a množství systémových prostředků, které jsou k žádosti přiřazeny přes `workload_group` parametr.

> [!NOTE]
> Klasifikace se vyhodnocuje na základě jednotlivých požadavků. Více požadavků v jedné relaci lze klasifikovat odlišně.

## <a name="classification-weighting"></a>Váha klasifikace

V rámci procesu klasifikace je pro určení, která skupina úloh přiřazena, k dismístění vážení.  Vážení probíhá takto:

|Parametr klasifikátoru |Hmotnost   |
|---------------------|---------|
|MEMBER: UŽIVATEL      |64       |
|MEMBER: ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername`Parametr je povinný.  Pokud je však zadaný member uživatel databáze, nikoli databázová role, je váha pro uživatele vyšší, a proto je zvolen klasifikátor.

Pokud je uživatel členem více rolí s přiřazenými nebo odpovídajícími různými třídami prostředků v několika klasifikátorech, uživateli se udělí nejvyšší přiřazení třídy prostředku.  Toto chování je konzistentní s existujícím chováním přiřazení tříd prostředků.

## <a name="system-classifiers"></a>Třídění systému

Klasifikace úloh má klasifikátory zatížení systému. Třídění systémových klasifikátorů mapují existující členství v rolích tříd prostředků na přidělení prostředků třídy prostředků s normální důležitostí. Systémové třídění nelze vyřadit. Chcete-li zobrazit třídění systému, můžete spustit následující dotaz:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Kombinování přiřazení tříd prostředků s klasifikátory

Systémová třídění vytvořená vaším jménem poskytují jednoduchou cestu k migraci na klasifikaci úloh. Použití mapování rolí tříd prostředků s prioritou klasifikace může vést k netřídění, když začnete vytvářet nové klasifikátory s důležitostí.

Představte si následující scénář:

- Existující datový sklad má DBAUser uživatele databáze přiřazený k roli třídy prostředků largerc. Bylo provedeno přiřazení třídy prostředků s sp_addrolemember.
- Datový sklad je nyní aktualizován se správou úloh.
- Chcete-li otestovat novou syntaxi klasifikace, je role databáze DBARole (která DBAUser je členem), má klasifikátor vytvořený pro jejich mapování na mediumrc a vysokou důležitost.
- Když se DBAUser přihlásí a spustí dotaz, dotaz se přiřadí k largerc. Vzhledem k tomu, že má uživatel přednost před členstvím v rolích.

Při řešení potíží s chybnou klasifikací doporučujeme, abyste při vytváření klasifikátorů úloh odebrali mapování rolí tříd prostředků.  Následující kód vrátí existující členství v roli třídy prostředku.  Spusťte [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý název člena vrácený z odpovídající třídy prostředku.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Další kroky

- Další informace o vytvoření klasifikátoru najdete v tématu [Vytvoření klasifikátoru úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Projděte si rychlý Start, jak vytvořit klasifikátor úloh [vytvořit klasifikátor úloh](quickstart-create-a-workload-classifier-tsql.md).
- V článcích s postupy můžete [nakonfigurovat důležitost úloh](sql-data-warehouse-how-to-configure-workload-importance.md) a [Spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- V tématu [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) můžete zobrazit dotazy a přiřazené důležitost.
