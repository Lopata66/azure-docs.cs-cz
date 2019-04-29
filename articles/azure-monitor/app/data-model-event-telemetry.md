---
title: Azure Application Insights Telemetrie datový Model - Telemetrie událostí | Dokumentace Microsoftu
description: Application Insights datový model pro telemetrie událostí
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8e519e5a5e5fe6b893fa5663e1426630f46f885f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900846"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie událostí: Datový model Application Insights

Můžete vytvořit událost položky telemetrie (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představující události, ke které došlo ve vaší aplikaci. Obvykle je interakce s uživatelem, například klikněte na tlačítko nebo objednávka rezervace. Může být událost životního cyklu aplikace jako aktualizace inicializace nebo konfigurace. 

Události sémanticky, může nebo nemusí být korelována požadavky. Pokud však správně použít telemetrie událostí je důležitější než požadavky nebo trasování. Události představují obchodní telemetrická data a mělo by být předmětu k oddělení, méně agresivní [vzorkování](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Název

Název události. Povolit správné seskupování a užitečné metriky, zakažte svou aplikaci tak, aby generuje malý počet názvů samostatná událost. Nepoužívejte například odlišný název pro každou instanci vygenerované události.

Maximální délka: 512 znaků

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- [Psát vlastní telemetrii událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované službou Application Insights.
