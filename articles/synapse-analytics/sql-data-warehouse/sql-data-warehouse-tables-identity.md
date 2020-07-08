---
title: Použití IDENTITY k vytváření náhradních klíčů
description: Doporučení a příklady použití vlastnosti IDENTITY k vytváření náhradních klíčů v tabulkách v synapse fondu SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 60f2e3f949a4f627839a07137ebaf77518db87a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213971"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Použití IDENTITY k vytváření náhradních klíčů v synapse fondu SQL

V tomto článku najdete doporučení a příklady použití vlastnosti IDENTITY k vytváření náhradních klíčů v tabulkách v synapse fondu SQL.

## <a name="what-is-a-surrogate-key"></a>Co je náhradní klíč

Náhradní klíč v tabulce je sloupec s jedinečným identifikátorem pro každý řádek. Klíč se negeneruje z dat tabulky. Datové modely, jako je vytváření náhradních klíčů ve svých tabulkách při návrhu modelů datového skladu. Vlastnost IDENTITY můžete použít k dosažení tohoto cíle jednoduše a efektivně, aniž by to ovlivnilo výkon zatížení.  

## <a name="creating-a-table-with-an-identity-column"></a>Vytvoření tabulky se sloupcem IDENTITY

Vlastnost IDENTITY je navržená pro horizontální navýšení kapacity napříč všemi distribucemi v synapse fondu SQL bez ovlivnění výkonu zatížení. Proto se implementace IDENTITY orientuje směrem k dosažení těchto cílů.

Můžete definovat tabulku, která má vlastnost IDENTITY při prvním vytvoření tabulky pomocí syntaxe, která je podobná následujícímu příkazu:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Pak můžete použít `INSERT..SELECT` k naplnění tabulky.

Tato zbývající část této části popisuje drobné odlišnosti implementace, která vám pomůže s jejich úplným pochopením.  

### <a name="allocation-of-values"></a>Přidělení hodnot

Vlastnost IDENTITY nezaručuje pořadí, ve kterém jsou přiděleny náhradní hodnoty, což odráží chování SQL Server a Azure SQL Database. V synapse fondu SQL se ale neexistence záruky vysloví.

Následující příklad je ilustrace:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

V předchozím příkladu byly vyloženo dva řádky v distribuci 1. První řádek má hodnotu náhrady 1 ve sloupci `C1` a druhý řádek má náhradní hodnotu 61. Obě tyto hodnoty byly vygenerovány vlastností IDENTITY. Přidělení hodnot však není souvislé. Toto chování je záměrné.

### <a name="skewed-data"></a>Zkosená data

Rozsah hodnot datového typu se rovnoměrně rozprostře napříč distribucí. Pokud bude distribuovaná tabulka z rozdělených dat vyčerpána, může být rozsah hodnot dostupných pro datový typ předčasně vyčerpán. Například pokud všechna data skončí v rámci jedné distribuce, pak efektivně má tabulka přístup pouze k sixtieth hodnot datového typu. Z tohoto důvodu je vlastnost IDENTITY omezená `INT` `BIGINT` jenom na datové typy.

### <a name="selectinto"></a>Vyberte.. USKLADNĚN

Když je sloupec existující IDENTITY vybraný do nové tabulky, zdědí nový sloupec vlastnost IDENTITY, pokud není splněná některá z následujících podmínek:

- Příkaz SELECT obsahuje spojení.
- Vícenásobné příkazy SELECT jsou spojeny pomocí SJEDNOCENí.
- Sloupec IDENTITY je v seznamu pro výběr uveden ve více než jednom okamžiku.
- Sloupec IDENTITY je součástí výrazu.

Pokud je jedna z těchto podmínek pravdivá, je sloupec vytvořen bez hodnoty NULL namísto dědění vlastnosti IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) se řídí stejným chováním SQL Server, které je popsané pro možnost vybrat. Uskladněn. V definici sloupce části příkazu ale nemůžete zadat vlastnost IDENTITY `CREATE TABLE` . V části CTAS také nemůžete použít funkci identita `SELECT` . K naplnění tabulky je nutné použít `CREATE TABLE` k definování tabulky, za kterou se `INSERT..SELECT` má naplnit.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitní vkládání hodnot do sloupce IDENTITY

Synapse fond SQL podporuje `SET IDENTITY_INSERT <your table> ON|OFF` syntaxi. Tuto syntaxi můžete použít k explicitnímu vkládání hodnot do sloupce IDENTITY.

Řada datových modelů, jako je například použití předdefinovaných záporných hodnot pro určité řádky v jejich dimenzích. Příkladem je řádek-1 nebo "Neznámý člen".

Následující skript ukazuje, jak tento řádek explicitně přidat pomocí SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Načítání dat

Přítomnost vlastnosti IDENTITY má nějaký vliv na váš kód pro načítání dat. Tato část popisuje několik základních vzorů pro načítání dat do tabulek pomocí IDENTITY.

Chcete-li načíst data do tabulky a vygenerovat náhradní klíč pomocí IDENTITY, vytvořte tabulku a pak použijte příkaz INSERT.. Vyberte nebo vložte... HODNOTY pro provedení zátěže.

Následující příklad zvýrazní základní vzor:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> V tuto chvíli není možné použít `CREATE TABLE AS SELECT` při načítání dat do tabulky se sloupcem identity.
>

Další informace o načítání dat najdete v tématu [Návrh extrakce, načítání a transformace (ELT) pro Synapseový fond SQL](design-elt-data-loading.md) a [načítání osvědčených postupů](guidance-for-loading-data.md).

## <a name="system-views"></a>Systémová zobrazení

K identifikaci sloupce, který má vlastnost IDENTITY, můžete použít zobrazení katalogu [Sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

V tomto příkladu, který vám pomůže lépe pochopit schéma databáze, tento příklad ukazuje, jak integrovat sys. identity_column s dalšími zobrazeními systémového katalogu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Omezení

Vlastnost IDENTITY se nedá použít:

- Když datový typ sloupce není INT nebo BIGINT
- Pokud je sloupec také distribučním klíčem
- Když je tabulka externí tabulkou

V synapse fondu SQL se nepodporují následující související funkce:

- [IDENTITA ()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Běžné úkoly

V této části najdete ukázkový kód, který můžete použít k provádění běžných úkolů při práci se sloupci IDENTITY.

Sloupec C1 je identita ve všech následujících úlohách.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Vyhledání nejvyšší přidělené hodnoty pro tabulku

Pomocí `MAX()` funkce určete nejvyšší přidělenou hodnotu pro distribuovanou tabulku:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Vyhledání počáteční hodnoty a přírůstku vlastnosti IDENTITY

Zobrazení katalogu můžete použít ke zjištění zvýšení identity a hodnot konfigurace počáteční hodnoty pro tabulku pomocí následujícího dotazu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Další kroky

- [Přehled tabulky](sql-data-warehouse-tables-overview.md)
- [Identita CREATE TABLE (Transact-SQL) (vlastnost)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
