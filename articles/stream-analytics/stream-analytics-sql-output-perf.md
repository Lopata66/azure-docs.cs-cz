---
title: Azure Stream Analytics výstup do Azure SQL Database
description: Přečtěte si, jak vypisuje data SQL Azure z Azure Stream Analytics a dosahují vyšších sazeb propustnosti zápisu.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443603"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure Stream Analytics výstup do Azure SQL Database

Tento článek popisuje tipy, které vám pomůžou dosáhnout lepšího výkonu propustnosti zápisu při načítání dat do SQL Azure databáze pomocí Azure Stream Analytics.

Výstup SQL v Azure Stream Analytics podporuje paralelní zápis jako možnost. Tato možnost umožňuje [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie úloh, ve kterých je paralelní zápis více výstupních oddílů do cílové tabulky. Povolení této možnosti v Azure Stream Analytics ale nemusí být dostačující pro dosažení vyšší propustnosti, protože na SQL Azure konfiguraci databáze a schématu tabulky záleží výrazně. Výběr indexů, klíčů clusteringu, faktoru plnění indexu a komprese má vliv na dobu načítání tabulek. Další informace o tom, jak optimalizovat databázi SQL Azure pro zlepšení výkonu dotazů a zatížení na základě vnitřních srovnávacích testů, najdete v tématu [Průvodce výkonem služby SQL Database](../sql-database/sql-database-performance-guidance.md). Řazení zápisů není zaručené při psaní paralelně s SQL Azure databáze.

Tady je několik konfigurací v rámci každé služby, které mohou pomoci zlepšit celkovou propustnost vašeho řešení.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Zdědit dělení** – Tato možnost Konfigurace výstupu SQL umožňuje dědění schématu dělení předchozího kroku dotazu nebo vstupu. Když je tato možnost povolená, zapisuje se do tabulky založené na disku a má [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie pro vaši práci. očekává se, že se zobrazí lepší propustnost. Tento oddíl již probíhá automaticky pro mnoho dalších [výstupů](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Uzamykání tabulek (byla TABLOCK) je také zakázáno pro hromadné vložení vytvořené s touto možností.

> [!NOTE] 
> Pokud je k dispozici více než 8 vstupních oddílů, nemusí být dědění vstupního schématu dělení vhodné. Tento horní limit byl pozorován v tabulce s jedním sloupcem identity a clusterovaným indexem. V takovém případě zvažte, jestli v dotazu použijete [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8, jestli chcete explicitně zadat počet modulů pro zápis výstupu. Na základě schématu a volby indexů se může vaše pozorování lišit.

- **Velikost dávky** – Konfigurace výstupu SQL umožňuje zadat maximální velikost dávky ve Azure Stream Analytics výstupu SQL na základě povaze cílové tabulky nebo úlohy. Velikost dávky je maximální počet záznamů, které se odesílají při každé transakci hromadného vložení. V clusterovaných indexech columnstore můžou velikosti dávek kolem [100 tisíc](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umožňovat více paralelních, minimálního protokolování a optimalizace zámků. V tabulkách založených na disku může být pro vaše řešení optimální (výchozí) nebo nižší, protože vyšší velikosti dávek můžou během hromadných vložení aktivovat zvýšení úrovně zámku.

- **Optimalizace vstupních zpráv** – Pokud jste optimalizovani pomocí dědění a velikosti dávek, zvýšením počtu vstupních událostí na jednu zprávu na oddíl pomůžete dalšímu doručování propustnosti zápisu. Ladění vstupní zprávy umožňuje velikost dávky v rámci Azure Stream Analytics až po zadanou velikost dávky, což zlepšuje propustnost. Toho je možné dosáhnout použitím [Komprese](stream-analytics-define-inputs.md) nebo zvýšení velikosti vstupních zpráv v centru EventHub nebo v objektu BLOB.

## <a name="sql-azure"></a>SQL Azure

- **Dělená tabulka a indexy** – použití [dělené](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabulky SQL a dělené indexy v tabulce se stejným sloupcem, jako má klíč oddílu (například PartitionID), může významně snížit počet kolizí mezi oddíly během zápisu. V případě dělené tabulky budete muset vytvořit [funkci oddílu](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) a [schéma oddílu](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) v primární skupině souborů. Tím se také zvýší dostupnost stávajících dat při načítání nových dat. Omezení v/v protokolu může být dosaženo na základě počtu oddílů, které lze zvýšit upgradováním SKU.

- **Vyhněte se narušení jedinečnosti klíčů** – Pokud se v protokolu Azure Stream Analyticsch aktivit zobrazí [více výstražných zpráv o porušení klíčů](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) , zajistěte, aby vaše úloha neovlivnila omezením jedinečnosti omezení, která se pravděpodobně vyskytují během případů obnovení. To je možné vyhnout nastavením možnosti [Ignorovat\_klíč duplicity\_](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) na vašich indexech.

## <a name="azure-data-factory-and-in-memory-tables"></a>Tabulky Azure Data Factory a v paměti

- **Tabulka v paměti jako dočasná tabulka** – [tabulky v paměti](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umožňují velmi vysokorychlostní načítání dat, ale data se musí vejít do paměti. Srovnávací testy ukazují hromadné načítání z tabulky v paměti do tabulky založené na disku je přibližně desetkrát rychlejší než přímé hromadné vložení pomocí jediného zapisovače do tabulky založené na disku se sloupcem identity a clusterovaným indexem. Chcete-li využít tento hromadný výkon, nastavte [úlohu kopírování pomocí Azure Data Factory](../data-factory/connector-azure-sql-database.md) , která kopíruje data z tabulky v paměti do tabulky založené na disku.

## <a name="avoiding-performance-pitfalls"></a>Zamezení nástrah výkonu
Hromadné vkládání dat je mnohem rychlejší než načítání dat pomocí jednoduchých vložení, protože opakovaná režie přenosu dat, analýza příkazu INSERT, spuštění příkazu a vystavení záznamu transakce se vyhne. Místo toho se k streamování dat v modulu úložiště používá efektivnější cesta. Náklady na instalaci této cesty jsou mnohem vyšší než jeden příkaz INSERT v tabulce založené na disku. Mezi 100 řádků, které jsou téměř vždy efektivnější, se obvykle pohybuje hranice. 

Pokud je míra příchozích událostí nízká, může snadno vytvořit velikosti dávek nižší než 100 řádků, což znamená, že hromadné vložení je neefektivní a využívá příliš mnoho místa na disku. Chcete-li toto omezení obejít, můžete provést jednu z následujících akcí:
* Vytvořte místo [triggeru](/sql/t-sql/statements/create-trigger-transact-sql) pro použití jednoduchého vložení pro každý řádek.
* Použijte dočasnou tabulku v paměti, jak je popsáno v předchozí části.

K dalšímu takovému scénáři dochází při psaní do neclusterovaných indexů columnstore (NCCI), kde menší hromadné vložení může vytvořit příliš mnoho segmentů, což může způsobit selhání indexu. V takovém případě doporučujeme místo toho použít clusterovaný index columnstore.

## <a name="summary"></a>Souhrn

V souhrnu se funkce děleného výstupu v Azure Stream Analytics pro výstup SQL zarovnala paralelnímu využití úlohy s dělenou tabulkou v SQL Azure by vám mělo poskytnout významné vylepšení propustnosti. Využití Azure Data Factory pro orchestraci přesunu dat z tabulky v paměti do tabulek založených na disku může mít za následek nárůst propustnosti velikosti. Pokud je to proveditelné, vylepšení hustoty zpráv může být také významným faktorem při zvyšování celkové propustnosti.
