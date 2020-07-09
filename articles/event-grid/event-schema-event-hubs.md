---
title: Azure Event Hubs jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události centra událostí s Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 960aa1fe7184e1d02d28fdc135907119fee8f123
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113679"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události centra událostí.Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Event Hubs generuje typ události **Microsoft. EventHub. CaptureFileCreated** při vytvoření sběrného souboru.

### <a name="example-event"></a>Příklad události

Tato ukázková událost ukazuje schéma události centra událostí, která se aktivuje, když funkce Capture ukládá soubor: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| závislosti | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Typ | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | odkazy objektů | Data události centra událostí |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| fileUrl | řetězec | Cesta k zachycenému souboru. |
| fileType | řetězec | Typ souboru digitalizačního souboru. |
| Oddílu | řetězec | ID horizontálních oddílů |
| sizeInBytes | celé číslo | Velikost souboru. |
| eventCount | celé číslo | Počet událostí v souboru. |
| firstSequenceNumber | celé číslo | Nejmenší číslo sekvence z fronty. |
| lastSequenceNumber | celé číslo | Poslední pořadové číslo z fronty. |
| firstEnqueueTime | řetězec | Poprvé z fronty. |
| lastEnqueueTime | řetězec | Čas posledního spuštění z fronty. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md) | Když Event Hubs vytvoří zachytávací soubor, Event Grid pošle událost do aplikace Function App. Aplikace načte zachytávací soubor a migruje data do datového skladu. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Informace o zpracování událostí centra událostí najdete v tématu [streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md).