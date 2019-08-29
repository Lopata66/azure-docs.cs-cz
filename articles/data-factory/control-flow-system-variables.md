---
title: Systémové proměnné v Azure Data Factory | Microsoft Docs
description: Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: d932164f972169c93e368a41c09df039196341b5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141636"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporované nástrojem Azure Data Factory
Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.

## <a name="pipeline-scope"></a>Obor kanálu
Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON kanálu.

| Název proměnné | Popis |
| --- | --- |
| @pipeline().DataFactory |Název datové továrny, na které běží běh kanálu v rámci |
| @pipeline().Pipeline |Název kanálu |
| @pipeline().RunId | ID konkrétního spuštění kanálu |
| @pipeline().TriggerType | Typ triggeru, který vyvolal kanál (ruční, Scheduler) |
| @pipeline().TriggerId| ID triggeru, který vyvolá kanál |
| @pipeline().TriggerName| Název triggeru, který vyvolá kanál. |
| @pipeline(). TriggerTime| Čas, kdy Trigger vyvolal kanál. Doba triggeru je skutečný čas aktivace, nikoli naplánovaný čas. Například `13:20:08.0149599Z` se vrátí místo`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Rozsah triggeru plánu
Na tyto systémové proměnné se dá odkazovat kdekoli v triggeru JSON, pokud je Trigger typu: "ScheduleTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger().scheduledTime |Čas, kdy byla aktivační událost naplánována k vyvolání spuštění kanálu. Například pro Trigger, který se aktivuje každých 5 minut, tato proměnná vrátí `2017-06-01T22:20:00Z` `2017-06-01T22:29:00Z` , `2017-06-01T22:25:00Z`v uvedeném pořadí.|
| @trigger().startTime |Čas, kdy se Trigger **skutečně** vyvolal pro vyvolání spuštění kanálu. Například pro Trigger, který se aktivuje každých 5 minut, může tato proměnná vracet něco podobného `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:29:00.9935483Z` , `2017-06-01T22:25:00.7958577Z`v uvedeném pořadí. (Poznámka: Časové razítko je ve výchozím nastavení ve formátu ISO 8601.)|

## <a name="tumbling-window-trigger-scope"></a>Rozsah triggeru pro bubnový interval
Na tyto systémové proměnné se dá odkazovat kdekoli v triggeru JSON, pokud je Trigger typu: "TumblingWindowTrigger."
(Poznámka: Časové razítko je ve výchozím nastavení ve formátu ISO 8601.)

| Název proměnné | Popis |
| --- | --- |
| @trigger().outputs.windowStartTime |Spustí se okno, když se Trigger naplánoval na vyvolání běhu kanálu. Pokud má aktivační událost pro bubnové okno frekvenci "každou hodinu", bude to čas na začátku hodiny.|
| @trigger().outputs.windowEndTime |Konec okna v případě, že Trigger naplánoval vyvolání spuštění kanálu. Pokud má aktivační událost pro bubnové okno frekvenci "každou hodinu", bude to čas na konci hodiny.|
## <a name="next-steps"></a>Další postup
Informace o tom, jak se tyto proměnné používají ve výrazech, najdete v tématu [Expression language & Functions](control-flow-expression-language-functions.md).
