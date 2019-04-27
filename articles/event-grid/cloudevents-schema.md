---
title: Pomocí služby Azure Event Grid s událostmi ve schématu CloudEvents
description: Popisuje, jak nastavit schématu CloudEvents událostí ve službě Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 0195ce82396a7b05335242a38a2881e1b2d1afb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436592"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Použití schématu CloudEvents pomocí služby Event Grid

Kromě jeho [výchozí událost schéma](event-schema.md), nativně podporuje události v Azure Event Grid [schématu CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](https://cloudevents.io/) je [otevřete specifikace](https://github.com/cloudevents/spec/blob/master/spec.md) pro data události popisující.

CloudEvents zjednodušuje vzájemná funkční spolupráce poskytnutím společné schéma události pro publikování a využívání cloudu na základě událostí. Toto schéma umožňuje jednotnou nástroje, standardních způsobů směrování a zpracování událostí a univerzální způsoby deserializaci schématu vnější události. Se společným schématem lze snadněji práci integrovat napříč platformami.

Sestavuje CloudEvents řadou [spolupracovníci](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [základní nativní Cloud computingu Foundation](https://www.cncf.io/). Je aktuálně k dispozici jako verze 0,1.

Tento článek popisuje postup použití schématu CloudEvents pomocí služby Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Nainstalujte funkci ve verzi preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schématu

Tady je příklad událost Azure Blob Storage ve formátu CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 má k dispozici následující vlastnosti:

| CloudEvents        | Type     | Příklad hodnoty JSON             | Popis                                                        | Event Grid mapování
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | Typ došlo k výskytu                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | Verze typ eventType (volitelné)                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | Verzi CloudEvents specifikace, které používá událost        | *předat prostřednictvím*
| source             | Identifikátor URI      | "/ mycontext"                     | Popisuje Tvůrce událostí                                       | téma #subject
| ID události            | String   | "1234-1234-1234"                 | ID události                                                    | id
| čas události          | Timestamp| "2018-04-05T17:31:00Z"           | Časové razítko kdy k události došlo (volitelné)                    | čas události
| schemaURL          | Identifikátor URI      | "https:\//myschema.com"           | Odkaz na schéma, které používá atribut dat (volitelné) | *Nepoužito*
| contentType        | String   | "application/json"               | Popište formát kódování dat (volitelné)                       | *Nepoužito*
| Rozšíření         | Mapa      | {"nadbytečné": "posouzení ohrožení zabezpečení", "extB", "vB"}  | Žádné další metadata (volitelné)                                 | *Nepoužito*
| data               | Object   | {"objA": "posouzení ohrožení zabezpečení", "objB", "vB"}  | Datová část události (volitelné)                                       | data

Další informace najdete v tématu [CloudEvents specifikace](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Hodnoty hlavičky pro události, které zajišťujeme ve schématu CloudEvents spolu se schématem služby Event Grid jsou stejné s výjimkou `content-type`. Pro schématu CloudEvents, je tato hodnota hlavičky `"content-type":"application/cloudevents+json; charset=utf-8"`. Pro schéma služby Event Grid, je tato hodnota hlavičky `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurace služby Event Grid pro CloudEvents

Event Grid můžete použít pro vstupní i výstupní události ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako jsou události služby Blob Storage a události služby IoT Hub a vlastní události. Můžete také transformovat tyto události na lince vpřed a zpět.


| Vstupní schéma       | Schéma výstupu
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Event Grid formátu  | Formát CloudEvents
| Formát CloudEvents | Event Grid formátu
| Event Grid formátu  | Event Grid formátu

Pro všechna schémata událostí služby Event Grid vyžaduje ověřování při publikování na téma event gridu a při vytváření odběru událostí. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](security-authentication.md).

### <a name="input-schema"></a>Vstupní schéma

Když vytvoříte vlastní téma nastavíte vstupní schéma vlastního tématu.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

Aktuální verze CloudEvents nepodporuje, dávkové zpracování událostí. K publikování událostí s CloudEvent schématem do tématu, publikujte jednotlivě každou událost.

### <a name="output-schema"></a>Schéma výstupu

Schéma výstupu se nastavují při vytvoření odběru událostí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Pokud používáte PowerShell, použijte:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

Aktuální verze CloudEvents nepodporuje, dávkové zpracování událostí. Odběr událostí, který je nakonfigurovaný pro schéma CloudEvent dostane každá událost jednotlivě. V současné době nelze použít aktivační událost služby Event Grid pro aplikaci Azure Functions ve schématu CloudEvents se doručení události. Pomocí aktivační událost HTTP. Příklady implementace, která bude přijímat události ve schématu CloudEvents triggeru HTTP, najdete v článku [použít aktivační událost HTTP jako aktivační událost služby Event Grid](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Další postup

* Informace o monitorování doručení událostí najdete v tématu [doručování zpráv služby Event Grid monitorování](monitor-event-delivery.md).
* Doporučujeme vám vyzkoušet, komentáře, a [přispívat](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) k CloudEvents.
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
