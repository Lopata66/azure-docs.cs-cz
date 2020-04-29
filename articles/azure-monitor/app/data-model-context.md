---
title: Datový model Azure Telemetrie Application Insights – kontext telemetrie | Microsoft Docs
description: Model dat kontextu telemetrie Application Insights
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671859"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontext telemetrie: Application Insights datový model

Každá položka telemetrie může mít Kontextová pole silného typu. Každé pole umožňuje konkrétní scénář monitorování. Pomocí kolekce Custom Properties můžete ukládat vlastní nebo kontextové informace specifické pro aplikaci.


## <a name="application-version"></a>Verze aplikace

Informace v kontextových polích aplikace jsou vždy o aplikaci, která posílá telemetrii. Verze aplikace slouží k analýze změn trendů v chování aplikace a její korelace k nasazení.

Maximální délka: 1024


## <a name="client-ip-address"></a>IP adresa klienta

IP adresa klientského zařízení. Podporují se adresy IPv4 a IPv6. Při odesílání telemetrie ze služby je kontextem umístění uživatel, který operaci zahájil ve službě. Application Insights extrahuje informace o geografickém umístění z IP adresy klienta a pak ho zkrátí. Proto se IP adresa klienta sama o sobě nedá použít jako identifikovatelná informace pro koncové uživatele. 

Maximální délka: 46


## <a name="device-type"></a>Typ zařízení

Původně toto pole bylo použito k označení typu zařízení, které používá koncový uživatel aplikace. Aktuálně používá se primárně k rozlišení telemetrie JavaScriptu s typem zařízení "browser" ze telemetrie na straně serveru s typem zařízení "PC".

Maximální délka: 64


## <a name="operation-id"></a>ID operace

Jedinečný identifikátor kořenové operace. Tento identifikátor umožňuje seskupit telemetrii mezi více komponentami. Podrobnosti najdete v tématu [korelace telemetrie](../../azure-monitor/app/correlation.md) . ID operace se vytváří buď pomocí žádosti, nebo zobrazením stránky. Všechny ostatní telemetrie nastaví toto pole na hodnotu obsahujícího zobrazení žádosti nebo stránky. 

Maximální délka: 128


## <a name="parent-operation-id"></a>ID nadřazené operace

Jedinečný identifikátor bezprostředního nadřazeného prvku položky telemetrie Podrobnosti najdete v tématu [korelace telemetrie](../../azure-monitor/app/correlation.md) .

Maximální délka: 128


## <a name="operation-name"></a>Název operace

Název (skupina) operace. Název operace je vytvořen buď pomocí žádosti, nebo zobrazením stránky. Všechny ostatní položky telemetrie nastaví toto pole na hodnotu obsahujícího zobrazení žádosti nebo stránky. Název operace se používá k vyhledání všech položek telemetrie pro skupinu operací (například GET Home/index). Tato vlastnost kontextu slouží k zodpovězení otázek, jako je "Jaké jsou typické výjimky vyvolané na této stránce."

Maximální délka: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetický zdroj operace

Název syntetického zdroje Některé telemetrie z aplikace mohou představovat syntetické přenosy. Může se jednat o webový prohledávací nástroj, který indexuje web, testy dostupnosti webu nebo trasování z knihoven diagnostiky, jako je Application Insights samotné sady SDK.

Maximální délka: 1024


## <a name="session-id"></a>ID relace

ID relace – instance interakce uživatele s aplikací. Informace v kontextových polích relace jsou vždy o koncovém uživateli. Při odesílání telemetrie ze služby je kontext relace o uživateli, který operaci zahájil ve službě.

Maximální délka: 64


## <a name="anonymous-user-id"></a>ID anonymního uživatele

ID anonymního uživatele Představuje koncového uživatele aplikace. Při odesílání telemetrie ze služby je uživatelský kontext o uživateli, který operaci zahájil ve službě.

[Vzorkování](../../azure-monitor/app/sampling.md) je jedním z postupů pro minimalizaci množství shromážděné telemetrie. Algoritmus vzorkování se pokusí buď vzorkovat v rámci celé korelační telemetrie, nebo na ně. Pro generování skóre vzorkování se používá anonymní ID uživatele. Takže ID anonymního uživatele by mělo být náhodným množstvím hodnoty. 

Použití anonymního uživatelského ID k uložení uživatelského jména je zneužití pole. Použijte ID ověřeného uživatele.

Maximální délka: 128


## <a name="authenticated-user-id"></a>ID ověřeného uživatele

ID ověřeného uživatele Opakem ID anonymního uživatele Toto pole představuje uživatele s popisným názvem. Vzhledem k tomu, že informace o PII nejsou shromažďovány ve výchozím nastavení většinou v sadě SDK.

Maximální délka: 1024


## <a name="account-id"></a>ID účtu

V aplikacích s více klienty se jedná o ID nebo název účtu, se kterým uživatel pracuje. Příklady mohou být ID předplatného pro Azure Portal nebo název blogu pro blogovací platformu.

Maximální délka: 1024


## <a name="cloud-role"></a>Cloudová role

Název role, které je aplikace součástí. Provede mapování přímo na název role v Azure. Lze ji také použít k rozlišení mikroslužeb, které jsou součástí jedné aplikace.

Maximální délka: 256


## <a name="cloud-role-instance"></a>Instance cloudové role

Název instance, ve které je aplikace spuštěná. Název počítače pro místní počítač, název instance pro Azure.

Maximální délka: 256


## <a name="internal-sdk-version"></a>Interní: verze sady SDK

Verze sady SDK. Informace najdete v [tomto článku](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) .

Maximální délka: 64


## <a name="internal-node-name"></a>Interní: název uzlu

Toto pole představuje název uzlu, který se používá pro účely fakturace. Použijte ji k přepsání standardní detekce uzlů.

Maximální délka: 256


## <a name="next-steps"></a>Další kroky

- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Podívejte se na standardní [konfiguraci](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)kolekce vlastností kontextu.
