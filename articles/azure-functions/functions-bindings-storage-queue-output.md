---
title: Výstupní vazba služby Azure Queue Storage pro Azure Functions
description: Naučte se vytvářet zprávy Azure Queue Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47, tracking-python
ms.openlocfilehash: 1141186a262676fc47b0727c47e682dfe95ba6fb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055930"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Výstupní vazby Azure Queue Storage pro Azure Functions

Azure Functions může vytvořit nové zprávy Azure Queue Storage nastavením výstupní vazby.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](./functions-bindings-storage-queue.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která vytvoří zprávu fronty pro každý PŘIJATÝ požadavek HTTP.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru protokolu HTTP v *function.js* kódu souboru a [skriptu jazyka C# (. csx)](functions-reference-csharp.md) , který používá vazbu. Funkce vytvoří položku fronty s datovou částí objektu **CustomQueueMessage** pro každý PŘIJATÝ požadavek HTTP.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Zde je kód skriptu jazyka C#, který vytváří jednu zprávu fronty:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Pomocí parametru nebo můžete odeslat více zpráv najednou `ICollector` `IAsyncCollector` . Zde je kód skriptu jazyka C#, který odesílá více zpráv, jednu s daty požadavku HTTP a jednu s pevně zakódovanými hodnotami:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru protokolu HTTP v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce vytvoří položku fronty pro každý přijatý požadavek HTTP.

Tady je *function.js* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Můžete odeslat více zpráv najednou definováním pole zprávy pro `myQueueItem` výstupní vazbu. Následující kód jazyka JavaScript odesílá dvě zprávy fronty s pevně zakódovanými hodnotami pro každý přijatý požadavek HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak vymezit výstup jedné a více hodnot do front úložiště. Konfigurace potřebná pro *function.js* je stejná jako jedna.

Vazba fronty úložiště je definována v *function.jsna* místě, kde je *typ* nastaven na `queue` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Chcete-li nastavit jednotlivé zprávy ve frontě, předáte jedinou hodnotu `set` metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Chcete-li vytvořit více zpráv ve frontě, deklarujte parametr jako příslušný typ seznamu a předejte pole hodnot (odpovídající typu seznamu) `set` metodě.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 Následující příklad ukazuje funkci jazyka Java, která vytvoří zprávu fronty pro, když se aktivuje požadavkem HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@QueueOutput` poznámku k parametrům, jejichž hodnota by byla zapsána do fronty úložiště.  Typ parametru by měl být `OutputBinding<T>` , kde `T` je jakýkoli nativní typ Java Pojo.

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte rozhraní [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Atribut se vztahuje na `out` parametr nebo návratovou hodnotu funkce. Konstruktor atributu přebírá název fronty, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Vlastnost můžete nastavit `Connection` tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [výstup – příklad](#example).

Atribut můžete použít `StorageAccount` k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace najdete v tématu Trigger – atributy.

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`Poznámka umožňuje napsat zprávu jako výstup funkce. Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vytvoří zprávu fronty.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Vlastnost    | Popis |
|-------------|-----------------------------|
|`name`       | Deklaruje název parametru v signatuře funkce. Když je funkce aktivována, hodnota tohoto parametru má obsah zprávy ve frontě. |
|`queueName`  | Deklaruje název fronty v účtu úložiště. |
|`connection` | Odkazuje na připojovací řetězec účtu úložiště. |

Parametr přidružený k `QueueOutput` poznámce je zadán jako instance [OutputBinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Queue` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastaven na hodnotu `queue` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastaven na hodnotu `out` . Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné, která představuje frontu v kódu funkce. Nastavte na `$return` odkaz na návratovou hodnotu funkce.|
|**Proměnné QueueName** |**Proměnné QueueName** | Název fronty. |
|**vázán** | **Připojení** |Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Zápis jedné zprávy fronty pomocí parametru metody jako `out T paramName` . Místo parametru můžete použít návratový typ metody `out` a `T` může to být kterýkoli z následujících typů:

* Objekt serializovatelný jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte vytvořit navázání `CloudQueueMessage` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

V jazyce C# a C# zapište více zpráv fronty pomocí jednoho z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Zápis jedné zprávy fronty pomocí parametru metody jako `out T paramName` . `paramName`Je hodnota zadaná ve `name` vlastnosti *function.jsv*. Místo parametru můžete použít návratový typ metody `out` a `T` může to být kterýkoli z následujících typů:

* Objekt serializovatelný jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Pokud se pokusíte vytvořit navázání `CloudQueueMessage` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

V jazyce C# a C# zapište více zpráv fronty pomocí jednoho z následujících typů: 

* `ICollector<T>` nebo `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Položka výstupní fronta je k dispozici prostřednictvím `context.bindings.<NAME>` , kde `<NAME>` odpovídá názvu definovanému v *function.js*. Pro datovou část položky fronty lze použít řetězec nebo objekt s možností serializace JSON.

# <a name="python"></a>[Python](#tab/python)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce:

- **Návratová hodnota**: nastavte `name` vlastnost v *function.jsna* `$return` . V této konfiguraci je návratová hodnota funkce trvalá jako zpráva úložiště fronty.

- **Imperativní**: předejte hodnotu metodě [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného jako typ [out](/python/api/azure-functions/azure.functions.out?view=azure-python) . Předaná hodnota `set` je trvalá jako zpráva úložiště fronty.

# <a name="java"></a>[Java](#tab/java)

K dispozici jsou dvě možnosti pro výstup zprávy centra událostí z funkce pomocí anotace [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Návratová hodnota**: použitím poznámky k samotné funkci je vrácená hodnota funkce trvalá jako zpráva centra událostí.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , kde `T` je Pojo nebo jakýkoli nativní typ Java. Při této konfiguraci předává hodnota metodě předávání hodnoty `setValue` jako zprávy centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba |  Referenční informace |
|---|---|
| Fronta | [Chybové kódy fronty](/rest/api/storageservices/queue-service-error-codes) |
| Objekt blob, tabulka, fronta | [Kódy chyb úložiště](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta |  [Řešení potíží](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad host.jsv souboru níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [host.jsv referenčních informacích pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maximální interval mezi cykly dotazování fronty. Minimum je 00:00:00.100 (100 ms) a zvýší až 00:01:00 (1 min).  V 1. x je datový typ milisekund a v 2. x a vyšší je časový interval.|
|visibilityTimeout|00:00:00|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdařil. |
|batchSize|16|Počet zpráv ve frontě, které funkce runtime Functions načítá současně a procesy paralelně. Když se zpracovávané číslo vrátí do `newBatchThreshold` , modul runtime získá další dávku a začne zpracovávat tyto zprávy. Proto je navíc maximální počet souběžných zpráv zpracovávaných na funkci `batchSize` plus `newBatchThreshold` . Toto omezení se vztahuje odděleně na jednotlivé funkce aktivované frontou. <br><br>Pokud se chcete vyhnout paralelnímu provádění zpráv přijatých v jedné frontě, můžete nastavit `batchSize` na hodnotu 1. Toto nastavení však eliminuje souběžnost, pokud vaše aplikace Function App běží na jednom virtuálním počítači. Pokud se aplikace funkcí škáluje na více virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. |
|maxDequeueCount|5|Počet pokusů o zpracování zprávy před jejich přesunutím do nepoškozené fronty.|
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpracovávaných zpráv souběžně vrátí k tomuto číslu, modul runtime načte další dávku.|

## <a name="next-steps"></a>Další kroky

- [Spustit funkci jako změny dat úložiště ve frontě (aktivační událost)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
