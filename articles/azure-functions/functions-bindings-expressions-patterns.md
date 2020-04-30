---
title: Azure Functions výrazy a vzory vazeb
description: Naučte se vytvářet různé Azure Functions výrazy vazby na základě běžných vzorů.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277645"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions vzorů výrazů vazeb

Jednou z nejúčinnějších funkcí [triggerů a vazeb](./functions-triggers-bindings.md) jsou *výrazy vazby*. V souboru *Function. JSON* a v parametrech funkcí a kódu můžete použít výrazy, které se předají na hodnoty z různých zdrojů.

Většina výrazů je označena a tím, že je zabalena ve složených závorkách. Například ve funkci triggeru fronty se `{queueTrigger}` překládá na text zprávy fronty. Pokud je `path` `container/{queueTrigger}` vlastnost pro výstupní vazbu objektu BLOB a funkce se aktivuje ve zprávě `HelloWorld`fronty, vytvoří se objekt BLOB s názvem `HelloWorld` .

Typy vazbových výrazů

* [Nastavení aplikace](#binding-expressions---app-settings)
* [Název souboru aktivační události](#trigger-file-name)
* [Metadata aktivační události](#trigger-metadata)
* [Datové části JSON](#json-payloads)
* [Nový GUID](#create-guids)
* [Aktuální datum a čas](#current-time)

## <a name="binding-expressions---app-settings"></a>Výrazy vazeb – nastavení aplikace

Jako osvědčený postup by se měly tajné klíče a připojovací řetězce spravovat pomocí nastavení aplikace namísto konfiguračních souborů. Tato funkce omezuje přístup k těmto tajným klíčům a usnadňuje ukládání souborů, jako je *Function. JSON* v úložištích veřejného zdrojového kódu.

Nastavení aplikace jsou užitečná také v případě, že chcete změnit konfiguraci na základě prostředí. Například v testovacím prostředí budete chtít monitorovat jinou frontu nebo kontejner úložiště objektů BLOB.

Výrazy vazby nastavení aplikace jsou identifikovány jinak než jiné výrazy vazby: jsou zabaleny v procentech namísto složených závorek. Pokud je `%Environment%/newblob.txt` například cesta výstupní vazby objektu BLOB a hodnota nastavení `Environment` aplikace je `Development`, vytvoří se v `Development` kontejneru objekt BLOB.

Když je funkce spuštěná místně, hodnoty nastavení aplikace pocházejí ze souboru *Local. Settings. JSON* .

Všimněte si, `connection` že vlastnost Triggers a Bindings je zvláštní případ a automaticky řeší hodnoty jako nastavení aplikace bez znaménka procenta. 

V následujícím příkladu je Trigger služby Azure Queue Storage, který pomocí nastavení `%input-queue-name%` aplikace definuje frontu, která se má aktivovat.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Stejný přístup můžete použít v knihovnách tříd:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Název souboru aktivační události

`path` Aktivační událost objektu BLOB může být vzor, který umožňuje odkazování na název triggerového objektu BLOB v jiných vazbách a kódu funkce. Vzor může také zahrnovat kritéria filtrování, která určují, které objekty blob mohou aktivovat vyvolání funkce.

Například v následující vazbě triggeru objektu BLOB je `path` `sample-images/{filename}`vzor, který vytvoří výraz vazby s názvem: `filename`

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Výraz `filename` se pak dá použít ve výstupní vazbě k určení názvu vytvořeného objektu BLOB:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kód funkce má přístup k této stejné hodnotě pomocí `filename` názvu parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Stejnou možnost použití výrazů vazby a vzorů se vztahuje na atributy v knihovnách tříd. V následujícím příkladu jsou parametry konstruktoru atributu stejné `path` hodnoty jako předchozí *funkce. JSON* – příklady: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Můžete také vytvořit výrazy pro části názvu souboru. V následujícím příkladu je funkce aktivována pouze pro názvy souborů, které odpovídají vzoru:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Další informace o tom, jak používat výrazy a vzory v řetězci cesty objektu blob, najdete v [referenčních informacích k vazbě objektů BLOB úložiště](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadata aktivační události

Kromě datové části, kterou poskytuje Trigger (například obsah zprávy fronty, která aktivovala funkci), mnoho triggerů poskytuje další hodnoty metadat. Tyto hodnoty lze použít jako vstupní parametry v jazyce C# a F # nebo vlastnosti `context.bindings` objektu v JavaScriptu. 

Například aktivační událost Azure Queue Storage podporuje následující vlastnosti:

* QueueTrigger – aktivace obsahu zprávy, pokud je platný řetězec
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Tyto hodnoty metadat jsou přístupné ve vlastnostech souboru *Function. JSON* . Předpokládejme například, že používáte Trigger fronty a zpráva Queue obsahuje název objektu blob, který chcete číst. V souboru *Function. JSON* můžete použít `queueTrigger` vlastnost metadata ve vlastnosti objektu BLOB `path` , jak je znázorněno v následujícím příkladu:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Podrobnosti o vlastnostech metadat pro jednotlivé triggery jsou popsány v odpovídajícím referenčním článku. Příklad najdete v tématu [metadata triggeru ve frontě](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentace je také k dispozici na kartě **integrace** na portálu v části **dokumentace** pod oblastí konfigurace vazby.  

## <a name="json-payloads"></a>Datové části JSON

Pokud je datová část triggeru JSON, můžete odkazovat na její vlastnosti v konfiguraci pro jiné vazby ve stejné funkci a v kódu funkce.

Následující příklad ukazuje soubor *Function. JSON* pro funkci Webhooku, která přijímá název objektu BLOB ve formátu JSON: `{"BlobName":"HelloWorld.txt"}`. Vstupní vazba objektu BLOB přečte objekt BLOB a vazba výstupu HTTP vrátí obsah objektu BLOB v odpovědi HTTP. Všimněte si, že vstupní vazba objektu BLOB Získá název objektu BLOB odkazem přímo `BlobName` na vlastnost`"path": "strings/{BlobName}"`().

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby tato práce fungovala v jazyce C# a F #, potřebujete třídu, která definuje pole k deserializaci, jako v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

V jazyce JavaScript je deserializace JSON automaticky provedena.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Zápis tečky

Pokud jsou některé vlastnosti v datové části JSON objekty s vlastnostmi, můžete na ty odkazovat přímo pomocí zápisu teček. Předpokládejme například, že váš kód JSON vypadá takto:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Můžete odkazovat přímo na `FileName` jako. `BlobName.FileName` V tomto formátu JSON je tu, jak by `path` vlastnost v předchozím příkladu vypadala takto:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

V jazyce C# budete potřebovat dvě třídy:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Vytvořit GUID

Výraz `{rand-guid}` vazby vytvoří identifikátor GUID. Následující cesta objektu BLOB v `function.json` souboru vytvoří objekt BLOB s názvem, jako je *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuální čas

Výraz `DateTime` vazby se překládá `DateTime.UtcNow`na. Následující cesta objektu BLOB v `function.json` souboru vytvoří objekt BLOB s názvem, například *2018-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Vazba za běhu

V jazyce C# a dalších jazycích .NET můžete použít imperativní vzor vazby, a to na rozdíl od deklarativních vazeb v *Functions. JSON* a Attributes. Imperativní vazba je užitečná v případě, že parametry vazby je třeba vypočítat za běhu, nikoli jako dobu návrhu. Další informace najdete v referenční příručce pro [vývojáře v jazyce c#](functions-dotnet-class-library.md#binding-at-runtime) nebo v [Referenční příručce pro vývojáře skriptu jazyka c#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití návratové hodnoty funkce Azure Functions](./functions-bindings-return-value.md)
