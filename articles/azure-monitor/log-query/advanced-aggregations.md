---
title: Pokročilé agregace v dotazech protokolu Azure Monitor | Dokumentace Microsoftu
description: Popisuje některé pokročilejší možnosti agregace dostupné dotazů na protokoly Azure monitoru.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551749"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Pokročilé agregace v dotazů na protokoly Azure monitoru

> [!NOTE]
> By se měla Dokončit [agregace v Azure Monitor vyžaduje](./aggregations.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje některé pokročilejší možnosti agregace, která je k dispozici pro dotazy Azure Monitor.

## <a name="generating-lists-and-sets"></a>Generování seznamy a sad
Můžete použít `makelist` kontingenční data pořadím hodnot v určitém sloupci. Můžete třeba prozkoumat nejběžnější pořadí událostí se provádějí na počítačích. V podstatě můžete přesouvat data pořadím identifikátory EventID v rozmezí na každém počítači. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| POČÍTAČ1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` vygeneruje seznam v pořadí, ve kterém dat byl předán do něj. Chcete-li seřadit události od nejstarších k nejnovějším, použijte `asc` v příkazu pořadí místo `desc`. 

Je také užitečné vytvořit seznam pouze jedinečné hodnoty. Tento postup se nazývá _nastavit_ a dá vygenerovat pomocí `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| POČÍTAČ1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Stejně jako `makelist`, `makeset` také pracuje s seřazených dat a bude generovat pole na základě pořadí řádky, které jsou předány do něj.

## <a name="expanding-lists"></a>Rozbalování seznamů
Inverzní operace k `makelist` nebo `makeset` je `mvexpand`, který rozbalí seznam hodnot k oddělení řádků. Můžete rozbalit napříč libovolným počtem dynamické sloupce, JSON a pole. Například můžete zkontrolovat *prezenčního signálu* tabulky pro odesílání dat z počítačů, které odeslaly prezenční signál za poslední hodinu řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Řešení | 
|--------------|----------------------|
| POČÍTAČ1 | "security", "updates", "changeTracking" |
| computer2 | "zabezpečení", "aktualizace" |
| computer3 | "Antimalwarové", "sledování změn ve" |
| ... | ... |

Použití `mvexpand` zobrazíte každá hodnota na samostatném řádku namísto seznam oddělený čárkami:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Řešení | 
|--------------|----------------------|
| POČÍTAČ1 | "zabezpečení" |
| POČÍTAČ1 | "updates" |
| POČÍTAČ1 | "changeTracking" |
| computer2 | "zabezpečení" |
| computer2 | "updates" |
| computer3 | "Antimalwarové" |
| computer3 | "changeTracking" |
| ... | ... |


Můžete pak použít `makelist` znovu k seskupení položek najednou a tentokrát naleznete v seznamu počítačů podle řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Řešení | list_Computer |
|--------------|----------------------|
| "zabezpečení" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "Antimalwarové" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Chybějící přihrádek zpracování
Užitečné použití `mvexpand` je potřeba vyplnit výchozí hodnoty pro chybějící přihrádky. Předpokládejme například, že se díváte provozuschopnost konkrétní počítač, ve kterých prezenčního signálu. Chcete také zobrazit prezenčního signálu, který je ve zdroji _kategorie_ sloupce. Za normálních okolností bychom použili jednoduchý příkaz shrnout takto:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý agent | 2017-06-06T17:00:00Z | 15 |
| Přímý agent | 2017-06-06T18:00:00Z | 60 |
| Přímý agent | 2017-06-06T20:00:00Z | 55 |
| Přímý agent | 2017-06-06T21:00:00Z | 57 |
| Přímý agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

V těchto výsledků ale sady přidružené k "2017-06-06T19:00:00Z" chybí, protože není k dispozici žádná data prezenčního signálu pro určitou hodinu. Použití `make-series` funkci přiřadí výchozí hodnotu prázdných kbelíků. Tím se vygeneruje řádek pro každou kategorii se dvěma sloupci další pole, jeden pro hodnoty a jeden pro odpovídající časovým intervalům:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategorie | count_ | TimeGenerated |
|---|---|---|
| Přímý agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Třetího prvku pole *count_* pole má hodnotu 0, podle očekávání a neexistuje odpovídající razítko "2017-06-06T19:00:00.0000000Z" v _TimeGenerated_ pole. Formát tohoto pole je obtížné číst ale. Použití `mvexpand` rozbalte pole a vytvořit stejný formát výstupu vygenerovanými `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý agent | 2017-06-06T17:00:00Z | 15 |
| Přímý agent | 2017-06-06T18:00:00Z | 60 |
| Přímý agent | 2017-06-06T19:00:00Z | 0 |
| Přímý agent | 2017-06-06T20:00:00Z | 55 |
| Přímý agent | 2017-06-06T21:00:00Z | 57 |
| Přímý agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zúžení výsledků pro sadu elementů: `let`, `makeset`, `toscalar`, `in`
Běžný scénář, kdy je výběr názvy některé konkrétní entity založené na sadě kritérií a potom filtrování jinou sadu dat do této sady entit. Například můžete najít počítače, na kterých je známo, že jim chybí aktualizace a identifikovat IP adresy, které uvádějí tyto počítače k:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Další postup

Zobrazit další lekce pro použití [Kusto dotazovací jazyk](/azure/kusto/query/) službou Azure Monitor můžete vytvářet protokoly dat:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)