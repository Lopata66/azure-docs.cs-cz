---
title: Konfigurace a čtení protokolů při použití triggeru Azure Functions pro Cosmos DB
description: Naučte se, jak vystavit protokoly do kanálu protokolování Azure Functions při použití triggeru Azure Functions pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: ad53762ad490c5e8feedb83007b8721fe4883806
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334527"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Jak nakonfigurovat a číst protokoly při použití triggeru Azure Functions pro Cosmos DB

Tento článek popisuje, jak můžete nakonfigurovat prostředí Azure Functions k odeslání triggeru Azure Functions pro Cosmos DB protokolů do nakonfigurovaného [řešení monitorování](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Zahrnuté protokoly

Aktivační událost Azure Functions pro Cosmos DB používá interní [knihovnu Change feed Processor](./change-feed-processor.md) a knihovna generuje sadu protokolů o stavu, které lze použít k monitorování interních operací pro [účely řešení potíží](./troubleshoot-changefeed-functions.md).

Protokoly o stavu popisují, jak Azure Functions Trigger pro Cosmos DB při pokusu o provedení operací při vyrovnávání zatížení nebo při inicializaci chovat.

## <a name="enabling-logging"></a>Povolení protokolování

Pokud chcete povolit protokolování při použití triggeru Azure Functions pro Cosmos DB, `host.json` vyhledejte soubor v projektu Azure Functions nebo v aplikaci Azure functions a [nakonfigurujte úroveň požadovaného protokolování](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Je nutné povolit trasování pro `Host.Triggers.CosmosDB` , jak je znázorněno v následující ukázce:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Po nasazení funkce Azure Functions s aktualizovanou konfigurací se v rámci trasování zobrazí Trigger Azure Functions pro Cosmos DB protokoly. Protokoly můžete zobrazit v nakonfigurovaném zprostředkovateli protokolování pod *kategorií* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Dotazování protokolů

Spusťte následující dotaz pro dotazování protokolů generovaných triggerem Azure Functions pro Cosmos DB ve [službě Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Další postup

* [Povolte monitorování](../azure-functions/functions-monitoring.md) v aplikacích Azure Functions.
* Naučte se [diagnostikovat a řešit běžné problémy](./troubleshoot-changefeed-functions.md) při použití triggeru Azure Functions pro Cosmos DB.