---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589737"
---
Použijte výstupní vazbu Event Hubs k zápisu událostí do datového proudu událostí. K zápisu událostí do centra událostí musíte mít oprávnění Odeslat.

Před pokusem o implementaci výstupní vazby se ujistěte, že jsou na místě požadované odkazy na balíčky.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](../articles/azure-functions/functions-dotnet-class-library.md) , která zapisuje zprávu do centra událostí pomocí návratové hodnoty metody jako výstup:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Následující příklad ukazuje, jak použít rozhraní `IAsyncCollector` k odeslání dávky zpráv. Tento scénář je běžný při zpracování zpráv přicházejících z jednoho centra událostí a odeslání výsledku do jiného centra událostí.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [ C# funkci skriptu](../articles/azure-functions/functions-reference-csharp.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . První příklad je pro Functions 2. x a vyšší a druhý je pro Functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Zde je C# kód skriptu, který vytváří jednu zprávu:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Tady je kód jazyka C# skript, který vytváří více zpráv:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci JavaScriptu](../articles/azure-functions/functions-reference-node.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* . První příklad je pro Functions 2. x a vyšší a druhý je pro Functions 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je kód JavaScriptu, který pošle jednu zprávu:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Zde je kód JavaScriptu, který odesílá více zpráv:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu centra událostí v souboru *Function. JSON* a [funkci Pythonu](../articles/azure-functions/functions-reference-python.md) , která používá vazbu. Funkce zapíše zprávu do centra událostí.

Následující příklady ukazují Event Hubs vázání dat v souboru *Function. JSON* .

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tady je kód Pythonu, který pošle jednu zprávu:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci jazyka Java, která zapisuje zprávu obsahující aktuální čas do centra událostí.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@EventHubOutput` anotaci u parametrů, jejichž hodnota by byla publikována do centra událostí.  Parametr by měl být typu `OutputBinding<T>`, kde T je POJO nebo jakýkoli nativní typ Java.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

Pro [ C# knihovny tříd](../articles/azure-functions/functions-dotnet-class-library.md)použijte atribut [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Konstruktor atributu přebírá název centra událostí a název nastavení aplikace, které obsahuje připojovací řetězec. Další informace o těchto nastaveních naleznete v tématu [Output-Configuration](#configuration). Tady je příklad atributu `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output- C# example](#example).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

V [knihovně modulu runtime Functions jazyka Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)použijte anotaci [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) u parametrů, jejichž hodnota by byla publikována do centra událostí. Parametr by měl být typu `OutputBinding<T>`, kde `T` je POJO nebo jakýkoli nativní typ Java.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `EventHub`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavené na eventHub. |
|**direction** | neuvedeno | Musí být nastavena na "out". Tento parametr je nastaven automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje událost. |
|**dílčí** |**EventHubName** | Pouze funkce 1. x. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**eventHubName** |**EventHubName** | Functions 2. x a vyšší. Název centra událostí Pokud je v připojovacím řetězci přítomen i název centra událostí, tato hodnota tuto vlastnost Přepisuje za běhu. |
|**vázán** |**Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec k oboru názvů centra událostí. Zkopírujte tento připojovací řetězec kliknutím na tlačítko **informace o připojení** pro *obor názvů*, nikoli v samotném centru událostí. Tento připojovací řetězec musí mít oprávnění Odeslat pro odeslání zprávy do datového proudu událostí.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Odesílat zprávy pomocí parametru metody, jako je například `out string paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Chcete-li zapsat více zpráv, můžete místo `out string`použít `ICollector<string>` nebo `IAsyncCollector<string>`.

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Odesílat zprávy pomocí parametru metody, jako je například `out string paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Chcete-li zapsat více zpráv, můžete místo `out string`použít `ICollector<string>` nebo `IAsyncCollector<string>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>`, kde `<name>` je hodnota zadaná ve vlastnosti `name` *funkce Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce:

- **Návratová hodnota**: nastavte vlastnost `name` v *Function. JSON* na `$return`. V této konfiguraci je návratová hodnota funkce trvalá jako zpráva centra událostí.

- **Imperativní**: předejte hodnotu metodě [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného jako typ [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Hodnota předaná do `set` je trvalá jako zpráva centra událostí.

# <a name="java"></a>[Java](#tab/java)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce pomocí anotace [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) :

- **Návratová hodnota**: použitím poznámky k samotné funkci je vrácená hodnota funkce trvalá jako zpráva centra událostí.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), kde `T` je Pojo nebo jakýkoli nativní typ Java. Při této konfiguraci předává hodnota metodě `setValue` hodnotu dál jako zprávu centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum událostí | [Provozní příručka](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Ukázkový soubor host. JSON níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [reference Host. JSON pro Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Odkaz na Host. JSON ve funkcích 1. x najdete v [referenčních informacích k host. JSON pro Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|`maxBatchSize`|10|Maximální počet událostí přijatých pro jednu smyčku příjmu.|
|`prefetchCount`|300|Výchozí počet předběžného načtení, který používá základní `EventProcessorHost`.|
|`batchCheckpointFrequency`|1|Počet dávek události, které mají být zpracovány před vytvořením kontrolního bodu centra EventHub.|
