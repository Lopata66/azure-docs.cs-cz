---
title: Návody... v Azure Application Insights | Microsoft Docs
description: Nejčastější dotazy v Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 63a958e1d3fb784651043181bff0428666fad6db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014589"
---
# <a name="how-do-i--in-application-insights"></a>Jak mám udělat ... pomocí Application Insights?
## <a name="get-an-email-when-"></a>Získat e-mail, když...
### <a name="email-if-my-site-goes-down"></a>Poslat e-mail, když se můj web neukončí
Nastavte [webový test dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-mail, pokud je můj web přetížený
Nastavte [Upozornění](../../azure-monitor/platform/alerts-log.md) na **dobu odezvy serveru**. Musí fungovat prahová hodnota mezi 1 a 2 sekundami.

![Snímek obrazovky, který ukazuje, jak nastavit výstrahu pro dobu odezvy serveru.](./media/how-do-i/030-server.png)

Vaše aplikace může také zobrazit znaménka kmene vrácením kódů selhání. Nastavte upozornění na **neúspěšné žádosti**.

Pokud chcete nastavit výstrahu pro **serverové výjimky**, možná budete muset provést [Další instalaci](../../azure-monitor/app/asp-net-exceptions.md) , aby se zobrazila data.

### <a name="email-on-exceptions"></a>E-mail na výjimkách
1. [Nastavení sledování výjimek](../../azure-monitor/app/asp-net-exceptions.md)
2. [Nastavení upozornění](../../azure-monitor/platform/alerts-log.md) na metriku počtu výjimek

### <a name="email-on-an-event-in-my-app"></a>E-mail na události v aplikaci
Řekněme, že byste chtěli při výskytu určité události získat e-mail. Application Insights neposkytuje toto zařízení přímo, ale může [Odeslat výstrahu, pokud metrika přetrvá prahovou hodnotu](../../azure-monitor/platform/alerts-log.md).

Výstrahy je možné nastavit na [vlastní metriky](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), ale ne na vlastní události. Napsání kódu pro zvýšení metriky, když dojde k události:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

nebo:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Vzhledem k tomu, že výstrahy mají dva stavy, je nutné při zvážení výstrahy na ukončení odeslat nízkou hodnotu:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

V [Průzkumníkovi metriky](../../azure-monitor/platform/metrics-charts.md) vytvořte graf, ve kterém se zobrazí upozornění:

![Snímek obrazovky, který ukazuje, jak vytvořit graf v Průzkumníkovi metrik, aby se zobrazila vaše signalizace.](./media/how-do-i/010-alarm.png)

Teď můžete nastavit výstrahu, která se aktivuje, když metrika překročí hodnotu Mid po krátkou dobu:

![Snímek obrazovky, který ukazuje, jak nastavit výstrahu, která se aktivuje, když metrika překročí hodnotu Mid po krátkou dobu.](./media/how-do-i/020-threshold.png)

Nastavte období průměrování na minimum.

Když metrika překročí nad prahovou hodnotu, dostanete e-maily.

Některé body ke zvážení:

* Výstraha má dva stavy ("výstrahy" a "v pořádku"). Stav je vyhodnocen pouze v případě, že je přijata metrika.
* E-mail se pošle jenom v případě, že se změní stav. To je důvod, proč je nutné odesílat metriky s vysokou a nízkou hodnotou.
* Pro vyhodnocení výstrahy je průměrem přijatých hodnot za předchozí období. K tomu dochází pokaždé, když je přijata metrika, takže e-maily je možné odeslat častěji, než je období, které jste nastavili.
* Vzhledem k tomu, že e-maily jsou odesílány na "Alert" a "v pořádku", možná budete chtít vzít v úvahu, že se událost jednoho snímku považuje za podmínku dvou stavů. Například namísto události "dokončená úloha" se zobrazí podmínka "probíhá úloha", kde získáte e-maily na začátku a na konci úlohy.

### <a name="set-up-alerts-automatically"></a>Nastavit výstrahy automaticky
[Použití PowerShellu k vytváření nových výstrah](../../azure-monitor/platform/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Použití PowerShellu ke správě Application Insights
* [Vytvoření nových prostředků](./create-new-resource.md#creating-a-resource-automatically)
* [Vytvořit nové výstrahy](../../azure-monitor/platform/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Samostatná telemetrie z různých verzí

* Více rolí v aplikaci: použijte jeden prostředek Application Insights a filtrujte [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Oddělení vývoje, testování a verzí vydání: použijte jiné prostředky Application Insights. Z web.config si vyzvedněte klíče instrumentace. [Další informace](../../azure-monitor/app/separate-resources.md)
* Verze sestavení pro vytváření sestav: přidejte vlastnost pomocí inicializátoru telemetrie. [Další informace](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorování back-end serverů a aplikací klasické pracovní plochy
[Použijte modul Windows Server SDK](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Vizualizace dat
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Řídicí panel s metrikami z více aplikací
* V [Průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md)upravte svůj graf a uložte ho jako oblíbenou položku. Připněte ho na řídicí panel Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Řídicí panel s daty z jiných zdrojů a Application Insights
* [Exportujte telemetrii do Power BI](../../azure-monitor/app/export-power-bi.md ).

Nebo

* Použijte SharePoint jako řídicí panel, který zobrazuje data ve webových částech SharePointu. K [exportu do SQL použijte průběžný export a Stream Analytics](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  K prohlédnutí databáze použijte PowerView a vytvořte webovou část služby SharePoint pro PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Odfiltrování anonymních nebo ověřených uživatelů
Pokud se uživatelé přihlásí, můžete nastavit [ID ověřeného uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Automaticky se to nestane.)

Pak můžete:

* Hledat ID konkrétních uživatelů

![Snímek obrazovky, který zobrazuje možnosti searchin pro konkrétní ID uživatelů](./media/how-do-i/110-search.png)

* Filtrovat metriky buď anonymních, nebo ověřených uživatelů

![Snímek obrazovky, který zobrazuje metrixs filtrování pro anonymní i ověřené uživatele.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Úprava názvů vlastností nebo hodnot
Vytvořte [Filtr](../../azure-monitor/app/api-filtering-sampling.md#filtering). To vám umožní upravit nebo filtrovat telemetrii předtím, než se pošle z vaší aplikace do Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Vypsat konkrétní uživatele a jejich použití
Pokud chcete pouze [vyhledat konkrétní uživatele](#search-specific-users), můžete nastavit [ID ověřeného uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Pokud chcete zobrazit seznam uživatelů s daty, například které stránky se zobrazí nebo jak často se přihlašují, máte dvě možnosti:

* [Nastavte ověřené ID uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [exportujte ho do databáze](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) a použijte vhodné nástroje k analýze uživatelských dat.
* Pokud máte pouze malý počet uživatelů, odešlete vlastní události nebo metriky pomocí dat o hodnotě metriky nebo názvu události a nastavte ID uživatele jako vlastnost. Chcete-li analyzovat zobrazení stránky, nahraďte standardní volání trackPageView JavaScriptu. K analýze telemetrie na straně serveru použijte inicializátor telemetrie k přidání ID uživatele do telemetrie všech serverů. Pak můžete filtrovat a segmentovat metriky a vyhledat ID uživatele.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Snížit provoz z aplikace do Application Insights
* V [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)zakažte všechny moduly, které nepotřebujete, například kolektor čítače výkonu.
* Použijte [vzorkování a filtrování](../../azure-monitor/app/api-filtering-sampling.md) v sadě SDK.
* Na webových stránkách omezte počet volání AJAX hlášených pro každé zobrazení stránky. Ve fragmentu skriptu po `instrumentationKey:...` vložte: `,maxAjaxCallsPerView:3` (nebo vhodné číslo).
* Pokud používáte [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), před odesláním výsledku COMPUTE agreguje dávky hodnot metriky. Existuje přetížení TrackMetric (), které to poskytuje.

Přečtěte si další informace o [cenách a kvótách](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Zakázat telemetrii
Chcete-li **dynamicky zastavit a spustit** shromažďování a přenos telemetrie ze serveru aplikace:

### <a name="aspnet-classic-applications"></a>Klasické aplikace ASP.NET

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Další aplikace
Pro `TelemetryConfiguration.Active` konzolové nebo ASP.NET Core aplikace se nedoporučuje používat singleton.
Pokud jste `TelemetryConfiguration` instanci vytvořili sami – nastavte `DisableTelemetry` na `true` .

Pro aplikace ASP.NET Core můžete k `TelemetryConfiguration` instanci přistupovat pomocí [vkládání závislostí ASP.NET Core](/aspnet/core/fundamentals/dependency-injection/). Další podrobnosti najdete v článku [ApplicationInsights for ASP.NET Core Applications](../../azure-monitor/app/asp-net-core.md) .

## <a name="disable-selected-standard-collectors"></a>Zakázat vybrané standardní sběrače
Můžete zakázat standardní sběrače (například čítače výkonu, požadavky HTTP nebo závislosti).

* **ASP.NET aplikace** – odstraňte nebo Odkomentujte příslušné řádky v [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core aplikace** – v ApplicationInsights se řiďte možnostmi konfigurace modulů telemetrie [ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Zobrazit čítače výkonu systému
Mezi metrikami, které můžete zobrazit v Průzkumníkovi metrik, patří sada čítačů výkonu systému. Existuje předdefinované okno s názvem **servery** , které zobrazuje několik z nich.

![Otevřete prostředek Application Insights a klikněte na servery.](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Pokud se nezobrazí žádná data čítače výkonu
* **Server IIS** na vašem počítači nebo na virtuálním počítači. [Nainstalujte monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Web Azure** – zatím nepodporujeme čítače výkonu. K dispozici je několik metrik, které můžete získat jako standardní součást ovládacího panelu webu Azure.
* Server se systémem **UNIX**  -  [Nainstalovat shromážděné](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Zobrazení dalších čítačů výkonu
* Nejdřív [přidejte nový graf](../../azure-monitor/platform/metrics-charts.md) a podívejte se, jestli je čítač v základní sadě, kterou nabízíme.
* V takovém případě [přidejte čítač do sady shromážděné modulem čítače výkonu](../../azure-monitor/app/performance-counters.md).
