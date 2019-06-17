---
title: Agregace ve službě Azure Monitor protokolu dotazy | Dokumentace Microsoftu
description: Popisuje funkce agregace v dotazů na protokoly Azure monitoru, které nabízejí užitečné způsoby, jak analyzovat data.
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
ms.openlocfilehash: fd8e886a78d0689ca60d8ea7c4d16639c81d5733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602725"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregace v dotazů na protokoly Azure monitoru

> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje funkce agregace v dotazů na protokoly Azure monitoru, které nabízejí užitečné způsoby, jak analyzovat data. Všechny tyto funkce pracují s `summarize` operátor, který vytvoří tabulku s agregované výsledky ve vstupní tabulce.

## <a name="counts"></a>Počty

### <a name="count"></a>count
Počet řádků v sadě výsledků po jsou použity nějaké filtry. Následující příklad vrátí celkový počet řádků v _výkonu_ tabulku z posledních 30 minut. Výsledek se vrátí v sloupec s názvem *count_* není-li přiřadit konkrétní název:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Vizualizace promítnu se dá zobrazit tak trend v čase:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Výstup z tohoto příkladu ukazuje počet záznamů trendu výkonu v intervalech 5 minut:

![Trend počtu](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount dcountif
Použití `dcount` a `dcountif` počet jedinečných hodnot v určitém sloupci. Následující dotaz vyhodnotí, kolik různých počítačů odeslaly prezenční signály za poslední hodinu:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Počet pouze počítače systému Linux, které odeslaly prezenční signály, použijte `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Vyhodnocení podskupiny
Chcete-li provést počet nebo jiných agregací na podskupiny ve vašich datech, použijte `by` – klíčové slovo. Třeba ke zjištění počtu jedinečných počítačů se systémem Linux, které odeslaly prezenční signály v každé zemi/oblast:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Spojené státy    | 19                  |
|Kanada           | 3                   |
|Irsko          | 0                   |
|Spojené království   | 0                   |
|Nizozemsko      | 2                   |


K analýze i menší podskupiny vašich dat, přidat další sloupce na `by` oddílu. Můžete například chtít počet jedinečných počítačů z každé země/oblast a OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentil a rozptyl
Při vyhodnocování číselné hodnoty, je běžnou praxí je průměrná pomocí `summarize avg(expression)`. Průměrné hodnoty jsou ovlivněny extrémní hodnoty, které charakterizují pouze několik případů. K vyřešení tohoto problému, můžete použít míň citlivé funkce, jako `median` nebo `variance`.

### <a name="percentile"></a>Percentil
Pokud chcete zjistit střední hodnotu, použijte `percentile` funkci s hodnotou k určení na percentilu:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Můžete také zadat různé percentily zobrazíte na agregovaný výsledek pro každý:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

To může zobrazit, že některé počítače procesory mají podobné střední hodnoty, ale jsou stabilní kolem medián, ostatní počítače ohlásil mnohem nižší a vyšší hodnoty procesoru, to znamená, že se zjistil provozní špičky.

### <a name="variance"></a>Odchylka
K vyhodnocení přímo odchylku hodnot, použijte směrodatná odchylka a rozptyl metody:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrým způsobem, jak analyzovat stabilitu využití procesoru je kombinování stdev s střední výpočtu:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zobrazit další lekce pro použití [Kusto dotazovací jazyk](/azure/kusto/query/) službou Azure Monitor můžete vytvářet protokoly dat:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
