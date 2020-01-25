---
title: Odeslání události do vlastního Azure Event Grid tématu
description: Tento článek popisuje, jak odeslat událost do vlastního tématu. Zobrazuje formát dat pro odeslání a událost.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721550"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Příspěvek do vlastního tématu pro Azure Event Grid

Tento článek popisuje, jak odeslat událost do vlastního tématu. Zobrazuje formát dat pro odeslání a událost. [Smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) se vztahuje pouze na příspěvky, které odpovídají očekávanému formátu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Koncový bod

Při odesílání příspěvku HTTP na vlastní téma použijte formát identifikátoru URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Například platný identifikátor URI je: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Pokud chcete získat koncový bod pro vlastní téma pomocí Azure CLI, použijte:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Chcete-li získat koncový bod pro vlastní téma s Azure PowerShell, použijte:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Hlavička

V žádosti zahrňte hodnotu záhlaví s názvem `aeg-sas-key`, která obsahuje klíč pro ověření.

Například platná hodnota hlavičky je `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Pokud chcete získat klíč pro vlastní téma pomocí Azure CLI, použijte:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Pokud chcete získat klíč pro vlastní téma pomocí PowerShellu, použijte:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Data událostí

Pro vlastní témata data nejvyšší úrovně obsahují stejná pole jako standardní události definované prostředky. Jedna z těchto vlastností je vlastnost dat, která obsahuje vlastnosti, které jsou jedinečné pro vlastní téma. Jako vydavatel událostí určíte vlastnosti pro daný datový objekt. Použijte následující schéma:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Popis těchto vlastností najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Při odesílání událostí do tématu Event gridu může mít pole celkovou velikost až 1 MB. Každá událost v poli je omezená na 64 KB (Obecná dostupnost) nebo 1 MB (Preview).

> [!NOTE]
> K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 až KB. 

Například platné schéma dat událostí je:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Odpověď

Po odeslání do koncového bodu tématu obdržíte odpověď. Odpověď je standardní kód odpovědi HTTP. Mezi běžné odezvy patří:

|Výsledek  |Odpověď  |
|---------|---------|
|Úspěch  | 200 OK  |
|Data události mají nesprávný formát. | 400 Chybný požadavek |
|Neplatný přístupový klíč | 401 Neautorizováno |
|Nesprávný koncový bod | 404 – Nenalezeno |
|Pole nebo událost překračuje omezení velikosti. | datová část 413 je moc velká. |

V případě chyb má tělo zprávy následující formát:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
