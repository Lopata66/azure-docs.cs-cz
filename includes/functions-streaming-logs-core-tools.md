---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020337"
---
#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Pomocí možnosti `logstream` spustíte příjem protokolů streamování konkrétní aplikace funkcí běžící v Azure, jako v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Integrované streamování protokolů ještě není povolené v základních nástrojích pro aplikace Function App spuštěné v systému Linux v plánu spotřeby. Pro tyto plány hostování je místo toho nutné použít Live Metrics Stream k zobrazení protokolů téměř v reálném čase.

#### <a name="live-metrics-stream"></a>Live Metrics Stream

[Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) pro aplikaci Function App můžete zobrazit v novém okně prohlížeče `--browser` , včetně možnosti, jak je uvedeno v následujícím příkladu:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
