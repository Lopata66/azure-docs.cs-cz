---
title: Inteligentní zjišťování – nízkou míru využívání prostředků cloudu, zjistil pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování aplikací pomocí Azure Application Insights pro nízké využití prostředků cloudu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61297499"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Nízké využití procesoru v cloudových prostředků (preview)

Application Insights automaticky analyzuje využití procesoru jednotlivých instancí rolí ve vaší aplikaci a zjistí instance s nízkým využitím výkonu procesoru. Toto zjišťování umožňuje snížit vašich prostředků Azure a snížit náklady, snižuje počet instancí role, které využívá každou roli, nebo snižuje počet rolí.

Tato funkce vyžaduje žádné speciální instalační program, než [konfiguraci čítačů výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje telemetrii čítače výkonu dostatek procesoru (% času procesoru).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Při obdržení tohoto typu upozornění inteligentního zjišťování
Typické oznámení nastane, pokud mnoho instancí Role Web/Worker vykazovat nízké využití procesoru.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Aplikaci jednoznačně spotřebovat příliš mnoho prostředků?
Ne, oznámení, neznamená, že vaše aplikace jednoznačně spotřebovává příliš mnoho prostředků. I když tyto vzory nízké využití procesoru obvykle naznačují, že by se mohly snížit spotřebu prostředků, toto chování může být typické pro vaši konkrétní roli, nebo může mít fyzické obchodní odůvodnění a můžete ignorovat. Například je možné, že jsou pro další prostředky, jako je například paměti/sítě a procesoru není potřeba více instancí.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu diagnostiky:
1. **Třídění.** Oznámení se zobrazí role ve vaší aplikaci, která vykazují nízké využití procesoru. Můžete přiřadit prioritu problému.
2. **Obor.** Kolik rolí monitorujícím nízké využití procesoru a kolik instancí každé role využívat nízké využití procesoru? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Zjišťování s procentem využití procesoru, zobrazující využití procesoru u každé instance v čase. Související položky a sestavy propojení podpůrné informace, jako je například percentil využití výkonu procesoru, můžete také použít při další diagnostice problému.
