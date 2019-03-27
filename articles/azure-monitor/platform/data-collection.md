---
title: Sledování dat shromážděných službou Azure Monitor | Dokumentace Microsoftu
description: Monitorování data shromážděná službou Azure Monitor je rozdělené na protokoly, které se používají pro pokročilé analýzy a metriky, které jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: e6d953841e5c22c21640f874ecad942f8db76ad1
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448895"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Sledování dat shromážděných službou Azure Monitor
[Azure Monitor](../overview.md) je služba, která vám pomůže sledovat vaše aplikace a prostředky, které spoléhají na to. Centrální pro tuto funkci je úložiště dat a další data z monitorovaných prostředků. Tento článek poskytuje úplný popis toho, jak tato data se ukládají a používat Azure Monitor.

Všechna data shromážděná službou Azure Monitor zapadá do jednoho ze dvou základních typů, [metriky](#metrics) a [protokoly](#logs). Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitém místě v čase. Jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Telemetrická data, jako je například událostmi a sledováním jsou uloženy jako protokoly kromě na data výkonu tak, aby se všechny zkombinovat pro analýzu.

![Přehled služby Azure Monitor](media/data-collection/overview.png)

## <a name="metrics"></a>Metriky
Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitou dobu. Jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Jsou užitečné pro výstrahy, protože můžou být často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou.

Například může shromažďování využití procesoru z virtuálního počítače každou minutu nebo počet uživatelů, přihlášení k aplikaci každých 10 minut. Upozornění může vyvolat, když jedna z těchto hodnot shromažďovaným nebo dokonce rozdíl mezi dvěma hodnotami překročí definované prahové hodnoty.

Konkrétní atributy metrik v Azure, patří:

* Pokud není určeno jinak v definici tuto metriku, shromážděných v minutových intervalech.
* Jednoznačně identifikují pomocí název metriky a obor názvů, který funguje jako kategorie.
* Ukládají 93 dnů. Metriky můžete zkopírovat protokoly pro dlouhodobé trendy.

Každá hodnota metriky má následující vlastnosti:
* Čas, kdy byl shromážděn hodnotu.
* Představuje typ měření hodnotu.
* Hodnota materiál náleží.
* Vlastní hodnota.
* Některé metriky může mít více rozměrů, jak je popsáno v další části. Vlastní metriky můžete mít až 10 dimenze.

### <a name="multi-dimensional-metrics"></a>Vícedimenzionálních metrik
Dimenze metriky jsou páry název hodnota, které přenášejí další data k popisu hodnota metriky. Například metrika _volného místa na disku_ může mít dimenzi volá _jednotka_ hodnotami _C:_, _D:_, který by umožnil zobrazení buď volného místa na disku ve všech jednotek nebo jednotlivě pro každý disk.

Následující příklad ukazuje dvě datové sady pro hypotetické metriku s názvem _propustnost sítě_. První datová sada nemá žádné dimenze. Druhý objekt dataset zobrazuje hodnoty dvourozměrné, _IP adresu_ a _směr_:

### <a name="network-throughput"></a>Propustnost sítě

| Timestamp     | Hodnota metriky |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kb/s |
| 8/9/2017 8:15 | 1,141.4 kb/s |
| 8/9/2017 8:16 | 1,110.2 kb/s |

Tato metrika-rozměrné lze pouze jako odpověď základní otázky "jaké byly Moje propustnost sítě v daném okamžiku?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě a dvě dimenze ("IP" a "Směr")

| Timestamp     | Dimenze "IP"   | Dimenze "Směr" | Hodnota metriky|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Odeslat"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Zobrazit" | 420.1 kB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 150.0 kb/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 115,2 kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Odeslat"    | 515.2 kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Zobrazit" | 371.1 kB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 155.0 kb/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 100.1 kB/s |

Tato metrika může odpovídat na otázky, jako je například "jaké byly propustnost sítě pro každou IP adresu?" a "a byla odeslána jak velký objem dat přijatých?" Vícedimenzionálních metrik provádět další analýzy a diagnostiku hodnotu ve srovnání s-jednodimenzionální metriky.

### <a name="value-of-metrics"></a>Hodnoty metrik
Jednotlivé metriky obvykle poskytují přehled o malou samostatně. Poskytuje jednu hodnotu bez kontextu než porovnání, které se jednoduché prahové hodnoty. Jsou užitečné v kombinaci s dalšími metrikami k identifikaci trendů a vzorů, nebo v kombinaci s protokoly, které poskytují kontext po konkrétní hodnoty.

Například určitý počet uživatelů ve vaší aplikaci v daném okamžiku můžete zjistit málo o stavu aplikace. Ale náhlému poklesu u uživatelů, indikován více hodnot stejné metriky, by mohly poukazovat problém. Nadměrné výjimek vyvolaných aplikací a indikován samostatné metriku, by mohly identifikovat problém aplikace, který způsobuje, že rozevírací. Události, které aplikace se vytvoří pro identifikaci selhání v jeho součásti můžete zjistit původní příčinu.

### <a name="sources-of-metric-data"></a>Zdroje dat metriky
Existují tři základní zdroje z metrik shromážděných službou Azure Monitor. Všechny tyto metriky jsou k dispozici v úložišti metrik, kde lze vyhodnotit jejich společně bez ohledu na jejich zdroj.

**Platforma metriky** vytvářejí prostředky Azure a získáte přehled o jejich stav a výkon. Vytvoří jednotlivých typů prostředků [odlišnou sadu metriky](metrics-supported.md) bez veškeré požadované konfigurace. 

**Metriky aplikací** jsou vytvořeny pomocí Application Insights pro monitorované aplikace a pomáhají detekovat problémy s výkonem a sledování trendů ve využití vaší aplikace. To zahrnuje tyto hodnoty jako _doba odezvy serveru_ a _výjimky prohlížeče_.

**Vlastní metriky** metriky, které definujete kromě standardních metrik, které jsou automaticky dostupné. Vlastní metriky se musí vytvořit na jediném prostředku ve stejné oblasti jako prostředek. Můžete vytvořit vlastní metriky pomocí následujících metod:
- [Definujte vlastní metriky v aplikaci](../../azure-monitor/app/api-custom-events-metrics.md) , který je monitorován pomocí Application Insights. Tyto jsou kromě standardních sadu metriky aplikací.
- Publikovat vlastní metriky z vašich virtuálních počítačů Windows pomocí [Windows diagnostické rozšíření (WAD)](../../azure-monitor/platform/diagnostics-extension-overview.md).
- Publikovat vlastní metriky z vašich virtuálních počítačů s Linuxem pomocí [InfluxData Telegraf agenta](https://www.influxdata.com/time-series-platform/telegraf/).
- Zápis vlastních metrik ze služby Azure pomocí rozhraní API pro vlastní metriky.

![Přehled metrik](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>Co můžete dělat s metrikami?
Úlohy, které můžete provádět pomocí metrik, patří:

- Použití [metriky analytics](metrics-charts.md) k analýze shromážděných metrik a vykreslit v grafu. Sledování výkonu prostředků (jako je například virtuální počítač, webu nebo logiky aplikace) tak, že připnete grafy [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurace [metriky pravidlo upozornění](alerts-metric.md) , který odešle oznámení, nebo má [automatizované akce](action-groups.md) Pokud metrika překročí mezní hodnotu.
- Použití [automatického škálování](autoscale-overview.md) zvýšení nebo snížení prostředků na základě metriky překročení prahové hodnoty.
- Metriky tras s protokoly k analýze dat metriky spolu s daty log a k uložení hodnoty metrik déle než 93 dní. 
- Stream metrik [centra událostí](stream-monitoring-data-event-hubs.md) směrovat je do [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) nebo k externím systémům.
- [Archiv](../../azure-monitor/learn/tutorial-archive-data.md) historii výkon nebo stav prostředku pro dodržování předpisů, auditování, nebo v režimu offline pro účely vykazování.
- Přístup k hodnoty metrik z příkazového řádku nebo vlastní aplikace s využitím [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/) nebo [rozhraní REST API](rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Zobrazení metrik
Metriky ve službě Azure Monitor jsou uloženy v časové řadě databáze optimalizovaný pro rychlé načítání a ukládá hodnoty metrik 93 dní. Zkopírujte metriky do protokolů pro dlouhodobé analýzy a sledování trendů.

Data metriky se používá v mnoha různými způsoby, jak je popsáno výše. Použití [metriky analytics](metrics-charts.md) přímo analyzovat data v úložišti metriky a graf hodnoty několika metrik v čase. Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky pomocí [Azure, rozhraní REST API pro monitorování](rest-api-walkthrough.md).

![Metriky Analytics](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>Logs

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Protokoly mohou obsahovat číselných hodnot, jako třeba metriky, ale obvykle obsahují textová data se podrobný popis. Tyto další se liší od metriky v tom, že se liší v jejich strukturu a často nejsou shromážděny v pravidelných intervalech.

Běžný typ položky protokolu je událost, která se shromažďují nedojde. Jsou už vytvořené aplikace nebo služby a obvykle zahrnují dostatek informací, které poskytují úplný kontext na své vlastní. Například událost může znamenat, že určitý prostředek vytvořil nebo změnil, nový hostitel spuštěna v reakci na zvýšení provozu, nebo v aplikaci došlo k chybě.

Protokoly jsou zvláště užitečné pro kombinování dat z různých zdrojů, pro komplexní analýzu a trendy v čase. Vzhledem k tomu, že formát dat se může lišit, aplikace můžete vytvořit vlastní protokoly pomocí struktura, která potřebují. Metriky jsou replikovány i v protokolech kombinovat s dalšími daty monitorování pro vytvoření trendu a ostatní data analýzy.



### <a name="sources-of-log-data"></a>Zdroje dat protokolu
Azure Monitor může shromažďovat data protokolu z různých zdrojů v rámci Azure a z místních prostředků. Zdroje dat protokolu, patří:

- [Protokoly aktivit](collect-activity-logs.md) z prostředků Azure, které obsahují informace o své konfiguraci a stavu a [diagnostické protokoly](diagnostic-logs-stream-log-store.md) , které poskytují přehled o jejich operace.
- Agenty na [Windows](agent-windows.md) a [Linux](../learn/quick-collect-linux-computer.md) virtuálních počítačů, které odesílají telemetrii z hostovaného operačního systému a aplikací do Azure monitoru podle [zdroje dat](data-sources.md) , který můžete nakonfigurovat.
- Aplikace data shromážděná pomocí [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Data a poskytují přehled o konkrétní aplikaci nebo službu [řešení monitorování](../insights/solutions.md) nebo funkce, jako jsou přehledy o kontejnerech, Insights virtuálního počítače nebo Insights skupiny prostředků.
- Shromážděná data zabezpečení [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- [Metriky](#metrics) z prostředků Azure. To umožňuje ukládat metriky po dobu delší než 93 dnů a analyzujte je pomocí jiných dat protokolu.
- Telemetrická data zapsána do [služby Azure Storage](azure-storage-iis-table.md).
- Vlastní data z jakéhokoli klienta REST API pomocí [rozhraní API kolekce dat HTTP](data-collector-api.md) klienta nebo z [aplikace logiky Azure](https://docs.microsoft.com/azure/logic-apps/) pracovního postupu.

![Přehled protokolů](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>Co můžete dělat s protokoly?
Úlohy, které můžete provést pomocí protokolů zahrnují následující:

- Použití [Log Analytics](../log-query/get-started-portal.md) na webu Azure Portal psát dotazy, analýza dat protokolu. Připnout výsledky se vykresluje jako tabulky nebo grafy [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurace [pravidel upozornění protokolů](alerts-log.md) , který odešle oznámení, nebo má [automatizované akce](action-groups.md) při výsledky dotazu splňují konkrétní výsledek.
- Vytvořit pracovní postup podle dat protokolu pomocí [Logic Apps](~/articles/logic-apps/index.yml).
- Export výsledků dotazu na [Power BI](powerbi.md) a použít různé vizualizace a sdílet s uživateli mimo Azure.
- Přístup k hodnoty metrik z příkazového řádku nebo vlastní aplikace s využitím [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) nebo [rozhraní REST API](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Zobrazení dat protokolu
Všechna data protokolu ve službě Azure Monitor jsou načítány s použitím [dotaz protokolu](../log-query/log-query-overview.md) napsané pomocí [Kusto dotazovací jazyk](../log-query/get-started-queries.md), která umožňuje rychle načítat, konsolidovat a analýzy shromážděných dat. Použití [Log Analytics](../log-query/portals.md) k psaní a testování dotazů na webu Azure Portal. Můžete interaktivně pracovat s výsledky nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst protokoly pomocí [Azure, rozhraní REST API pro monitorování](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

> [!IMPORTANT]
> Data ze služby Application Insights se ukládají v samostatném oddílu než jiná data protokolu ve službě Azure Monitor. Tento atribut podporuje stejné funkce jako jiná data protokolu, ale je nutné použít [konzoly Application Insights](../app/analytics.md) nebo [API pro Application Insights](https://dev.applicationinsights.io/) pro přístup k těmto datům. Můžete použít [napříč prostředky dotazu](../log-query/cross-workspace-query.md) k analýze dat aplikací společně s další data protokolu.

![Logs](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Převod dat monitorování

### <a name="metrics-to-logs"></a>Metriky, které protokoly
Metriky můžete zkopírovat protokoly, provádět komplexní analýzy s jinými datovými typy pomocí Azure monitoru Bohatý dotazovací jazyk. Můžete také zachovat data protokolu pro delší dobu než metriky, které umožňuje provádět sledování trendů v čase. Použití metrik pro podporu téměř v reálném čase analýzy a upozornění při používání protokolů pro sledování trendů a analýzy s ostatními daty.

Pokyny pro shromažďování metrik z prostředků v Azure můžete získat [protokoly služby Azure shromažďovat metriky pro použití ve službě Azure Monitor a](collect-azure-metrics-logs.md). Získejte pokyny pro shromažďování metrik prostředků z prostředků Azure PaaS za [konfigurace shromažďování metrik prostředků Azure PaaS pomocí Azure monitoru](collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Protokoly a metriky
Jak je popsáno výše, jsou responzivní více než tento počet protokolů, metrik, takže můžete vytvářet upozornění s nižší latencí a s nižšími náklady. Významné množství číselná data se ukládá jako protokoly, které by bylo vhodné pro metriky, ale není uložený jako metriky ve službě Azure Monitor. Běžným příkladem jsou data o výkonu shromážděná z agentů a řešení pro správu. Některé z těchto hodnot je možné zkopírovat do metrik, kde jsou k dispozici pro výstrahy a analýzy s Průzkumníkem metrik.

Vysvětlení této funkce je k dispozici na [vytvořit upozornění metriky pro protokoly ve službě Azure Monitor](alerts-metric-logs.md). Seznam hodnot podpora je k dispozici na [podporované metriky ve službě Azure Monitor](metrics-supported.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Datový Stream k externím systémům
Kromě použití nástroje pro analýzu dat monitorování v Azure, můžete mít povinnost předat externího nástroje, jako jsou informace o zabezpečení a událostí produktů pro správu (SIEM). Toto přesměrování se obvykle provádí přímo z monitorovaných prostředků prostřednictvím [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

Získejte pokyny různých typů údajů o monitorování na [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Další postup

- Další informace o [data k dispozici monitorování](data-sources.md) pro různé prostředky v Azure.
