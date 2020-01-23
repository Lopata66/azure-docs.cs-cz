---
title: Použití Azure Event Grid s událostmi ve schématu CloudEvents
description: Popisuje, jak používat CloudEvents schéma pro události v Azure Event Grid. Služba podporuje události v implementaci JSON cloudových událostí.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: e7cddf95a6537e5799acc523effb484c2249453d
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548046"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Použití schématu CloudEvents v 1.0 s Event Grid

Kromě [výchozího schématu událostí](event-schema.md)Azure Event Grid nativně podporuje události v [implementaci JSON pro vazby protokolu CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) a [http](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) je [otevřená specifikace](https://github.com/cloudevents/spec/blob/v1.0/spec.md) popisující data události.

CloudEvents zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a spotřebovávání cloudových událostí. Toto schéma umožňuje jednotným nástrojům, standardním způsobům směrování &ch událostí a univerzálním způsobům deserializace vnějšího schématu událostí. Pomocí společného schématu můžete snadněji integrovat práci na různých platformách.

CloudEvents se sestavuje několika [spolupracovníky](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [základu cloud computingu Native](https://www.cncf.io/). Je aktuálně k dispozici jako verze 1,0.

Tento článek popisuje, jak používat schéma CloudEvents s Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Nainstalujte funkci ve verzi preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schéma

Tady je příklad události Azure Blob Storage ve formátu CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Podrobný popis dostupných polí, jejich typy a definice v CloudEvents v 1.0 jsou [k dispozici zde](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Hodnoty hlaviček pro události doručené ve schématu CloudEvents a schématu Event Grid jsou stejné s výjimkou `content-type`. V případě schématu CloudEvents je hodnota hlavičky `"content-type":"application/cloudevents+json; charset=utf-8"`. Pro Event Grid schéma je hodnota hlavičky `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurace Event Grid pro CloudEvents

Event Grid můžete použít pro vstupní i výstupní události ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako jsou události Blob Storage a události IoT Hub a vlastní události. Tyto události může také transformovat na základě spojení a zpátky.


| Vstupní schéma       | Výstupní schéma
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Formát Event Grid  | Formát CloudEvents
| Formát CloudEvents | Formát Event Grid
| Formát Event Grid  | Formát Event Grid

Pro všechna schémata událostí Event Grid vyžaduje ověření při publikování do služby Event Grid a při vytváření odběru události. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](security-authentication.md).

### <a name="input-schema"></a>Vstupní schéma

Vstupní schéma pro vlastní téma nastavíte při vytváření vlastního tématu.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
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
  -InputSchema CloudEventSchemaV1_0
```

Aktuální verze CloudEvents nepodporuje dávkování událostí. Chcete-li publikovat události pomocí schématu CloudEvent do tématu, publikujte každou událost jednotlivě.

### <a name="output-schema"></a>Výstupní schéma

Výstupní schéma nastavíte při vytváření odběru události.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Pokud používáte PowerShell, použijte:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 V současné době nemůžete použít Trigger Event Grid pro aplikaci Azure Functions, když se událost doručí ve schématu CloudEvents. Použijte Trigger HTTP. Příklady implementace triggeru HTTP, který přijímá události ve schématu CloudEvents, najdete v tématu [použití CloudEvents s Azure Functions](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Ověření koncového bodu pomocí CloudEvents v 1.0

Pokud už jste obeznámeni s Event Grid, možná jste si všimli, že je Event Grid ověření koncového bodu handshake pro předcházení zneužití. CloudEvents v 1.0 implementuje svou vlastní [sémantiku ochrany proti zneužívání](security-authentication.md#webhook-event-delivery) pomocí metody HTTP. Další informace si můžete přečíst [tady](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Při použití schématu CloudEvents pro výstup Event Grid použití s ochranou zneužití CloudEvents v 1.0 místo mechanismu události ověřování Event Grid.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Použít s Azure Functions

[Vazba Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) není nativně podporovaná CloudEvents, takže funkce aktivované protokolem HTTP se používají ke čtení zpráv CloudEvents. Při použití triggeru HTTP ke čtení CloudEvents je nutné napsat kód pro to, co aktivační událost Event Grid automaticky:

* Odešle odpověď ověření k žádosti o [ověření předplatného](../event-grid/security-authentication.md#webhook-event-delivery).
* Vyvolá funkci jednou pro každý prvek pole události obsaženého v textu požadavku.

Informace o adrese URL, která se má použít k místnímu vyvolání funkce nebo při spuštění v Azure, najdete v tématu [Referenční dokumentace k aktivačním vazbám http](../azure-functions/functions-bindings-http-webhook.md) .

Následující vzorový C# kód triggeru HTTP simuluje chování triggeru Event Grid.  Tento příklad slouží pro události doručené ve schématu CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující ukázkový kód JavaScriptu pro Trigger HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Doporučujeme vám testovat, komentovat a [přispívat](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) k CloudEvents.
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
