---
title: Durable Functions publikování do služby Azure Event Grid (preview)
description: Zjistěte, jak konfigurovat automatické publikování Azure Event Grid pro Durable Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733707"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publikování do služby Azure Event Grid (preview)

Tento článek popisuje, jak nastavit Durable Functions k publikování Orchestrace životní cyklus (například, vytvoří se události, dokončenou a neúspěšné) pro vlastní [tématu Azure Event gridu](https://docs.microsoft.com/azure/event-grid/overview).

Toto jsou některé scénáře, kdy se tato funkce je užitečná:

* **Scénáře DevOps, jako je nasazení modrá nebo zelená**: Můžete chtít vědět, pokud jsou všechny úkoly spuštěné před implementací [strategii nasazení vedle sebe](durable-functions-versioning.md#side-by-side-deployments).

* **Přidává rozšířenou podporu monitorování a diagnostiku**: Vám může udržovat přehled o informace o stavu Orchestrace v externím úložišti optimalizované pro dotazy, jako je SQL database nebo cosmos DB.

* **Aktivita dlouho běžící na pozadí**: Pokud používáte Durable Functions pro dlouho běžící aktivitu na pozadí, tato funkce pomáhá zjistit aktuální stav.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc nebo později v projektu Durable Functions.
* Nainstalujte [emulátoru úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo použijte [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Vytvoření tématu vlastní event gridu

Vytvořte téma event gridu pro odesílání událostí z Durable Functions. Následující pokyny ukazují, jak vytvořit téma s použitím rozhraní příkazového řádku Azure. Informace o tom, jak to udělat pomocí Powershellu nebo na webu Azure portal najdete v následujících článcích:

* [EventGrid rychlých startů: Vytvoření vlastní události – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid rychlých startů: Vytvoření vlastní události – Azure portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. Azure Event Grid v současné době nepodporuje všech oblastech. Informace o tom, které oblasti jsou podporovány, naleznete v tématu [Přehled služby Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma event gridu poskytuje koncový bod definovaný uživatelem, do kterého odesíláte události do. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože bude záznam DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Získání koncového bodu a klíče

Získejte koncový bod tohoto tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Získáte klíč tématu. Nahraďte `<topic_name>` s vámi zvolený název.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Teď můžete odesílat události do tématu.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurace publikování Azure Event Grid

V projektu Durable Functions najdete `host.json` souboru.

Přidat `eventGridTopicEndpoint` a `eventGridKeySettingName` v `durableTask` vlastnost.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Je to možné vlastnosti konfigurace služby Azure Event Grid najdete v [dokumentace k host.json](../functions-host-json.md#durabletask). Po dokončení konfigurace `host.json` soubor, aplikace function app odesílá události životního cyklu do tématu event gridu. Tento postup funguje při spuštění aplikace function app, místně i v Azure. ".

Nastavte nastavení aplikace, které pro klíč tématu do aplikace Function App a `local.setting.json`. Následující kód JSON je ukázka `local.settings.json` pro místní ladění. Nahraďte `<topic_key>` s klíč tématu.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Ujistěte se, že [emulátor úložiště](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) funguje. Je vhodné spustit `AzureStorageEmulator.exe clear all` před provedením příkazu.

## <a name="create-functions-that-listen-for-events"></a>Vytvoření funkce, které naslouchat událostem

Vytvoření aplikace Function App. Je nejlepší umístit ve stejné oblasti jako téma event gridu.

### <a name="create-an-event-grid-trigger-function"></a>Vytvoření funkce pro aktivaci event grid

Vytvoření funkce, která se zobrazí události životního cyklu. Vyberte **vlastní funkci**.

![Vyberte možnost vytvořit vlastní funkci.](./media/durable-functions-event-publishing/functions-portal.png)

Zvolte Trigger služby Event Grid a vyberte `C#`.

![Vyberte Trigger služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Zadejte název funkce a pak vyberte `Create`.

![Vytvoření triggeru služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Vytvoří funkci s následujícím kódem:

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Vyberte `Add Event Grid Subscription`. Tato operace přidá odběr event gridu pro téma event gridu, kterou jste vytvořili. Další informace najdete v tématu [koncepty ve službě Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Vyberte odkaz na Trigger služby Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Vyberte `Event Grid Topics` pro **typ tématu**. Vyberte skupinu prostředků, kterou jste vytvořili pro téma event gridu. Vyberte instanci téma event gridu. Stisknutím klávesy `Create`.

![Vytvoří odběr Event Gridu.](./media/durable-functions-event-publishing/eventsubscription.png)

Nyní jste připraveni přijímat události životního cyklu.

## <a name="create-durable-functions-to-send-the-events"></a>Vytvoření Durable Functions k odesílání událostí

Ve vašem projektu Durable Functions zahájit ladění na místním počítači.  Následující kód je stejný jako kód šablony pro Durable Functions. Jste již nakonfigurovali `host.json` a `local.settings.json` na místním počítači.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Při volání `Sample_HttpStart` Postman nebo prohlížeče, spustí odolné funkce pro odeslání události životního cyklu. Koncový bod se obvykle `http://localhost:7071/api/Sample_HttpStart` pro místní ladění.

Zobrazí protokoly z funkce, kterou jste vytvořili na webu Azure Portal.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schéma událostí

Následující seznam popisuje schéma události životního cyklu:

* **`id`**: Jedinečný identifikátor události event gridu.
* **`subject`**: Cesta k předmětu událostí. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` bude `Running`, `Completed`, `Failed`, a `Terminated`.  
* **`data`**: Odolná služba Functions konkrétní parametry.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) název.
  * **`functionName`**: Název funkce nástroje Orchestrator.
  * **`instanceId`**: Trvalý instanceId funkce.
  * **`reason`**: Další data přidružená k události sledování. Další informace najdete v tématu [diagnostiky v Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Orchestrace stav modulu Runtime. Spuštěna, dokončena, se nezdařilo, protože bylo zrušeno.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Čas události (UTC).
* **`dataVersion`**: Verze schématu událostí životního cyklu.
* **`metadataVersion`**:  Verze metadat.
* **`topic`**: Event grid tématu prostředek.

## <a name="how-to-test-locally"></a>Místní testování

Chcete-li otestovat místně, použijte [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další Správa instancí v Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Další informace ve Durable Functions](durable-functions-versioning.md)
