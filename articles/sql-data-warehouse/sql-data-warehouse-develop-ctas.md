---
title: Vytvoření TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro programování s příkazem Vytvořit TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: igorstan
ms.openlocfilehash: f791f460efec1b84533379e74add003619dbac6f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521563"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Pomocí CREATE TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse
Tipy pro programování s příkazem Vytvořit TABLE AS SELECT (CTAS) jazyka T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-is-create-table-as-select-ctas"></a>Co je vytvořit TABLE AS SELECT (CTAS)?

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) nebo příkazu CTAS je jednou z vašich nejdůležitějších funkcí T-SQL k dispozici. To je paralelní operace, která vytvoří novou tabulku na základě výstupu příkazu SELECT. Funkce CTAS je nejjednodušší a nejrychlejší způsob, jak vytvořit a vložení dat do tabulky pomocí jediného příkazu. 

## <a name="selectinto-vs-ctas"></a>VYBERTE... DO sady vs. CTAS
Můžete zvážit CTAS jako mimořádně účtovat podle verze [vyberte... DO](/sql/t-sql/queries/select-into-clause-transact-sql) příkazu.

Níže je příklad jednoduchý příkaz SELECT... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VYBERTE... Ale neumožňuje změnit metodu distribuce nebo typ indexu jako součást operace. `[dbo].[FactInternetSales_new]` vytvoří výchozí typ distribuce jako ROUND_ROBIN a struktura tabulky výchozí jako CLUSTEROVANÝ INDEX COLUMNSTORE.

Použití příkazu CTAS, budete moct zadat rozdělení tabulky dat, stejně jako typ struktury tabulky.
Chcete-li převést předchozího příkladu na CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

 

> [!NOTE]
> Pokud se pouze snažíte změnit index vaše `CTAS` operace a zdrojová tabulka je provádět distribuci hodnot hash vaše `CTAS` operace provede nejlépe, pokud chcete zachovat stejný typ sloupce a data distribuce. Tím se vyhnete pro různé distribuce přesun dat v průběhu operace, což je efektivnější.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Použití příkazu CTAS k kopírování tabulky
Například jeden z nejčastěji používaných používá `CTAS` vytváří kopii tabulky, aby mohli měnit jazyka DDL. Pokud například jste původně vytvořili tabulky jako `ROUND_ROBIN` a chcete ho změnit na tabulka distribuovaná na sloupci, `CTAS` je, jak by změnit sloupec distribuce. `CTAS` je také možné změnit typy rozdělení do oddílů, indexování nebo sloupec.

Řekněme, že jste vytvořili tuto tabulku pomocí výchozí typ distribuce `ROUND_ROBIN` distribuované, protože byl zadán žádný sloupec distribuce v `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Teď chcete vytvořit novou kopii této tabulky s clusterovaný Columnstore Index, takže můžete využít výkon tabulek Clusterované Columnstore. Také chcete distribuovat této tabulky na ProductKey, protože jsou předvídání spojení na tomto sloupci a chcete zabránit přesunu dat během spojení ProductKey. A konečně můžete také chtít přidat dělení na OrderDateKey, takže můžete rychle odstranit stará data odstranit staré oddíly. Tady je příkaz CTAS staré tabulky budou zkopírovány do nové tabulky.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Nakonec můžete přejmenovat tabulek do odkládacího souboru v nové tabulky a pak vyřadit staré tabulky.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Použití příkazu CTAS obejít nepodporované funkce
Funkce CTAS lze také vyřešit řadu nepodporované funkce uvedené níže. Tato metoda může být velmi často se situace win/win jako pouze váš kód bude kompatibilní s, ale to se často spustí rychleji na SQL Data Warehouse. Tento výkon je výsledkem jeho plně paralelizované návrhu. Mezi scénáře, které je možné pracovat s CTAS kolem patří:

* SPOJENÍ standardu ANSI na aktualizace
* Spojení standardu ANSI na odstranění
* SLOUČENÍ – příkaz

> [!NOTE]
> Zamyslete se nad "CTAS první". Pokud si myslíte, že můžete řešit potíže s použitím `CTAS` , který je obvykle nejlepší způsob, jak přistupovat ke – i v případě, že píšete víc dat v důsledku.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Nahrazení spojení standardu ANSI pro příkazy update
Může se stát, že máte komplexní aktualizace, které spojí dohromady pomocí syntaxe spojení standardu ANSI provádět aktualizace nebo odstranění více než dvě tabulky.

Představte si, že jste museli aktualizovat v této tabulce:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Původní dotaz může mít podívali přibližně takto:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Protože SQL Data Warehouse nepodporuje spojení standardu ANSI v `FROM` klauzuli `UPDATE` prohlášení, nelze zkopírovat tento kód přes beze změny mírně.

Můžete použít kombinaci `CTAS` a implicitní spojení nahradit tento kód:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Odstranit nahrazení spojení standardu ANSI pro příkazy
Někdy je nejlepším řešením pro odstranění dat použít `CTAS`. Místo odstranění dat, vyberte data, která chcete zachovat. To platí zejména pro `DELETE` příkazy, které používají ANSI syntaxe připojit, protože SQL Data Warehouse nepodporuje spojení standardu ANSI `FROM` klauzuli `DELETE` příkazu.

Příklad převedený příkazu DELETE je k dispozici následující:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Nahraďte příkazy merge
Příkazy Merge lze nahradit, alespoň v části použití příkazu CTAS. VKLÁDÁNÍ a aktualizace můžete konsolidovat v jediném příkazu. Odstraněné záznamy, které by měly být omezené z `SELECT` příkaz chcete vynechat, nechte ve výsledcích.

Následující příklad je určený pro UPSERT:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Funkce CTAS doporučení: Explicitně uvést typ dat a možnost použití hodnoty Null z výstupu
Při přenesení kódu, můžete se setkat, že spustíte mezi tímto typem kódování vzorců:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinktivně si možná myslíte tento kód by měl migrovat do příkazu CTAS a budete mít správný. Je však skryté problém tady.

Následující kód nevydává stejný výsledek:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Všimněte si, že sloupec "výsledek" přenesou data hodnot typu a možnost použití hodnoty Null z výrazu. To může vést k drobným odchylky hodnot, pokud nevyloučíte.

Vyzkoušejte následující příklad:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Hodnota uložená pro výsledek se liší. Trvalé hodnoty ve sloupci výsledek při použití v jiných výrazech, stane chyba ještě více významné.

![Výsledky příkazu CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

To je zvlášť důležité pro data migrace. I když druhý dotaz je pravděpodobně přesnější dojde k problému. Data bude odlišná v porovnání s zdrojového systému a, která vede na otázky integrity migrace. Toto je jedna z těchto výjimečných případech, kdy "nesprávný" odpověď je ve skutečnosti ten správný!

Z důvodu, že můžeme vidět tento rozdíl mezi dvěma výsledky je implicitní přetypování. V prvním příkladu definuje tabulce definici sloupce. Při vložení řádku dochází implicitní převod typu. V druhém příkladu neexistuje žádný implicitní převod typu jako výrazu definuje datový typ sloupce. Všimněte si také, že sloupce v druhém příkladu byl definovaný jako sloupec s možnou hodnotou Null vzhledem k tomu v prvním příkladu má není. Pokud tabulka byla vytvořena v prvním příkladu, byla explicitně definovány možnosti použití hodnoty NULL sloupce. V druhém příkladu byla ponechána výrazu a ve výchozím nastavení, výsledkem by byla definice hodnotu NULL.  

Jak tyto problémy vyřešit, musíte explicitně nastavit možnosti použití hodnoty Null a převodu typu vyberte část příkazu CTAS. V části Vytvoření tabulky nelze nastavit tyto vlastnosti.

Následující příklad ukazuje, jak kód opravit:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Je třeba počítat s následujícím:

* CAST nebo CONVERT byl použit
* ISNULL slouží k vynucení možnosti použití hodnoty Null nejsou COALESCE
* ISNULL je nejzevnější funkce
* Druhá část ISNULL je konstanta, tzn. 0

> [!NOTE]
> Pro možnost použití hodnoty Null se správně nastavit je potřeba použít ISNULL a ne COALESCE. COALESCE není deterministické funkce a tedy výsledkem výrazu bude vždy s možnou hodnotou Null. ISNULL se liší. Není deterministický. Proto když je druhá část funkce ISNULL konstanta nebo literál pak výsledná hodnota bude NOT NULL.
> 
> 

Tento tip není právě užitečná pro zajištění integrity svých výpočtech. Je také důležité pro přepínání oddílů tabulky. Představte si, že máte této tabulce jsou definované jako vaše fakt:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Hodnota pole je však počítané výrazu není součástí zdrojová data.

K vytvoření datové sady oddílů, může být vhodné provést následující kroky:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Dotaz spustí naprosto bez problémů. Problém je při pokusu o provedení přepnutí oddílu. Definice tabulek se neshodují. Chcete-li definice tabulky shodovat, musí být upravena přidat příkazu CTAS `ISNULL` funkce k zachování atributu možnosti použití hodnoty NULL sloupce.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Zobrazí se proto, že typ konzistence a Správa vlastností možnosti použití hodnoty Null na provedení příkazu CTAS je osvědčené technické praxe nejlepší. Pomáhá zachovat integritu ve svých výpočtech a také zajišťuje, že je možné přepínání oddílů.

Odkazovat [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentaci. Je jedním z nejdůležitějších příkazy ve službě Azure SQL Data Warehouse. Zajistěte, aby že ho důkladně porozumět.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

