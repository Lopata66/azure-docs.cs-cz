---
title: Referenční informace pro vývojáře jazyka JavaScript pro službu Azure Functions | Dokumentace Microsoftu
description: Naučte se vyvíjet funkce pomocí jazyka JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: a8ee92d117a416d638f62b573dfb155f67bf66e0
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983134"
---
# <a name="azure-functions-javascript-developer-guide"></a>Příručka pro vývojáře Azure Functions JavaScript

Prostředí jazyka JavaScript pro službu Azure Functions umožňuje snadno exportovat funkce, která je předána jako `context` objektu pro komunikaci s modulem runtime a pro příjem a odesílání dat prostřednictvím vazeb.

Tento článek předpokládá, že jste už čtete [referenční informace pro vývojáře Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Funkce exportu
Každá funkce JavaScript, musíte exportovat jeden `function` prostřednictvím `module.exports` pro modul runtime vyhledat funkci a spustit ho. Tuto funkci nutné vždy provést `context` objekt jako první parametr.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
// You can also use 'arguments' to dynamically handle inputs
module.exports = function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
    context.done();
};
```

Trigger a vstupní vazby (vazby `direction === "in"`) lze předat jako parametry funkce. Jsou předávány do funkce ve stejném pořadí, ve kterém jsou definovány v *function.json*. Můžete dynamicky zpracovávat vstupy pomocí jazyka JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objektu. Pokud máte například `function(context, a, b)` a změňte ho na `function(context, a)`, stále můžete získat hodnotu `b` v kódu funkce rekapitulací `arguments[2]`.

Všechny vazby, bez ohledu na směru, jsou také předají `context` pomocí `context.bindings` vlastnost.

## <a name="context-object"></a>objekt kontextu
Modul runtime používá `context` objekt k předávání dat do a z vaší funkce a umožnit vám komunikovat s modulem runtime.

`context` Objekt je vždy první parametr funkce a musí být zahrnut, protože obsahuje metody, jako `context.done` a `context.log`, které jsou nutné k využití modulu runtime správně. Můžete pojmenovat objekt cokoli, co byste chtěli (například `ctx` nebo `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
    context.done();
};
```

### <a name="contextbindings-property"></a>Vlastnost Context.Bindings

```
context.bindings
```
Vrátí objekt s názvem, který obsahuje všechny vstupní a výstupní data. Například následující definice vazby ve vaší *function.json* umožňuje přístup k obsahu z fronty z `context.bindings.myInput` a přiřaďte výstupy do fronty pomocí `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Všimněte si, že můžete také definujte výstupní vazby dat pomocí `context.done` metoda místo `context.binding` objektu (viz níže).

### <a name="contextbindingdata-property"></a>Vlastnost context.bindingData

```
context.bindingData
```
Vrátí objekt s názvem, který obsahuje data volání triggeru metadata a funkce (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Například aktivační událost metadat najdete v tomto [služby event hubs – příklad](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context.Done – metoda
```
context.done([err],[propertyBag])
```

Informuje o modulu runtime, který váš kód bylo dokončeno. Pokud funkce používá jazyk JavaScript [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarace (k dispozici prostřednictvím uzlu 8 + funkce verze 2.x), není potřeba použít `context.done()`. `context.done` Zpětného volání je implicitně volána.

Pokud funkce není asynchronní funkci **musí volat `context.done`**  informovat modul runtime dokončení vaší funkce. Provedení příkazu vyprší časový limit, pokud není nalezena.

`context.done` Metoda umožňuje předat zpět oba uživatelem definované chybové modul runtime a objekt JSON obsahující výstupní vazbu data. Předat vlastnosti `context.done` přepíše nic nastavit `context.bindings` objektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context.log – metoda  

```
context.log(message)
```
Umožňuje zapisovat do protokolů streamování funkce na výchozí úrovni trasování. Na `context.log`, další protokolování metody jsou k dispozici, které vám umožňují zapisovat protokoly funkce na jiných úrovních trasování:


| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **Chyba (_zpráva_)**   | Zapíše chyba úroveň protokolování nebo nižší.   |
| **upozornění (_zpráva_)**    | Zapíše do protokolování nebo nižší úroveň pro upozornění. |
| **informace o (_zpráva_)**    | Zapíše informace o úroveň protokolování nebo nižší.    |
| **verbose (_zpráva_)** | Zápisy na podrobné úrovni protokolování.           |

Následující příklad zapíše protokolu na úroveň trasování varování:

```javascript
context.log.warn("Something has happened."); 
```
Je možné [nakonfigurovat prahové hodnoty úroveň trasování pro protokolování](#configure-the-trace-level-for-console-logging) host.json souboru. Další informace o psaní protokolů, najdete v části [zápis trasování výstupy](#writing-trace-output-to-the-console) níže.

Čtení [monitorování Azure Functions](functions-monitoring.md) získat další informace o zobrazení a dotazování protokolů funkce.

## <a name="binding-data-type"></a>Datový typ vazby

Chcete-li definovat datový typ pro vstupní vazby, použijte `dataType` vlastnost v definici vazby. Například pokud chcete číst obsah požadavku HTTP v binárním formátu, použijte typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Možnosti pro `dataType` jsou: `binary`, `stream`, a `string`.

## <a name="writing-trace-output-to-the-console"></a>Zápisu výstupu sledování do konzoly 

Ve službě Functions použijete `context.log` metody zapsat výstup trasování do konzoly. Funkce v1.x, nemůžete použít `console.log` k zápisu do konzoly. V v2.x funkce trasování výstupy prostřednictvím `console.log` jsou zachyceny na úrovni aplikace Function App. To znamená, že výstupem z `console.log` nejsou vázané na volání určité funkce.

Při volání `context.log()`, vaše zapíše se do konzoly na výchozí úrovni trasování, který je _informace_ úroveň trasování. Následující kód, zapíše do konzoly na úroveň trasování informace:

```javascript
context.log({hello: 'world'});  
```

Tento kód je ekvivalentní k výše uvedený kód:

```javascript
context.log.info({hello: 'world'});  
```

Tento kód se zapíše do konzoly na úrovni Chyba:

```javascript
context.log.error("An error has occurred.");  
```

Protože _chyba_ je trasování nejvyšší úrovně, toto trasování bude zapsáno do výstupu na všech úrovních trasování tak dlouho, dokud je povoleno protokolování.

Všechny `context.log` metody podporují stejný formát parametru, který podporuje na Node.js [util.format metoda](https://nodejs.org/api/util.html#util_util_format_format). Vezměte v úvahu následující kód, který zapisuje protokoly funkce s použitím výchozí úroveň trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Můžete také napsat stejný kód v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Nakonfiguruje úroveň trasování pro protokolování konzoly

Funkce umožňuje definovat prahové hodnoty úroveň trasování pro zápis do konzoly, která usnadňuje ovládací prvek, který způsob trasování jsou zapsány do konzoly z vašich funkcí. Chcete-li nastavení prahové hodnoty pro všechna trasování zapsána do konzoly, použijte `tracing.consoleLevel` vlastnost v souboru host.json. Toto nastavení platí pro všechny funkce v aplikaci function app. Následující příklad nastaví prahovou hodnotu trasování Zapnutí podrobného protokolování:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Hodnoty **consoleLevel** odpovídají názvům `context.log` metody. Chcete-li zakázat všechny protokolování trasování do konzoly, nastavte **consoleLevel** k _vypnout_. Další informace najdete v tématu [referenční materiály k host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP aktivačními událostmi a vazbami

HTTP a triggerů webhooků a HTTP výstupní vazby pomocí žádostí a odpovědí objekty představují zasílání zpráv protokolu HTTP.  

### <a name="request-object"></a>Objekt žádosti

`context.req` (Požadavek) má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _Text_        | Objekt, který obsahuje text žádosti.               |
| _Záhlaví_     | Objekt, který obsahuje hlavičky požadavku.                   |
| _– Metoda_      | Metoda HTTP požadavku.                                |
| _PůvodníAdresaURL_ | Adresa URL požadavku.                                        |
| _params_      | Objekt, který obsahuje směrování parametry požadavku. |
| _Dotaz_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Tělo zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

`context.res` (Odpověď) má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _Text_    | Objekt, který obsahuje text odpovědi.         |
| _Záhlaví_ | Objekt, který obsahuje hlavičky odpovědi.             |
| _isRaw_   | Označuje, že formátování se přeskočí pro odpověď.    |
| _Stav_  | Stavový kód HTTP odpovědi.                     |

### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi 

Při práci s triggerů HTTP, můžete přístup k objektům HTTP požadavků a odpovědí v několika způsoby:

+ Z `req` a `res` vlastnosti `context` objektu. Tímto způsobem můžete použít konvenční vzor k datům přístup protokolu HTTP z objektu context, místo nutnosti použít úplnou `context.bindings.name` vzor. Následující příklad ukazuje, jak získat přístup k `req` a `res` objektů `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Z pojmenované vstupní a výstupní vazby. Tímto způsobem triggeru HTTP a vazby fungují stejně jako všechny vazby. Následující příklad nastaví objektu odpovědi pomocí pojmenovaná `response` vazby: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[Pouze odpovědi]_  Voláním `context.res.send(body?: any)`. Je vytvořen odpověď HTTP se vstupem `body` jako text odpovědi. `context.done()` je implicitně volána.

+ _[Pouze odpovědi]_  Voláním `context.done()`. Zvláštní druh vazby HTTP vrátí odpověď, která je předána `context.done()` metody. Následující HTTP výstupní vazby definuje `$return` výstupní parametr:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Uzel verze

V následující tabulce jsou uvedeny verze Node.js používá každá hlavní verze modul runtime služby Functions:

| Verze funkcí | Verze Node.js | 
|---|---|
| 1.x | 6.11.2 (uzamčeno modulem runtime) |
| 2.x  | _Aktivní LTS_ a _aktuální_ verze Node.js (8.11.1 a 10.6.0 doporučeno). Nastavení verze s použitím WEBSITE_NODE_DEFAULT_VERSION [nastavení aplikace, které](functions-how-to-use-azure-function-app-settings.md#settings).|

Zobrazí aktuální verzi, která používá modul runtime s tiskem `process.version` z jakékoli funkce.

## <a name="package-management"></a>Správa balíčků
Následující kroky umožňují zahrnout balíčky ve vaší aplikaci funkcí: 

1. Přejděte do části `https://<function_app_name>.scm.azurewebsites.net` (Soubor > Nový > Jiné).

2. Klikněte na tlačítko **konzole ladění** > **CMD**.

3. Přejděte na `D:\home\site\wwwroot`a pak přetáhněte soubor package.json **wwwroot** složky v horní polovině stránky.  
    Soubory můžete nahrát také aplikaci function App jinými způsoby. Další informace najdete v tématu [aktualizace souborů aplikace funkce](functions-reference.md#fileupdate). 

4. Po nahrání souboru package.json, spusťte `npm install` v příkaz **vzdálené spuštění konzoly Kudu**.  
    Tato akce stáhne balíčky uvedené v souboru package.json a restartuje aplikaci function app.

Po instalaci balíčky, které potřebujete, můžete je importovat do funkce voláním `require('packagename')`, jako v následujícím příkladu:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Byste měli definovat `package.json` souboru v kořenovém adresáři aplikace function App. Definování souboru umožňuje všechny funkce v aplikaci sdílet stejné balíčky uložené v mezipaměti, která poskytuje nejlepší výkon. Pokud nastane konflikt verzí ho mohli vyřešit tak, že přidáte `package.json` soubor do složky na konkrétní funkce.  

## <a name="environment-variables"></a>Proměnné prostředí
Chcete-li získat proměnnou prostředí nebo nastavení hodnoty aplikace, použijte `process.env`, jak je znázorněno zde `GetEnvironmentVariable` funkce:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>Důležité informace pro funkce jazyka JavaScript

Při práci s funkcí jazyka JavaScript, mějte na paměti aspekty uvedené v následujících dvou částech.

### <a name="choose-single-vcpu-app-service-plans"></a>Zvolte jeden virtuální procesor plány služby App Service

Když vytvoříte aplikaci function app, který používá plán služby App Service, doporučujeme, že vyberete jeden virtuální procesor plán spíše než plán s více virtuálních procesorů. V současné době funkce jazyka JavaScript funkce efektivněji běží na virtuálních počítačích s jedním virtuálním procesorem a pomocí větší virtuální počítače nevytvoří vylepšení očekávaný výkon. Pokud je to nezbytné, můžete ručně škálovat přidáváním dalších instancí virtuálních počítačů na jeden virtuální procesor, nebo můžete povolit automatické škálování. Další informace najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Podpora TypeScript a CoffeeScript
Protože přímou podporu ještě neexistuje pro automatické kompilaci TypeScript nebo CoffeeScript prostřednictvím modulu runtime, musí být zpracována mimo modul runtime v době nasazení těchto podpory. 

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)

