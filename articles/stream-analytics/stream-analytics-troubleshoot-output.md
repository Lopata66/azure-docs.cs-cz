---
title: Řešení potíží s výstupy Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží s vašimi výstupními připojeními v Azure Stream Analytics úlohách.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: fc35e6a723afab3f230aa91e4b6895aead35e141
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037065"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Řešení potíží s výstupy Azure Stream Analytics

Tento článek popisuje běžné problémy s Azure Stream Analyticsmi výstupními připojeními a postupy při řešení problémů s výstupem. Mnohé kroky pro řešení potíží vyžadují, aby se pro vaši úlohu Stream Analytics povolily prostředky a další diagnostické protokoly. Pokud nemáte Povolené protokoly prostředků, přečtěte si téma [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>Úloha nevytváří výstup.

1. Ověřte připojení ke výstupům pomocí tlačítka **Testovat připojení** pro každý výstup.
1. Podívejte se na [sledování metrik](stream-analytics-monitoring.md) na kartě **monitorování** . Vzhledem k tomu, že jsou hodnoty agregované, jsou metriky zpožděné o několik minut.

   * Pokud je hodnota **vstupních událostí** větší než nula, může úloha číst vstupní data. Pokud hodnota **vstupních událostí** není větší než nula, dojde k problému se vstupem úlohy. Další informace najdete v tématu [řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md) .
   * Pokud je hodnota **chyby převodu dat** větší než nula a stoupání, přečtěte si část [Azure Stream Analytics chyby dat](data-errors.md) , kde najdete podrobné informace o chybách při převodu dat.
   * Pokud je hodnota **chyby běhového prostředí** větší než nula, úloha přijme data, ale při zpracování dotazu generuje chyby. Chcete-li najít chyby, klikněte na [protokoly auditu](../azure-resource-manager/management/view-activity-logs.md)a potom vyfiltrujte stav **selhání** .
   * Pokud je hodnota **vstupních událostí** větší než nula a **výstupní události** se rovná nule, jeden z následujících příkazů je pravdivý:
      * Zpracování dotazu vedlo k nulovým výstupním událostem.
      * Události nebo pole mohou být poškozeny, což po zpracování dotazu vytvoří nulový výstup.
      * Úloha nemohla odeslat data do výstupní jímky pro účely připojení nebo ověřování.

   Zprávy protokolu operací vysvětlují další podrobnosti, včetně toho, co se děje, s výjimkou případů, kdy logika dotazu filtruje všechny události. Pokud zpracování více událostí generuje chyby, jsou chyby shrnuty každých 10 minut.

## <a name="the-first-output-is-delayed"></a>První výstup je zpožděný.

Při spuštění úlohy Stream Analytics se čtou vstupní události. V některých případech ale může dojít ke zpoždění výstupu.

Dlouhé hodnoty času v dočasných prvcích dotazů mohou přispět k zpoždění výstupu. Aby se vytvořil správný výstup v rámci velkých oken času, úloha streamování načte data z poslední možné doby k vyplnění časového okna. Data mohou být až po dobu až sedmi dnů. Žádný výstup negeneruje, dokud nebudou přečteny nezpracované vstupní události. Tento problém se může nacházet při upgradu systému na úlohy streamování. V případě, že dojde k upgradu, úloha se restartuje. K těmto inovacím obvykle dochází jednou za několik měsíců.

Při návrhu dotazu Stream Analytics použijte volitelné rozhodnutí. Pokud pro dočasné prvky v syntaxi dotazu úlohy použijete velký časový interval, může při spuštění nebo restartování úlohy způsobit zpoždění v prvním výstupu. Více než několik hodin, až sedm dní, je považováno za velký časový interval.

Jedním z rizik pro tento druh prvního zpoždění výstupu je použití technik paralelního dotazování, jako je vytváření oddílů dat. Nebo můžete přidat další jednotky streamování pro zlepšení propustnosti, dokud se úloha nedosáhne.  Další informace najdete v tématu [týkajícím se vytváření úloh Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

Tyto faktory ovlivňují časovou osu prvního výstupu:

* Použití agregovaných oken, jako je například klauzule GROUP BY pro bubny, skákající a posuvná okna:

  * V případě agregací nebo skákající oken se výsledky generují na konci časového rámce okna.
  * Pro posuvné okno se výsledky generují, když událost vstoupí do posuvných oken nebo je ukončí.
  * Pokud plánujete použít velkou velikost okna, například více než jednu hodinu, je nejlepší vybrat skákající nebo posuvné okno. Tyto typy oken umožňují zobrazit výstup častěji.

* Používání dočasná spojení, jako je například spojení s DATEDIFF:
  * Shoduje se s vygenerováním hned po doručení obou stran odpovídajících událostí.
  * Data, u kterých chybí shoda, jako je levé vnější spojení, se generují na konci okna DATEDIFF pro každou událost na levé straně.

* Použití dočasných analytických funkcí, jako je například FIRST, LAST a LAG s TRVÁNÍm LIMITu:
  * Pro analytické funkce je výstup vygenerován pro každou událost. Nedochází k žádnému zpoždění.

## <a name="the-output-falls-behind"></a>Výstup spadá pod

V průběhu běžné operace úlohy může výstup trvat déle a delší dobu latence. Pokud výstup spadá do toho, jak to bude, můžete určit hlavní příčiny tím, že prozkoumáte následující faktory:

* Bez ohledu na to, jestli je jímka pro příjem dat omezená
* Bez ohledu na to, jestli je nadřazený zdroj omezený
* Určuje, jestli je logika zpracování v dotazu náročná na výpočetní výkon.

Chcete-li zobrazit podrobnosti výstupu, vyberte úlohu streamování v Azure Portal a pak vyberte **diagram úlohy**. Pro každý vstup existuje metrika události nevyřízených položek na oddíl. Pokud se metrika stále zvyšuje, je indikátorem, že systémové prostředky jsou omezené. Zvýšení je potenciálně v důsledku omezení výstupní jímky nebo vysokého využití procesoru. Další informace najdete v tématu [ladění řízené daty pomocí diagramu úloh](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Upozornění na porušení klíče s výstupem Azure SQL Database

Když nakonfigurujete službu Azure SQL Database jako výstup do Stream Analytics úlohy, hromadně vloží záznamy do cílové tabulky. Obecně Azure Stream Analytics garantuje doručení do výstupní jímky [aspoň jednou](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) . V případě, že má tabulka SQL definováno jedinečné omezení, můžete přesto [dosáhnout přesně jednoho doručení]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do výstupu SQL.

Když nastavíte omezení jedinečnosti klíčů pro tabulku SQL, Azure Stream Analytics odstraní duplicitní záznamy. Rozdělí data do dávek a rekurzivně je vloží, dokud nebude nalezen jediný duplicitní záznam. Proces rozdělení a vložení ignoruje duplicitní hodnoty v jednom okamžiku. Pro úlohu streamování, která má mnoho duplicitních řádků, je proces neefektivní a časově náročný. Pokud se v protokolu aktivit během předchozí hodiny zobrazí více výstražných zpráv o porušení klíčů, je pravděpodobný, že váš výstup SQL zpomaluje celou úlohu.

Chcete-li tento problém vyřešit, [nakonfigurujte index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje porušení klíče, povolením možnosti IGNORE_DUP_KEY. Tato možnost umožňuje SQL ignorovat duplicitní hodnoty během hromadných vložení. Azure SQL Database jednoduše vytvoří zprávu upozornění namísto chyby. V důsledku toho Azure Stream Analytics už nevytvářejí chyby narušení primárního klíče.

Při konfiguraci IGNORE_DUP_KEY pro několik typů indexů Pamatujte na následující poznámky:

* Nemůžete nastavit IGNORE_DUP_KEY pro primární klíč nebo jedinečné omezení, které používá příkaz ALTER INDEX. Index je potřeba vyřadit a znovu vytvořit.  
* IGNORE_DUP_KEY můžete nastavit pomocí příkazu ALTER INDEX pro jedinečný index. Tato instance je odlišná od primárního KLÍČového a JEDINEČNÉho omezení a je vytvořena pomocí definice indexu nebo indexu.  
* Možnost IGNORE_DUP_KEY neplatí pro indexy úložiště sloupců, protože pro ně nemůžete vymáhat jedinečnost.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Názvy sloupců jsou malými písmeny v Azure Stream Analytics (1,0)

Při použití původní úrovně kompatibility (1,0) Azure Stream Analytics změny názvů sloupců na malá písmena. Toto chování bylo opraveno v novějších úrovních kompatibility. Pokud chcete zachovat případ, přejděte na úroveň kompatibility 1,1 nebo novější. Další informace najdete v tématu [úroveň kompatibility pro úlohy Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Získání pomoci

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka Azure Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace o REST API správy Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
