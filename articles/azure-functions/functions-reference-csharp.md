---
title: Funkce jazyka C# skript pro vývojáře referenční informace k Azure
description: Naučte se vyvíjet funkce Azure použitím skriptu jazyka C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 232a235cdbf9dc3934bdac14f9612d6865718823
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892411"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# (.csx) pro vývojáře odkaz na skript

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Tento článek je Úvod do vytváření Azure Functions s použitím skriptu jazyka C# (*.csx*).

Služba Azure Functions podporuje C# a C# skript programovacích jazyků. Pokud hledáte pokyny [pomocí jazyka C# v sadě Visual Studio projekt knihovny tříd](functions-develop-vs.md), naleznete v tématu [jazyka C# – reference pro vývojáře](functions-dotnet-class-library.md).

Tento článek předpokládá, že jste už čtete [Příručka pro vývojáře Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak funguje .csx

Je na základě zkušeností skriptu C# pro službu Azure Functions [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data budou téci do funkce jazyka C# prostřednictvím argumenty metody. Argument názvy jsou určené v `function.json` souborů a jsou předdefinované funkce protokolovací nástroj a zrušení tokenů, jako jsou názvy pro přístup k věci.

*.Csx* formátu umožňuje psát méně "standardní" a zaměřit se na psaní pouze funkce C#. Namísto obtékání všechno, co v oboru názvů a třídy, stačí definovat `Run` metoda. Zahrnout všechny odkazy na sestavení a obory názvů na začátku souboru jako obvykle.

Aplikace funkcí *.csx* soubory jsou zkompilovány při inicializaci instance. Tento krok kompilace znamená, že kroky, jako je úplné spuštění může trvat delší dobu skript funkcí jazyka C# ve srovnání s knihoven tříd C#. Tento krok kompilace je také Proč jsou funkce skriptu jazyka C# upravit na webu Azure Portal, zatímco nejsou knihovny tříd jazyka C#.

## <a name="folder-structure"></a>struktura složek

Struktura složek projektu skriptu jazyka C# vypadá takto:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Existuje sdílený [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má svůj vlastní soubor s kódem (.csx) a vazbu konfigurační soubor (function.json).

Rozšíření vazby vyžaduje [verze 2.x](functions-versions.md) funkce modulu runtime jsou definovány v `extensions.csproj` souboru se soubory knihovny v `bin` složky. Při vývoji místně, musíte [registraci rozšíření vazby](./functions-bindings-register.md#local-development-azure-functions-core-tools). Při vytváření funkcí na webu Azure Portal, je tato registrace provede za vás.

## <a name="binding-to-arguments"></a>Vytvoření vazby na argumenty

Vstupní nebo výstupní data je svázaná s C# skript parametr funkce prostřednictvím `name` vlastnost *function.json* konfigurační soubor. Následující příklad ukazuje *function.json* souboru a *run.csx* souboru pro funkci aktivovanou protokolem fronty. Název parametru, který přijímá data ze zprávy fronty `myQueueItem` vzhledem k tomu, který je hodnota `name` vlastnost.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r` Příkazu je vysvětleno [dále v tomto článku](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má svůj vlastní podporované typy; například aktivační událost objektů blob je možné s parametrem řetězec, parametr POCO `CloudBlockBlob` parametr nebo některý další podporované typy. [Článku o vazbě pro vazby objektů blob](functions-bindings-storage-blob.md#trigger---usage) zobrazí všechny podporované typy parametrů pro triggery objektů blob. Další informace najdete v tématu [aktivačními událostmi a vazbami](functions-triggers-bindings.md) a [vazby referenční dokumenty pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odkazování na vlastní třídy

Pokud potřebujete použít vlastní třídu prostý staré CLR objektů POCO, můžete zahrnout definice třídy ve stejném souboru nebo ji umístit do samostatného souboru.

Následující příklad ukazuje *run.csx* příklad, který obsahuje definici třídy POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Třída POCO musí mít metodu getter a setter definované pro každou vlastnost.

## <a name="reusing-csx-code"></a>Opětovné použití kódu .csx

Můžete použít třídy a metody definované v jiných *.csx* soubory ve vašich *run.csx* souboru. K tomuto účelu použijte `#load` direktivy ve vaší *run.csx* souboru. V následujícím příkladu rutiny protokolování s názvem `MyLogger` se sdílí v *myLogger.csx* a načtena do *run.csx* pomocí `#load` – direktiva:

Příklad *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Příklad *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Použití sdílené *.csx* soubor je běžný vzor, pokud chcete silného typu dat předávaných mezi funkcí pomocí pomocí objektů POCO. V následující zjednodušený příklad triggerem HTTP a aktivační událost fronty sdílet POCO objekt s názvem `Order` silného typu pořadí dat:

Příklad *run.csx* pro HTTP trigger:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Příklad *run.csx* pro aktivační událost fronty:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Příklad *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Můžete použít relativní cestu `#load` – direktiva:

* `#load "mylogger.csx"` načte soubor umístěný ve složce funkce.
* `#load "loadedfiles\mylogger.csx"` načte soubor umístěný ve složce ve složce funkce.
* `#load "..\shared\mylogger.csx"` načte soubor umístěný ve složce na stejné úrovni jako funkce složky tedy přímo pod *wwwroot*.

`#load` – Direktiva funguje pouze s *.csx* souborů, ne s *.cs* soubory.

## <a name="binding-to-method-return-value"></a>Vytvoření vazby na návratovou hodnotu metody

Návratová hodnota metody můžete použít pro výstupní vazby, pomocí názvu `$return` v *function.json*. Příklady najdete v tématu [aktivačními událostmi a vazbami](./functions-bindings-return-value.md).

Použijte návratovou hodnotu, pouze v případě, že výsledky spuštění úspěšné funkce, který je vždy v návratovou hodnotu pro předání do výstupní vazbu. Jinak použijte `ICollector` nebo `IAsyncCollector`, jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupní hodnoty

Zapsat do výstupní vazbu více hodnot nebo pokud volání úspěšné funkce nemusí nic k předání do výstupní vazbu, použijte [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy. Tyto typy jsou pouze pro zápis kolekce, které jsou zapsány do výstupní vazbu po dokončení metody.

Tento příklad zapíše více front zpráv do stejné frontě pomocí `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Protokolování

Do protokolu výstupní datový proud protokolů v C#, zahrnují argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Doporučujeme vám, že si vzpomenete `log`. Vyhněte se použití `Console.Write` ve službě Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Informace o novější protokolovacího rozhraní, které můžete použít namísto `TraceWriter`, naleznete v tématu [zápisu přihlásí funkcí jazyka C#](functions-monitoring.md#write-logs-in-c-functions) v **monitorování Azure Functions** článku.

## <a name="async"></a>Async

Provést funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), použijte `async` – klíčové slovo a vraťte se `Task` objektu.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nemůžete použít `out` parametry v asynchronních funkcí. Výstupní vazby, použijte [návratovou hodnotu funkce](#binding-to-method-return-value) nebo [objekt kolekce](#writing-multiple-output-values) místo.

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, který umožňuje operačnímu systému upozornit funkce se chystá ukončit váš kód. Toto oznámení můžete použít k Ujistěte se, že funkce nebude dojde k neočekávanému ukončení tak, aby data ponechá v nekonzistentním stavu.

Následující příklad ukazuje, jak vyhledat brzké ukončení funkce.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Import obory názvů

Pokud je potřeba importovat obory názvů, můžete provést tak jako obvykle, se `using` klauzuli.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Následující obory názvů automaticky importují a proto jsou volitelné:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení

Pro sestavení rozhraní, přidejte odkazy pomocí `#r "AssemblyName"` směrnice.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Následující sestavení jsou automaticky přidány pomocí Azure Functions, který je hostitelem prostředí:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Následující sestavení mohou být odkazovány jednoduchý název (například `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odkazování na vlastní sestavení

Odkazovat na vlastní sestavení, můžete použít buď *sdílené* sestavení nebo *privátní* sestavení:

* Sdílená sestavení jsou sdíleny napříč všechny funkce v rámci aplikace function app. Odkazovat na vlastní sestavení, nahrajte do složky s názvem sestavení `bin` ve vašich [funkce aplikace kořenové složky](functions-reference.md#folder-structure) (wwwroot).

* Soukromá sestavení jsou součástí kontextu dané funkce a podpory různých verzí bokem. Soukromá sestavení musí být nahrán v `bin` složky v adresáři funkce. Odkazovat na sestavení pomocí názvu souboru, například `#r "MyAssembly.dll"`.

Informace o tom, jak nahrávat soubory do složky funkce, najdete v části na [Správa balíčků](#using-nuget-packages).

### <a name="watched-directories"></a>Sledované adresáře

Adresář obsahující soubor skriptu funkce je automaticky sledované změny sestavení. Chcete-li sledovat změny sestavení v dalších adresářích, přidejte je do `watchDirectories` seznamu v [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Pomocí balíčků NuGet
Balíčky NuGet používané k 2.x C# funkci, nahrajte *function.proj* soubor do složky funkce v systému souborů aplikace function app. Tady je příklad *function.proj* soubor, který přidá odkaz na *Microsoft.ProjectOxford.Face* verze *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    
    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Pokud chcete použít vlastní NuGet informačního kanálu, určit informačního kanálu ve *Nuget.Config* souboru v kořenovém adresáři aplikace Function App. Další informace najdete v tématu [konfigurace NuGet chování](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> V 1.x C# funkce a balíčky NuGet jsou odkazovány pomocí *project.json* souboru místo *function.proj* souboru.

Funkce 1.x, použijte *project.json* souboru místo toho. Tady je příklad *project.json* souboru: 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Použití souboru function.proj

1. Funkce otevřete na webu Azure Portal. Na kartě protokoly se zobrazí výstup instalace balíčku.
2. K nahrání *function.proj* souboru, použijte jednu z metod popsaných v [aktualizace souborů aplikace funkce](functions-reference.md#fileupdate) v referenčním tématu pro vývojáře Azure Functions.
3. Po *function.proj* nahraje soubor, zobrazí se výstup jako v následujícím příkladu ve své funkci je streamování protokolů:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Proměnné prostředí

Chcete-li získat proměnnou prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, jak je znázorněno v následujícím příkladu kódu:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Vazbu za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vazby modelu, nikoli [ *deklarativní* ](https://en.wikipedia.org/wiki/Declarative_programming) vazby v *function.json*. Imperativní vazby je užitečné, když parametry vazby musí vypočítat v době běhu spíše než návrhu. V tomto modelu můžete vázat na podporované vstupní a výstupní vazby na průběžné v kódu funkce.

Definování dnešní vazby následujícím způsobem:

- **Ne** zahrnout položku v *function.json* pro požadovanou imperativní vazby.
- Předejte vstupní parametr [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- K provedení vazby dat používají následující vzor jazyka C#.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` je atribut .NET, který definuje vaši vazby a `T` je vstupní nebo výstupní typ, který podporuje tento typ vazby. `T` nemůže být `out` typ parametru (například `out JObject`). Například v tabulce Mobile Apps Výstupní vazba podporuje [šest výstupní typy](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)pro `T`.

### <a name="single-attribute-example"></a>Příklad jeden atribut

Následující příklad kódu vytvoří [výstupní vazby úložiště blob](functions-bindings-storage-blob.md#output) s objektem blob cestu, která je definována v době běhu, pak zapíše řetězec do objektu blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definuje [úložiště objektů blob](functions-bindings-storage-blob.md) vstupní nebo výstupní vazby, a [TextWriter](/dotnet/api/system.io.textwriter) je typ vazby podporované výstup.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace function App. hlavní připojovací řetězec účtu úložiště (což je `AzureWebJobsStorage`). Můžete zadat nastavení vlastní aplikace, které chcete použít pro účet úložiště tak, že přidáte [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předání do pole atributu `BindAsync<T>()`. Použití `Binder` parametr, ne `IBinder`.  Příklad:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

V následující tabulce jsou uvedeny atributy .NET pro každý typ vazby a balíčky, ve kterých jsou definovány.

> [!div class="mx-codeBreakAll"]
> | Vazba | Atribut | Přidat odkaz |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fronta úložiště | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Objekt blob úložiště | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage table | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivační události a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro službu Azure Functions](functions-best-practices.md)
