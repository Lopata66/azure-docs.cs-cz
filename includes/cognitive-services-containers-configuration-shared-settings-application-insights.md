---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001146"
---
Toto `ApplicationInsights` nastavení umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](/azure/application-insights) . Application Insights poskytuje podrobné monitorování vašeho kontejneru. Svůj kontejner můžete snadno monitorovat z důvodu dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru.

Následující tabulka popisuje nastavení konfigurace podporovaná v `ApplicationInsights` části.

|Vyžadováno| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|No| `InstrumentationKey` | Řetězec | Klíč instrumentace instance Application Insights, do které se odesílají data telemetrie kontejneru. Další informace najdete v tématu [Application Insights pro ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Příklad:<br>`InstrumentationKey=123456789`|