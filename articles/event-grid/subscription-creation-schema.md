---
title: Schéma předplatného služby Azure Event Grid
description: Popisuje vlastnosti pro přihlášení k odběru události pomocí služby Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822789"
---
# <a name="event-grid-subscription-schema"></a>Schéma předplatného Event Grid

Pokud chcete vytvořit odběr Event gridu, odeslat požadavek na operaci předplatného vytvořit událost. Použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Například pro vytvoření odběru událostí účtu úložiště s názvem `examplestorage` ve skupině prostředků s názvem `examplegroup`, použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Název odběru události musí být dlouhý 3 až 64 znaků a může obsahovat pouze a – z, A-Z, 0-9, a "-". Tento článek popisuje vlastnosti a schéma pro text žádosti.
 
## <a name="event-subscription-properties"></a>Vlastnosti odběru událostí

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| cíl | objekt | Objekt, který definuje koncový bod. |
| Filtr | objekt | Volitelné pole k filtrování typů událostí. |

### <a name="destination-object"></a>cílový objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| endpointType | řetězec | Typ koncového bodu pro předplatné (webhook/HTTP, Centrum událostí nebo fronty). | 
| endpointUrl | řetězec | Cílová adresa URL pro události v tento odběr události. | 

### <a name="filter-object"></a>objekt filtru

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| includedEventTypes | pole | Shoda, když typ události ve zprávě události je přesnou shodu na jednu z těchto názvů typu event. Vyvolá chybu, pokud název události se neshoduje s názvy typů registrované události pro zdroje událostí. Výchozí odpovídá všechny typy událostí. |
| subjectBeginsWith | řetězec | Shodu předpony filtr na pole Předmět v případě zprávy. Výchozí nebo prázdný řetězec odpovídá všem. | 
| subjectEndsWith | řetězec | Přípona match filtr na pole Předmět v případě zprávy. Výchozí nebo prázdný řetězec odpovídá všem. |
| isSubjectCaseSensitive | řetězec | Ovládací prvky malá a velká písmena odpovídající pro filtry. |


## <a name="example-subscription-schema"></a>Příklad schéma předplatného

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Další postup

* Úvod do služby Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)