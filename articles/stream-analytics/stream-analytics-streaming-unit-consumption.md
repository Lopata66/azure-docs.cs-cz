---
title: Jednotky streamování v Azure Stream Analytics
description: Tento článek popisuje nastavení jednotek streamování a další faktory, které mají vliv na výkon v Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: a4811da398fde869d8eb5457db11a592006c59a9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934275"
---
# <a name="understand-and-adjust-streaming-units"></a>Pochopení a úprava jednotek streamování

Jednotky streamování (SUs) představují výpočetní prostředky, které jsou přiděleny k provedení Stream Analytics úlohy. Čím vyšší je počet SU, tím více prostředků CPU a paměti se úloze přidělí. Tato kapacita vám umožní soustředit se na logiku dotazů a abstrakce nutnosti spravovat hardware a včas spouštět úlohy Stream Analytics.

Aby se dosáhlo nízké latence zpracování streamů, provádějí úlohy Stream Analytics veškeré zpracování v paměti. Když dojde k nedostatku paměti, úloha streamování se nezdařila. V důsledku toho je pro produkční úlohy důležité monitorovat využití prostředků úlohy streamování a zajistit, aby byl k dispozici dostatek prostředků, aby byly úlohy spuštěné 24/7.

Metrika využití SU%, která je v rozsahu od 0% do 100%, popisuje spotřebu paměti vašich úloh. Pro úlohu streamování s minimálními nároky je tato metrika obvykle mezi 10% a 20%. Pokud je využití SU% nízké a vstupní události obdrží nevyřízené, vaše zatížení pravděpodobně vyžaduje více výpočetních prostředků, což vyžaduje, abyste zvýšili počet služeb SUs. Je vhodné zachovat metriku SU nižší než 80%, aby se zohlednila občasné špičky. Microsoft doporučuje nastavit výstrahu na 80% SU využití metriky, aby se zabránilo vyčerpání prostředků. Další informace najdete v tématu [kurz: nastavení výstrah pro Azure Stream Analytics úlohy](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurace Stream Analyticsch jednotek streamování (SUs)
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V seznamu prostředků Najděte Stream Analytics úlohu, kterou chcete škálovat, a pak ji otevřete. 

3. Na stránce úloha v části **Konfigurovat** záhlaví vyberte **škálovat**. 

    ![Konfigurace úlohy Azure Portal Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Pomocí posuvníku nastavte službu SUs pro úlohu. Všimněte si, že jste omezeni na konkrétní nastavení SU. 

## <a name="monitor-job-performance"></a>Monitorování výkonu úlohy
Pomocí Azure Portal můžete sledovat propustnost úlohy:

![Azure Stream Analytics monitorování úloh][img.stream.analytics.monitor.job]

Vypočítá očekávanou propustnost úlohy. Pokud je propustnost menší, než je očekáváno, vylaďte vstupní oddíl, vylaďte dotaz a přidejte do úlohy službu SUs.

## <a name="how-many-sus-are-required-for-a-job"></a>Kolik požadavků na službu SUs vyžaduje úloha?

Volba počtu požadovaných součástí služby SUs pro konkrétní úlohu závisí na konfiguraci oddílů pro vstupy a dotaz, který je definován v rámci úlohy. Stránka **škálování** umožňuje nastavit správný počet služby SUs. Osvědčeným postupem je přidělit více služby SUs, než je potřeba. Modul pro zpracování Stream Analytics optimalizuje latenci a propustnost za cenu přidělení další paměti.

Obecně platí, že osvědčeným postupem je začít s 6 službami SUs for dotazů, které nepoužívají **oddíly podle**. Pak se pomocí zkušební a chybové metody, ve které se po předávání reprezentativních objemů dat, a prověřte metriky využití% SU, určí sladké místo. Maximální počet jednotek streamování, které může úloha Stream Analytics použít, závisí na počtu kroků v dotazu definovaném pro úlohu a na počtu oddílů v každém kroku. Další informace o limitech najdete [tady](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Další informace o volbě správného počtu SUs najdete na této stránce: [škálování úloh Azure Stream Analytics pro zvýšení propustnosti](stream-analytics-scale-jobs.md) .

> [!Note]
> Volba toho, kolik SUs se vyžaduje pro konkrétní úlohu, závisí na konfiguraci oddílů pro vstupy a na dotazu definovaném pro danou úlohu. Pro úlohu můžete vybrat až tuto kvótu v rámci služby SUs. Ve výchozím nastavení má každé předplatné Azure kvótu až 500 služby SUs pro všechny úlohy analýzy v konkrétní oblasti. Pokud chcete zvýšit platnost svých předplatných za tuto kvótu, obraťte se na [Podpora Microsoftu](https://support.microsoft.com). Platné hodnoty pro službu SUs na úlohu jsou 1, 3, 6 a v přírůstcích po 6.

## <a name="factors-that-increase-su-utilization"></a>Faktory, které zvyšují využití SU% 

Dočasné (časově orientované) prvky dotazu jsou základní sadou stavových operátorů poskytovaných Stream Analytics. Stream Analytics spravuje stav těchto operací interně jménem uživatele, a to díky správě spotřeby paměti, vytváření kontrolních bodů pro odolnost a obnovení stavu během upgradů služby. I když Stream Analytics plně spravuje stavy, existuje několik doporučení doporučených postupů, která by si měli uživatelé zvážit.

Všimněte si, že úloha se složitou logikou dotazů by mohla mít vysoké procento využití SU i v případě, že nepřetržitě nepřijímá vstupní události. K tomu může dojít po náhlém špičkě ve vstupních a výstupních událostech. Pokud je dotaz složitý, může úloha dál udržovat stav v paměti.

Využití SU% se může po krátké době před návratem na očekávané úrovně náhle přetáhnout na 0. K tomu dochází kvůli přechodným chybám nebo upgradům iniciované systémem. Zvýšení počtu jednotek streamování pro úlohu se nemusí snížit, pokud váš dotaz není [plně paralelní](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

## <a name="stateful-query-logicin-temporal-elements"></a>Logika stavových dotazů v dočasnách prvcích
Jednou z jedinečných možností Azure Stream Analytics úlohy je provádět stavové zpracování, jako jsou například agregovaná okna, dočasná spojení a dočasné analytické funkce. Každý z těchto operátorů uchovává informace o stavu. Maximální velikost okna pro tyto prvky dotazu je sedm dní. 

Koncept dočasného okna se zobrazí v několika Stream Analyticsch prvcích dotazu:
1. Okna agregace: seskupení podle bubnu, skákající a posuvných oken

2. Dočasná spojení: spojení s funkcí DATEDIFF

3. Dočasné analytické funkce: jako první, poslední a PRODLEVa s dobou trvání omezení

Následující faktory ovlivňují využitou paměť (součást metrik jednotek streamování) tím, že Stream Analytics úlohy:

## <a name="windowed-aggregates"></a>Agregované hodnoty okna
Spotřebované množství paměti (velikost stavu) pro agregaci v okně není vždy přímo úměrné velikosti okna. Místo toho je využitá paměť úměrná mohutnosti dat nebo počtu skupin v každém časovém intervalu.


Například v následujícím dotazu je číslo přidružené k `clusterid` mohutnosti dotazu. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Aby bylo možné zmírnit problémy způsobené vysokou mohutnosti v předchozím dotazu, můžete odesílat události do centra událostí, které je rozdělené `clusterid`, a škálovat dotaz tím, že systému umožníte, aby každý vstupní oddíl zpracoval samostatně pomocí **oddílu podle** uvedeného postupu. v následujícím příkladu:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho se zmenší počet `clusterid` hodnot přicházejících do jednotlivých uzlů, čímž se sníží mohutnost operátoru Group by. 

Oddíly centra událostí by měly být rozdělené podle klíče seskupení, aby nedocházelo k nutnosti snižovat krok. Další informace najdete v tématu [přehled Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Dočasná spojení
Spotřebované množství paměti (velikost stavu) dočasného připojení je úměrné počtu událostí v dočasné Wiggle místnosti spojení, což je vstupní rychlost události vynásobená velikostí místnosti Wiggle. Jinými slovy, paměť spotřebovaná spojeními je potažená na časový rozsah funkce DateDiff vynásobený průměrnou sazbou události.

Počet nespárovaných událostí v připojení ovlivňuje využití paměti pro dotaz. Následující dotaz hledá imprese reklamy, které generují kliknutí:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

V tomto příkladu je možné, že se zobrazují spousty reklam a na ni klikne jenom pár lidí a je potřeba, aby se všechny události zobrazovaly v časovém období. Využitá paměť je přímo úměrná velikosti tohoto okna a frekvenci událostí. 

Pokud to chcete napravit, pošlete události do centra událostí, které je rozdělené do oddílů pomocí klíčů Join (ID v tomto případě), a nahorizontální navýšení kapacity dotazu tím, že systém zpracuje každý vstupní oddíl samostatně pomocí **oddílu podle** uvedeného příkladu:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho se zmenší počet událostí přicházejících do jednotlivých uzlů a tím se zmenší velikost stavu, který je udržován v okně spojení. 

## <a name="temporal-analytic-functions"></a>Dočasné analytické funkce
Spotřebované množství paměti (velikost stavu) dočasné analytické funkce je úměrné četnosti událostí vynásobené dobou trvání. Paměť spotřebovaná analytickými funkcemi není úměrná velikosti okna, ale počítá se podle počtu oddílů v každém časovém intervalu.

Náprava je podobná dočasnému připojení. Můžete škálovat dotaz pomocí **oddílu podle**. 

## <a name="out-of-order-buffer"></a>Vyrovnávací paměť mimo pořadí 
Uživatel může nakonfigurovat velikost vyrovnávací paměti mimo pořadí v podokně Konfigurace řazení událostí. Vyrovnávací paměť se používá k ukládání vstupů po dobu trvání okna a jejich uspořádání. Velikost vyrovnávací paměti je úměrná hodnotě vstupní frekvence události vynásobené velikostí okna mimo pořadí. Výchozí velikost okna je 0. 

Chcete-li napravit přetečení vyrovnávací paměti mimo pořadí, proveďte horizontální navýšení kapacity dotazu pomocí **dělení**. Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho se zmenší počet událostí přicházejících na jednotlivé uzly a tím se sníží počet událostí v každé vyrovnávací paměti pro změnu pořadí. 

## <a name="input-partition-count"></a>Počet vstupních oddílů 
Každý vstupní oddíl vstupu úlohy má vyrovnávací paměť. Větší počet vstupních oddílů, čím více prostředků úloha spotřebovává. U každé jednotky streamování může Azure Stream Analytics zpracovat přibližně 1 MB/s vstupu. Proto můžete optimalizovat tak, že odpovídá počtu jednotek streamování Stream Analytics s počtem oddílů v centru událostí. 

Obvykle je úloha nakonfigurovaná s jednou jednotkou streamování dostatečná pro centrum událostí se dvěma oddíly (což je minimum pro centrum událostí). Pokud má centrum událostí více oddílů, vaše úloha Stream Analytics spotřebovává více prostředků, ale nemusí nutně využívat další propustnost, kterou poskytuje centrum událostí. 

Pro úlohu s 6 jednotkami streamování možná budete potřebovat 4 nebo 8 oddílů z centra událostí. Nepoužívejte ale příliš mnoho zbytečných oddílů, protože to způsobí nadměrné využití prostředků. Například centrum událostí s 16 oddíly nebo větší v Stream Analytics úlohy, která má 1 jednotku streamování. 

## <a name="reference-data"></a>Referenční data 
Referenční data v ASA jsou načtena do paměti pro rychlé vyhledávání. V rámci aktuální implementace každá operace spojení s referenčními daty uchovává kopii referenčních dat v paměti, a to i v případě, že se spojíte se stejnými referenčními daty víckrát. Pro dotazy s **oddíly podle**, každý oddíl obsahuje kopii referenčních dat, takže oddíly jsou plně oddělené. Díky multiplikační efektu může využití paměti rychle získat velkou vysokou dobu, pokud se k referenčním datům připojíte víckrát s více oddíly.  

### <a name="use-of-udf-functions"></a>Použití funkcí UDF
Když přidáte funkci UDF, Azure Stream Analytics načte modul runtime jazyka JavaScript do paměti. To bude mít vliv na% SU.

## <a name="next-steps"></a>Další kroky
* [Vytváření dotazů paralelizovat v Azure Stream Analytics](stream-analytics-parallelization.md)
* [Škálování úloh Azure Stream Analytics pro zvýšení propustnosti](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
