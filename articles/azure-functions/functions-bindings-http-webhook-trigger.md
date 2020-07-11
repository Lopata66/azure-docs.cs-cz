---
title: Aktivační událost Azure Functions HTTP
description: Naučte se volat Azure Function přes HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 437cbb87694adf89054161a7b0d40f6528b94199
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224090"
---
# <a name="azure-functions-http-trigger"></a>Aktivační událost Azure Functions HTTP

Trigger HTTP umožňuje vyvolat funkci s požadavkem HTTP. Pomocí triggeru HTTP můžete vytvářet rozhraní API bez serveru a reagovat na Webhooky.

Výchozí návratová hodnota funkce aktivované protokolem HTTP je:

- `HTTP 204 No Content`prázdné tělo ve funkcích 2. x a vyšší
- `HTTP 200 OK`prázdné tělo ve funkcích 1. x

Chcete-li upravit odpověď protokolu HTTP, nakonfigurujte [výstupní vazbu](./functions-bindings-http-webhook-output.md).

Další informace o vazbách HTTP naleznete v tématu [Přehled](./functions-bindings-http-webhook.md) a [výstupní odkaz na vazbu](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která vyhledává `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP. Všimněte si, že návratová hodnota se používá pro výstupní vazbu, ale atribut návratové hodnoty není povinný.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu C#, ke kterému se váže `HttpRequest` :

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Můžete vytvořit navázání na vlastní objekt namísto `HttpRequest` . Tento objekt se vytvoří z těla žádosti a analyzuje se jako JSON. Podobně lze typ předat výstupní vazbě odpovědi HTTP a vrátit se jako text odpovědi spolu se `200` stavovým kódem.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.js* souboru:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.js* souboru:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Načíst parametr z řetězce dotazu](#read-parameter-from-the-query-string)
* [Přečíst tělo žádosti POST](#read-body-from-a-post-request)
* [Načíst parametr z trasy](#read-parameter-from-a-route)
* [Číst tělo POJO z požadavku POST](#read-pojo-body-from-a-post-request)

Následující příklady znázorňují vazbu triggeru HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Načíst parametr z řetězce dotazu

Tento příklad přečte parametr s názvem `id` , z řetězce dotazu a použije ho k sestavení dokumentu JSON vráceného klientovi s typem obsahu `application/json` .

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Přečíst tělo žádosti POST

V tomto příkladu se přečte tělo žádosti POST jako a `String` a použije se k sestavení dokumentu JSON vráceného klientovi s typem obsahu `application/json` .

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Načíst parametr z trasy

Tento příklad načte povinný parametr s názvem `id` a nepovinný parametr `name` z cesty trasy a použije je k sestavení dokumentu JSON vráceného klientovi s typem obsahu `application/json` . T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Číst tělo POJO z požadavku POST

Zde je kód pro `ToDoItem` třídu, na kterou se odkazuje v tomto příkladu:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Tento příklad přečte tělo žádosti POST. Text žádosti se automaticky rozdělí do `ToDoItem` objektu a vrátí se klientovi s typem obsahu `application/json` . `ToDoItem`Parametr je serializován modulem runtime Functions, protože je přiřazen `body` vlastnosti `HttpMessageResponse.Builder` třídy.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md) a Java `HttpTrigger` je k dispozici atribut pro konfiguraci funkce.

Můžete nastavit úroveň autorizace a povolené metody HTTP v parametrech konstruktoru atributu, typ Webhooku a šablonu směrování. Další informace o těchto nastaveních najdete v tématu věnovaném [konfiguraci](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Tento příklad ukazuje, jak použít atribut [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Úplný příklad najdete v [příkladu triggeru](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Tento příklad ukazuje, jak použít atribut [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Úplný příklad najdete v [příkladu triggeru](#example).

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `HttpTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **textový** | Není k dispozici| Požadováno – musí být nastavené na `httpTrigger` . |
| **směr** | Není k dispozici| Požadováno – musí být nastavené na `in` . |
| **Jméno** | Není k dispozici| Required – název proměnné použitý v kódu funkce pro text žádosti nebo žádosti. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Určuje, které klíče (pokud existují) musí být k žádosti přítomny, aby bylo možné funkci vyvolat. Úroveň autorizace může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;Není vyžadován žádný klíč rozhraní API.</li><li><code>function</code>&mdash;Klíč rozhraní API specifický pro funkci je povinný. Toto je výchozí hodnota, pokud není zadána žádná.</li><li><code>admin</code>&mdash;Hlavní klíč je povinný.</li></ul> Další informace najdete v části o [autorizačních klíčích](#authorization-keys). |
| **způsobů** |**Metody** | Pole metod HTTP, na které funkce reaguje. Pokud není zadaný, funkce reaguje na všechny metody HTTP. Viz [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **cestě** | **Cestě** | Definuje šablonu směrování, která řídí, které adresy URL žádostí vaše funkce reaguje. Výchozí hodnota, pokud není zadána, je `<functionname>` . Další informace najdete v tématu [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Podporováno pouze pro modul runtime verze 1. x._<br/><br/>Nakonfiguruje Trigger HTTP tak, aby sloužil jako přijímač [Webhooku](https://en.wikipedia.org/wiki/Webhook) pro zadaného zprostředkovatele. `methods`Pokud jste nastavili tuto vlastnost, nenastavujte tuto vlastnost. Typ Webhooku může být jedna z následujících hodnot:<ul><li><code>genericJson</code>&mdash;Koncový bod Webhooku pro obecné účely bez logiky pro konkrétního zprostředkovatele. Toto nastavení omezuje požadavky jenom na ty, které používají HTTP POST a s `application/json` typem obsahu.</li><li><code>github</code>&mdash;Funkce reaguje na [Webhooky GitHubu](https://developer.github.com/webhooks/). Nepoužívejte vlastnost _authLevel_ s Webhooky GitHubu. Další informace najdete v části Webhooky GitHubu dále v tomto článku.</li><li><code>slack</code>&mdash;Funkce reaguje na [Webhooky časové rezervy](https://api.slack.com/outgoing-webhooks). Nepoužívejte vlastnost _authLevel_ s Webhooky časové rezervy. Další informace najdete v části časová pole webhooků dále v tomto článku.</li></ul>|

## <a name="payload"></a>Délka

Vstupní typ triggeru je deklarován jako `HttpRequest` nebo vlastní typ. Pokud zvolíte `HttpRequest` , získáte úplný přístup k objektu Request. Pro vlastní typ se modul runtime pokusí analyzovat tělo požadavku JSON pro nastavení vlastností objektu.

## <a name="customize-the-http-endpoint"></a>Přizpůsobení koncového bodu HTTP

Ve výchozím nastavení platí, že když vytvoříte funkci triggeru HTTP, funkce je adresovat s cestou formuláře:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Tuto trasu můžete přizpůsobit pomocí volitelné `route` vlastnosti vstupní vazby triggeru protokolu HTTP. Například následující *function.jsv* souboru definuje `route` vlastnost triggeru http:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Pomocí této konfigurace je nyní funkce adresovat s následujícím směrováním místo původní trasy.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Tato konfigurace umožňuje, aby kód funkce podporoval dva parametry v adrese, _kategorii_ a _ID_.

# <a name="c"></a>[C#](#tab/csharp)

Pomocí parametrů můžete použít jakékoli [omezení trasy webového rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) . Následující kód funkce jazyka C# využívá oba parametry.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Pomocí parametrů můžete použít jakékoli [omezení trasy webového rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) . Následující kód funkce jazyka C# využívá oba parametry.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

V uzlu poskytuje modul runtime Functions tělo požadavku z `context` objektu. Další informace najdete v [příkladu triggeru JavaScriptu](#example).

Následující příklad ukazuje, jak číst parametry směrování z `context.bindingData` .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Kontext spuštění funkce je zveřejněn prostřednictvím parametru deklarovaného jako `func.HttpRequest` . Tato instance umožňuje funkci přistupovat k parametrům datových tras, hodnotám řetězce dotazu a metodám, které umožňují vracet odpovědi HTTP.

Po definování jsou parametry směrování k dispozici funkci voláním `route_params` metody.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Kontext spuštění funkce je vlastnosti tak, jak jsou deklarovány v `HttpTrigger` atributu. Atribut umožňuje definovat parametry směrování, úrovně autorizace, příkazy HTTP a instanci příchozího požadavku.

Parametry směrování jsou definovány prostřednictvím `HttpTrigger` atributu.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Ve výchozím nastavení jsou všechny trasy funkcí s předponou *rozhraní API*. Můžete také přizpůsobit nebo odebrat předponu pomocí `extensions.http.routePrefix` vlastnosti v [host.js](functions-host-json.md) souboru. Následující příklad odebere předponu trasy *rozhraní API* pomocí prázdného řetězce pro předponu v souboru *host.js* .

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Použití parametrů směrování

Parametry směrování, které definují vzor funkce, `route` jsou k dispozici pro každou vazbu. Například pokud máte trasu definovanou jako, `"route": "products/{id}"` vazba Table Storage může použít hodnotu `{id}` parametru v konfiguraci vazby.

Následující konfigurace ukazuje, jak `{id}` je parametr předán do vazby `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Práce s identitami klientů

Pokud vaše aplikace Function App používá [App Service ověřování/autorizaci](../app-service/overview-authentication-authorization.md), můžete zobrazit informace o ověřených klientech z vašeho kódu. Tyto informace jsou k dispozici jako [hlavičky požadavků vložené platformou](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Tyto informace můžete také přečíst z dat vazby. Tato funkce je dostupná pouze pro modul runtime Functions v 2. x a vyšší. K dispozici je také v současnosti jenom pro jazyky .NET.

# <a name="c"></a>[C#](#tab/csharp)

Informace týkající se ověřených klientů jsou k dispozici jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal je k dispozici jako součást kontextu požadavku, jak je znázorněno v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativně je možné ClaimsPrincipal jednoduše zahrnout jako další parametr v signatuře funkce:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Informace týkající se ověřených klientů jsou k dispozici jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal je k dispozici jako součást kontextu požadavku, jak je znázorněno v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativně je možné ClaimsPrincipal jednoduše zahrnout jako další parametr v signatuře funkce:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Přístupové klíče funkce

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Získání klíčů

Klíče se ukládají jako součást aplikace Function App v Azure a jsou zašifrované v klidovém stavu. Chcete-li zobrazit klíče, vytvořte nové nebo zaveďte klíče k novým hodnotám, v [Azure Portal](https://portal.azure.com) přejděte na jednu z funkcí AKTIVOVANÝch protokolem HTTP a vyberte **Spravovat**.

![Spravujte klíče funkcí na portálu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Klíče funkcí můžete získat programově pomocí [rozhraní API pro správu klíčů](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autorizace klíče rozhraní API

Většina šablon triggeru HTTP vyžaduje v žádosti klíč rozhraní API. Vaše žádost HTTP obvykle vypadá jako na následující adrese URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

Klíč lze zahrnout do proměnné řetězce dotazu s názvem `code` , jak je uvedeno výše. Může být také obsažena v `x-functions-key` hlavičce protokolu HTTP. Hodnotou klíče může být libovolný klíč funkce definovaný pro funkci nebo libovolný klíč hostitele.

Můžete povolit anonymní požadavky, které nevyžadují klíče. Můžete také vyžadovat, aby byl hlavní klíč použit. Výchozí úroveň autorizace se mění pomocí `authLevel` vlastnosti ve formátu JSON vazby. Další informace najdete v tématu [Trigger-Configuration](#configuration).

> [!NOTE]
> Při místním spouštění funkcí je autorizace zakázaná bez ohledu na zadané nastavení úrovně autorizace. Po publikování do Azure `authLevel` se vynutilo nastavení v triggeru. Klíče jsou stále vyžadovány při spuštění [místně v kontejneru](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpečení koncového bodu HTTP v produkčním prostředí

Aby bylo možné plně zabezpečit koncové body funkcí v produkčním prostředí, měli byste zvážit implementaci jedné z následujících funkcí zabezpečení na úrovni aplikace. Při použití jedné z těchto metod zabezpečení na úrovni aplikace byste měli nastavit úroveň autorizace funkce aktivované protokolem HTTP na `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Nasazení aplikace Function App v izolaci

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooky

> [!NOTE]
> Režim Webhooku je dostupný jenom pro verzi 1. x modulu runtime Functions. Tato změna byla provedena za účelem zlepšení výkonu aktivačních událostí HTTP ve verzi 2. x a vyšší.

V šablonách Webhooku verze 1. x poskytují další ověřování datových částí Webhooku. Ve verzi 2. x a vyšší stále funguje základní aktivační událost HTTP a je doporučený postup pro Webhooky. 

### <a name="github-webhooks"></a>Webhooky GitHubu

Pokud chcete reagovat na Webhooky GitHubu, nejprve vytvořte funkci pomocí triggeru HTTP a nastavte vlastnost **webHookType** na hodnotu `github` . Pak zkopírujte jeho adresu URL a klíč rozhraní API do stránky **Přidat Webhook** vašeho úložiště GitHub. 

![Snímek obrazovky, který ukazuje, jak přidat Webhook pro vaši funkci](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhooky časové rezervy

Webhook časové rezervy vygeneruje token pro místo jeho zadání, takže musíte nakonfigurovat klíč specifický pro funkci s tokenem z časové rezervy. Podívejte se na [klíče pro autorizaci](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooky a klíče

Autorizace Webhooku se zpracovává komponentou přijímače Webhooku, součástí triggeru HTTP a mechanismus se liší v závislosti na typu Webhooku. Každý mechanismus spoléhá na klíč. Ve výchozím nastavení se používá klíč funkce s názvem "výchozí". Chcete-li použít jiný klíč, nakonfigurujte poskytovatele Webhooku tak, aby odesílal název klíče s požadavkem jedním z následujících způsobů:

* **Řetězec dotazu**: Zprostředkovatel předá název klíče v `clientid` parametru řetězce dotazu, například `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **Hlavička žádosti**: Zprostředkovatel předá název klíče v `x-functions-clientid` hlavičce.

## <a name="content-types"></a>Typy obsahu

Předávání binárních a formulářových dat do funkce non-C # vyžaduje použití příslušné hlavičky Content-Type. Mezi podporované typy obsahu patří `octet-stream` binární data a [typy s více částmi](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Známé problémy

Ve funkcích, které nejsou v jazyce C, jsou požadavky odeslané s typem obsahu `image/jpeg` výsledkem `string` předané funkci. V takových případech je možné ručně převést `string` hodnotu na pole bajtů pro přístup k nezpracovaným binárním datům.

## <a name="limits"></a>Omezení

Délka požadavku HTTP je omezená na 100 MB (104 857 600 bajtů) a délka adresy URL je omezená na 4 KB (4 096 bajtů). Tato omezení jsou určena `httpRuntime` prvkem [Web.configho souboru](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)modulu runtime.

Pokud funkce, která používá Trigger HTTP, nebude dokončena během 230 sekund, vyprší časový limit [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) a vrátí chybu HTTP 502. Funkce bude pokračovat v běhu, ale nebude moci vrátit odpověď HTTP. U dlouhotrvajících funkcí doporučujeme, abyste provedli asynchronní vzorce a vraceli umístění, kde můžete testovat stav žádosti pomocí příkazového testu. Informace o tom, jak dlouho může funkce běžet, najdete v tématu [škálování a plán využití hostování](functions-scale.md#timeout).


## <a name="next-steps"></a>Další kroky

- [Vrácení odpovědi HTTP z funkce](./functions-bindings-http-webhook-output.md)
