---
title: Trvalé entity – Azure Functions
description: Přečtěte si, jaké jsou trvalé entity a jak je používat v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: d469d52a6db6c3640d07b46422ffe669a898dde8
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262992"
---
# <a name="entity-functions"></a>Funkce entit

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nástroj entity Functions spravuje stav entity explicitně, spíše než implicitně představující stav prostřednictvím toku řízení.
Entity poskytují prostředky pro horizontální navýšení kapacity aplikací tím, že distribuují práci napříč mnoha entitami, každý s mírným stavem.

> [!NOTE]
> Funkce entit a související funkce jsou dostupné jenom v Durable Functions 2,0 a novějších.

## <a name="general-concepts"></a>Obecné koncepty

Entity se chovají jako malé služby, které komunikují prostřednictvím zpráv. Každá entita má jedinečnou identitu a vnitřní stav (pokud existuje). Podobně jako služby a objekty, entity provádějí operace po zobrazení výzvy. Když se operace spustí, může aktualizovat vnitřní stav entity. Může také volat externí služby a čekat na odpověď. Entity komunikují s jinými entitami, orchestrací a klienty pomocí zpráv, které jsou implicitně odesílány prostřednictvím spolehlivých front. 

Aby se zabránilo konfliktům, budou mít všechny operace na jedné entitě zaručené spouštění na základě sériového běhu, tj. jednoho po druhém. 

### <a name="entity-id"></a>ID entity
K entitám se dostanete pomocí jedinečného identifikátoru *ID entity*. ID entity je jednoduše dvojice řetězců, které jedinečně identifikují instanci entity. Skládá se z:

* **Název entity**, což je název, který identifikuje typ entity. Příkladem je "čítač". Tento název se musí shodovat s názvem funkce entity, která implementuje entitu. Nerozlišuje se případ.
* **Klíč entity**, což je řetězec, který jedinečně identifikuje entitu mezi všemi ostatními entitami se stejným názvem. Příkladem je identifikátor GUID.

Například funkce entity `Counter` může být použita k udržení skóre v online hře. Každá instance hry má jedinečné ID entity, například `@Counter@Game1` a `@Counter@Game2`. Všechny operace, které cílí na konkrétní entitu, vyžadují zadání ID entity jako parametru.

### <a name="entity-operations"></a>Operace s entitami ###

Pokud chcete vyvolat operaci na entitě, zadejte:

* **ID entity** cílové entity
* **Název operace**, což je řetězec, který určuje operaci, která má být provedena. Entita `Counter` například může podporovat operace `add`, `get`nebo `reset`.
* **Vstup operace**, což je volitelný vstupní parametr pro operaci. Například operace přidání může jako vstup mít celočíselnou hodnotu.
* **naplánovaný čas*, což je volitelný parametr pro určení doby doručení operace. Například operace může být spolehlivě naplánována na spuštění několik dní v budoucnu.

Operace mohou vracet výslednou hodnotu nebo výsledek chyby, například Chyba JavaScriptu nebo výjimka .NET. Tento výsledek nebo chybu mohou být pozorovány orchestrací, které volaly operaci.

Operace entity může také vytvořit, číst, aktualizovat a odstranit stav entity. Stav entity je vždy trvale trvalým úložištěm.

## <a name="define-entities"></a>Definování entit

V současné době jsou dvě různá rozhraní API pro definování entit:

**Syntaxe založená na funkcích**, kde jsou entity reprezentovány jako funkce a operace jsou explicitně odesílány aplikací. Tato syntaxe funguje dobře pro entity s jednoduchým stavem, malým počtem operací nebo dynamickou sadou operací, jako v aplikačních architekturách. Tato syntaxe může být zdlouhavá, protože nezachycuje chyby typu v době kompilace.

**Syntaxe založená na třídě (pouze .NET)** , kde entity a operace jsou reprezentovány třídami a metodami. Tato syntaxe vytváří snadněji čitelný kód a umožňuje, aby operace byly vyvolány způsobem bezpečným pro typ. Syntaxe založená na třídě je tenká vrstva nad syntaxí založenou na funkci, takže obě varianty lze ve stejné aplikaci použít zaměnitelné.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Příklad: syntaxe založená na funkcích –C#

Následující kód je příkladem jednoduché `Counter` entity implementované jako trvalá funkce. Tato funkce definuje tři operace, `add`, `reset`a `get`, z nichž každý funguje v celočíselném stavu.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Další informace o syntaxi založené na funkcích a způsobu jejich použití naleznete v tématu [syntaxe založená na funkcích](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Příklad: syntaxe založená na třídě-C#

Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Stav této entity je objekt typu `Counter`, který obsahuje pole, které ukládá aktuální hodnotu čítače. Chcete-li zachovat tento objekt v úložišti, je jeho serializace a deserializována knihovnou [JSON.NET](https://www.newtonsoft.com/json) . 

Další informace o syntaxi založené na třídě a způsobu jejich použití naleznete v tématu [definování tříd entit](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Příklad: entita JavaScript

Trvalé entity jsou k dispozici v JavaScriptu počínaje verzí **1.3.0** balíčku `durable-functions` npm. Následující kód je `Counter` entita implementovaná jako trvalá funkce napsaná v JavaScriptu.

**Čítač/Function. JSON**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Čítač/index. js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

## <a name="access-entities"></a>Přístup k entitám

K entitám lze přistupovat pomocí jednosměrné nebo obousměrné komunikace. Následující terminologie rozlišuje tyto dvě formy komunikace: 

* **Volání** entity používá obousměrnou komunikaci (round-trip). Do entity odešlete zprávu o operaci a potom před pokračováním vyčkejte na zprávu s odpovědí. Zpráva odpovědi může poskytnout výslednou hodnotu nebo výsledek chyby, jako je například Chyba JavaScriptu nebo výjimka .NET. Tento výsledek nebo chyba je následně pozorován volajícím.
* **Signalizace** entity používá jednosměrnou komunikaci (požární a zapomenuté). Odešlete zprávu o operaci, ale nečekáte na odpověď. I když je zaručeno doručení zprávy, odesílatel neví, kdy a nemůže sledovat žádnou hodnotu výsledku nebo chyby.

K entitám je možné přistupovat z funkcí klienta, z funkcí nástroje Orchestrator nebo z funkcí entity. Všechny typy komunikace nejsou podporovány všemi kontexty:

* V rámci klientů můžete identifikovat entity a můžete si přečíst stav entity.
* V rámci orchestrace můžete identifikovat entity a můžete volat entity.
* V rámci entit můžete identifikovat entity.

Následující příklady znázorňují různé způsoby přístupu k entitám.

### <a name="example-client-signals-an-entity"></a>Příklad: klient signalizuje entitu.

Pro přístup k entitám z běžné funkce Azure, která je také známá jako funkce klienta, použijte [vazbu klienta entity](durable-functions-bindings.md#entity-client). Následující příklad ukazuje funkce aktivované frontou, která tuto vazbu používá.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

> [!NOTE]
> V následujících příkladech se pro přístup k entitám zobrazí následující příklady s volným typem. Obecně doporučujeme, abyste měli [přístup k entitám prostřednictvím rozhraní](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , protože poskytuje další kontrolu typu.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

Termínový *signál* znamená, že volání rozhraní API entity je jednosměrné a asynchronní. Pro funkci klienta není možné zjistit, kdy entita tuto operaci zpracovala. Funkce klienta navíc nemůže sledovat žádné hodnoty výsledků ani výjimky. 

### <a name="example-client-reads-an-entity-state"></a>Příklad: klient přečte stav entity.

Funkce klienta se také mohou dotazovat na stav entity, jak je znázorněno v následujícím příkladu:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await context.df.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Dotazy na stav entity se odesílají do trvalého úložiště sledování a vracejí poslední trvalý stav entity. Tento stav je vždy stav "potvrzený", to znamená, že během provádění operace se nepředpokládá dočasný přechodný stav. Je ale možné, že je tento stav zastaralý ve srovnání se stavem v paměti entity. Pouze orchestrace mohou číst stav v paměti entity, jak je popsáno v následující části.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Příklad: signály Orchestrace a volání entity

Funkce Orchestrator mají přístup k entitám pomocí rozhraní API ve [vazbě triggeru orchestrace](durable-functions-bindings.md#orchestration-trigger). Následující příklad kódu ukazuje volání funkce Orchestrator a signalizaci `Counter` entitu.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript v současné době nepodporuje signalizaci entity z nástroje Orchestrator. Místo toho použijte `callEntity`.

---

Pouze orchestrace jsou schopny volat entity a získat odpověď, což může být buď návratová hodnota, nebo výjimka. Funkce klienta, které používají [vazbu klienta](durable-functions-bindings.md#entity-client) , mohou signalizovat pouze entity.

> [!NOTE]
> Volání entity z funkce Orchestrator je podobné volání [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) z funkce Orchestrator. Hlavním rozdílem je, že funkce entit jsou trvalé objekty s adresou, což je ID entity. Funkce entit podporují zadání názvu operace. Funkce aktivit na druhé straně jsou bezstavové a nemají koncept operací.

### <a name="example-entity-signals-an-entity"></a>Příklad: entita signalizuje entitu.

Funkce entity může posílat signály jiným entitám nebo dokonce sám sebe, zatímco provádí operaci.
Můžete například upravit předchozí příklad entity `Counter` tak, aby pošle signál "s milníkem" na určitou entitu monitorování, když čítač dosáhne hodnoty 100.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

---

## <a name="entity-coordination"></a>Koordinace entit (aktuálně jenom .NET)

Může nastat situace, kdy potřebujete koordinovat operace mezi několika entitami. Například v bankovní aplikaci můžete mít entity, které reprezentují jednotlivé bankovní účty. Při přenosu finančních prostředků z jednoho účtu na jiný je nutné zajistit, aby měl zdrojový účet dostatečné prostředky. Také je nutné zajistit, aby se aktualizace zdrojového i cílového účtu prováděly v rámci reakčního konzistentního postupu.

### <a name="example-transfer-funds-c"></a>Příklad: převodní prostředky (C#)

Následující příklad kódu přenáší prostředky mezi dvěma entitami účtu pomocí funkce Orchestrator. Koordinace aktualizací entit vyžaduje použití metody `LockAsync` k vytvoření _kritické části_ v orchestraci.

> [!NOTE]
> Pro zjednodušení tento příklad znovu používá dříve definovanou entitu `Counter`. V reálné aplikaci by bylo lepší definovat podrobnější `BankAccount` entitu.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

V rozhraní .NET `LockAsync` vrátí `IDisposable`, které po vyřazení ukončí oddíl kritické. Tento `IDisposable` výsledek lze použít společně s blokem `using` pro získání syntaktické reprezentace důležité části.

V předchozím příkladu funkce Orchestrator přenesla prostředky ze zdrojové entity na cílovou entitu. Metoda `LockAsync` uzamkl entity zdrojového i cílového účtu. Toto uzamykání zajišťuje, že žádný jiný klient by nemohl zadat dotaz nebo změnit stav obou účtů, dokud logika orchestrace neukončila kritickou část na konci `using`ho příkazu. Toto chování brání v tom, aby bylo možné přečerpání ze zdrojového účtu.

> [!NOTE] 
> Pokud se orchestrace ukončí, ať už normálně nebo s chybou, všechny kritické oddíly v průběhu budou implicitně ukončeny a všechny zámky se uvolní.

### <a name="critical-section-behavior"></a>Kritické chování oddílu

Metoda `LockAsync` vytvoří kritickou část v orchestraci. Tyto kritické oddíly zabraňují jiným orchestraci v provádění překrývajících se změn v zadané sadě entit. Interně rozhraní `LockAsync` API pošle operace "Lock" do entit a vrátí, když obdrží zprávu s odpovědí "zámek" z každé z těchto stejných entit. Zámky i odemknutí jsou integrované operace podporované všemi entitami.

V entitě nejsou povoleny žádné operace, pokud je v uzamčeném stavu. Toto chování zajistí, že entita může současně uzamknout pouze jedna instance orchestrace. Pokud se volající pokusí vyvolat operaci u entity, když je uzamčena orchestrací, tato operace je umístěna do fronty čekajících operací. Žádné nedokončené operace se zpracovávají do doby, než orchestrace hospodářství uvolní zámek.

> [!NOTE] 
> Toto chování se mírně liší od primitiv synchronizace používaných ve většině programovacích jazyků, jako je například příkaz `lock` v C#. Například v C#příkazu musí být příkaz `lock` používán všemi vlákny k zajištění správné synchronizace napříč více vlákny. Entity ale nevyžadují, aby všichni volající explicitně zamkli entitu. Pokud nějaký volající zamkne entitu, všechny ostatní operace v této entitě se zablokují a zařadí do fronty za tímto zámkem.

Zámky entit jsou odolné, takže zůstanou i v případě, že je spuštěný proces recyklován. Zámky jsou interně trvale zachované jako součást trvalého stavu entity.

Na rozdíl od transakcí nekritické oddíly automaticky vrátí zpět změny v případě chyb. Místo toho musí být jakékoli zpracování chyb, jako je vrácení zpět nebo opakování, explicitně kódované, například zachycením chyb nebo výjimek. Tato volba návrhu je úmyslné. Automatické vrácení všech důsledků orchestrace je obtížné nebo nemožné, protože orchestrace můžou spouštět aktivity a volat externí služby, které se nedají vrátit zpátky. Také se pokusy o vrácení zpět mohou selhat a vyžadují další zpracování chyb.

### <a name="critical-section-rules"></a>Kritická pravidla oddílů

Na rozdíl od primitivních zámků nízké úrovně ve většině programovacích jazyků jsou důležité oddíly *zaručené zablokování*. Chcete-li zabránit zablokování, vynutili následující omezení: 

* Kritické oddíly nemůžou být vnořené.
* Kritické oddíly nemůžou vytvářet podorchestry.
* Kritické oddíly mohou volat pouze entity, které byly uzamčeny.
* Kritické oddíly nemohou volat stejnou entitu pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze Neuzamčené entity.

Jakékoli porušení těchto pravidel způsobí chybu za běhu, jako je například `LockingRulesViolationException` v rozhraní .NET, která obsahuje zprávu s vysvětlením, jaké pravidlo bylo přerušeno.

## <a name="comparison-with-virtual-actors"></a>Porovnání s virtuálními aktéry

Mnohé z funkcí trvalé entity nechte inspirovat [model actor](https://en.wikipedia.org/wiki/Actor_model). Pokud jste již obeznámeni s objekty Actors, můžete rozpoznat mnoho konceptů popsaných v tomto článku. Trvalé entity jsou obzvláště podobné [virtuálním aktérům](https://research.microsoft.com/projects/orleans/)nebo zrnam, jak je oblíbená v rámci [projektu Orleans](http://dotnet.github.io/orleans/). Například:

* Trvalé entity jsou adresovatelné prostřednictvím ID entity.
* Trvalé operace s entitami se v jednom okamžiku spouštějí po jednom, aby se zabránilo konfliktům časování.
* Trvalé entity jsou vytvořeny implicitně při volání nebo signalizaci.
* Neprovádíte-li operace, odolné entity budou tiše odpojeny od paměti.

Je potřeba zaznamenat si několik důležitých rozdílů:

* Odolné entity mají přednost před latencí a nemusí být vhodná pro aplikace s přísnými požadavky na latenci.
* Trvalé entity nemají předdefinované časové limity pro zprávy. V Orleans vyprší po konfigurovatelné době všechny zprávy. Výchozí hodnota je 30 sekund.
* Zprávy odesílané mezi entitami jsou spolehlivě doručovány a v daném pořadí. V Orleans je podporováno spolehlivé nebo seřazené doručování obsahu odesílaného prostřednictvím datových proudů, ale není zaručeno pro všechny zprávy mezi zrna.
* Vzory odpovědí na požadavky v entitách jsou omezené na orchestraci. V rámci entit je povoleno pouze jednosměrné zasílání zpráv (označované také jako signalizace), jako v původním modelu actor a na rozdíl od zrn v Orleans. 
* Trvalé entity se nezablokují. V Orleans může docházet k zablokování a neřešit, dokud nevyprší časový limit zprávy.
* Odolné entity lze použít ve spojení s trvalými orchestrací a podporují mechanismy distribuovaného zamykání. 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si příručku pro vývojáře k trvalým entitám v .NET.](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Další informace o centrech úloh](durable-functions-task-hubs.md)
