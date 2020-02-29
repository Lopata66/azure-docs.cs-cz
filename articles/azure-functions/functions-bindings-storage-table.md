---
title: Vazby úložiště tabulek Azure pro Azure Functions
description: Naučte se používat vazby Azure Table Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915618"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Vazby úložiště tabulek Azure pro Azure Functions

Tento článek vysvětluje, jak pracovat s vazbami služby Azure Table Storage v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Vstup

Pomocí vstupní vazby Azure Table Storage si můžete přečíst tabulku v účtu Azure Storage.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte jeden řádek tabulky. Pro každý záznam vložený v tabulce se funkce aktivuje.

Hodnota klíče řádku {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte více řádků tabulky, kde `MyPoco` třída je odvozena z `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` není v [modulu runtime Functions v2](functions-versions.md)podporován. Alternativou je použití parametru `CloudTable` metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

Soubor *Function. JSON* určuje `partitionKey` a `rowKey`. Hodnota `rowKey` {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód skriptu jazyka C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce přečte entity pro klíč oddílu, který je určen ve zprávě fronty.

Tady je soubor *Function. JSON* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Kód C# skriptu přidá odkaz na sadu Azure Storage SDK, aby typ entity mohl odvozovat z `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` není podporován v modulu runtime Functions pro [verze 2. x a vyšší)](functions-versions.md). Alternativou je použití parametru `CloudTable` metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [kódu jazyka JavaScript](functions-reference-node.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

Soubor *Function. JSON* určuje `partitionKey` a `rowKey`. Hodnota `rowKey` {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Řádek jedné tabulky 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vrací seznam objektů Person, kteří jsou v zadaném oddílu v úložišti tabulek. V tomto příkladu se klíč oddílu extrahuje z trasy HTTP a tableName a připojení jsou z nastavení funkce. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

Anotace TableInput může také extrahovat vazby z těla JSON požadavku, podobně jako v následujícím příkladu.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

V následujících příkladech se používá filtr k dotazování na osoby s určitým názvem v tabulce Azure a omezení počtu možných shod na 10 výsledků.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Vstupní atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

 V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci vstupní vazby tabulky:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atributu vezme název tabulky, klíč oddílu a klíč řádku. Atribut lze použít pro parametr `out` nebo pro návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Úplný příklad naleznete v tématu Input- C# example.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Účet úložiště, který se má použít, se určuje v tomto pořadí:

* Vlastnost `Connection` atributu `Table`.
* Atribut `StorageAccount` aplikovaný na stejný parametr jako atribut `Table`.
* Atribut `StorageAccount` aplikovaný na funkci.
* Atribut `StorageAccount` aplikovaný na třídu.
* Výchozí účet úložiště pro aplikaci Function App (nastavení aplikace "AzureWebJobsStorage").

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@TableInput` anotaci u parametrů, jejichž hodnota by pocházela z tabulkového úložiště.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>`.

---

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Table`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**direction** | neuvedeno | musí být nastavené na `in`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje tabulku nebo entitu v kódu funkce. | 
|**Tabulky** | **Tabulky** | Název tabulky.| 
|**partitionKey** | **PartitionKey** |Volitelné. Klíč oddílu entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**rowKey** |**RowKey** | Volitelné. Klíč řádku entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**nezbytná** |**Nezbytná** | Volitelné. Maximální počet entit, které mají být načteny v jazyce JavaScript. Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**filtrovací** |**Filtrovací** | Volitelné. Výraz filtru OData pro vstup tabulky v JavaScriptu Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**vázán** |**Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

# <a name="c"></a>[C#](#tab/csharp)

* **Číst jeden řádek v**

  Nastavte `partitionKey` a `rowKey`. Přístup k datům tabulky pomocí parametru metody `T <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` je obvykle typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Vlastnosti `filter` a `take` se v tomto scénáři nepoužívají.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` musí být typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Pomocí metod `IQueryable` můžete provést jakékoli filtrování. V tomto scénáři nejsou použity vlastnosti `partitionKey`, `rowKey`, `filter`a `take`.  

  > [!NOTE]
  > `IQueryable` není v [modulu runtime Functions v2](functions-versions.md)podporován. Alternativou je [použití parametru paramName metody cloudu](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pro čtení tabulky pomocí sady SDK Azure Storage. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

* **Číst jeden řádek v**

  Nastavte `partitionKey` a `rowKey`. Přístup k datům tabulky pomocí parametru metody `T <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` je obvykle typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Vlastnosti `filter` a `take` se v tomto scénáři nepoužívají.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` musí být typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Pomocí metod `IQueryable` můžete provést jakékoli filtrování. V tomto scénáři nejsou použity vlastnosti `partitionKey`, `rowKey`, `filter`a `take`.  

  > [!NOTE]
  > `IQueryable` není v [modulu runtime Functions v2](functions-versions.md)podporován. Alternativou je [použití parametru paramName metody cloudu](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pro čtení tabulky pomocí sady SDK Azure Storage. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nastavte vlastnosti `filter` a `take`. Nenastavte `partitionKey` ani `rowKey`. Přístup k entitě vstupní tabulky (nebo entit) pomocí `context.bindings.<BINDING_NAME>`. Deserializované objekty mají vlastnosti `RowKey` a `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

Data tabulky se předávají funkci jako řetězec JSON. Deserializovat zprávu voláním `json.loads`, jak je znázorněno v [příkladu](#input)vstupu.

# <a name="java"></a>[Java](#tab/java)

Atribut [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) vám poskytne přístup k řádku tabulky, který funkci aktivoval.

---

## <a name="output"></a>Výstup

Použijte výstupní vazbu služby Azure Table Storage k zápisu entit do tabulky v Azure Storagem účtu.

> [!NOTE]
> Tato výstupní vazba nepodporuje aktualizaci existujících entit. K aktualizaci existující entity použijte operaci `TableOperation.Replace` [ze sady Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) .

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která pomocí triggeru http zapisuje jeden řádek tabulky. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce zapisuje více entit tabulky.

Tady je soubor *Function. JSON* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód skriptu jazyka C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu tabulky v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje více entit tabulky.

Tady je soubor *Function. JSON* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak použít výstupní vazbu tabulkového úložiště. Vazba `table` je nakonfigurována v *Function. JSON* přiřazením hodnot do `name`, `tableName`, `partitionKey`a `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
    }
  ]
}
```

Následující funkce vygeneruje jedinečnou UUI pro hodnotu `rowKey` a uloží ji do tabulkového úložiště.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci jazyka Java, která pomocí triggeru HTTP zapisuje jeden řádek tabulky.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Následující příklad ukazuje funkci jazyka Java, která pomocí triggeru HTTP zapisuje více řádků tabulky.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Výstup – atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atributu vezme název tabulky. Atribut lze použít pro parametr `out` nebo pro návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Úplný příklad naleznete v tématu [Output- C# example](#output).

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu [input-Attributes](#input---attributes-and-annotations).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

V [běhové knihovně funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte pro zápis hodnot do tabulkového úložiště [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) anotaci parametrů.

[Další podrobnosti](#output)najdete v příkladu.

---

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `Table`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavené na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**direction** | neuvedeno | musí být nastavené na `out`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje tabulku nebo entitu. Nastavte na `$return` pro odkaz na návratovou hodnotu funkce.| 
|**Tabulky** |**Tabulky** | Název tabulky.| 
|**partitionKey** |**PartitionKey** | Klíč oddílu entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#output---usage) .| 
|**rowKey** |**RowKey** | Klíč řádku entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#output---usage) .| 
|**vázán** |**Vázán** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

# <a name="c"></a>[C#](#tab/csharp)

Přístup k entitě výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName` kde `T` obsahuje vlastnosti `PartitionKey` a `RowKey`. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo dědění `TableEntity`.

Případně můžete použít parametr `CloudTable` metody k zápisu do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Přístup k entitě výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName` kde `T` obsahuje vlastnosti `PartitionKey` a `RowKey`. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo dědění `TableEntity`. Hodnota `paramName` je určena ve vlastnosti `name` *Function. JSON*.

Případně můžete použít parametr `CloudTable` metody k zápisu do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>`, kde `<name>` je hodnota zadaná ve vlastnosti `name` *funkce Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

K dispozici jsou dvě možnosti pro výstup zprávy řádku úložiště tabulky z funkce:

- **Návratová hodnota**: nastavte vlastnost `name` v *Function. JSON* na `$return`. V této konfiguraci je návratová hodnota funkce trvalá jako řádek tabulkového úložiště.

- **Imperativní**: předejte hodnotu metodě [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného jako typ [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Hodnota předaná do `set` je trvalá jako zpráva centra událostí.

# <a name="java"></a>[Java](#tab/java)

K dispozici jsou dvě možnosti pro výstup řádku úložiště tabulky z funkce pomocí anotace [TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Návratová hodnota**: použitím poznámky k samotné funkci je návratová hodnota funkce trvalá jako řádek úložiště tabulky.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), kde `T` obsahuje vlastnosti `PartitionKey` a `RowKey`. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo dědění `TableEntity`.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Tabulka | [Kódy chyb tabulky](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objekt blob, tabulka, fronta | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta | [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
