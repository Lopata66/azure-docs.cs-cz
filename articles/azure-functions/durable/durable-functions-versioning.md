---
title: Správa verzí v Durable Functions – Azure
description: Zjistěte, jak k implementaci správy verzí v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 33ca6c36cd11d53a3c50a8374181c511fd2f8c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648128"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Správa verzí v Durable Functions (Azure Functions)

Je nevyhnutelné, že funkce bude se přidal, odstranil a mění v průběhu životního cyklu aplikace. [Odolná služba Functions](durable-functions-overview.md) umožňuje řetězení takovým způsobem, který nebylo dříve možné dohromady funguje, a toto zřetězení ovlivňuje, jak můžete zpracovávat správy verzí.

## <a name="how-to-handle-breaking-changes"></a>Jak zpracovat změny způsobující chyby

Existuje několik příkladů rozbíjející změny mít na paměti. Tento článek popisuje nejběžnější ty. Hlavní motiv za všechny z nich je, oba Orchestrace nové i stávající funkce jsou ovlivněny změnami kódu funkce.

### <a name="changing-activity-function-signatures"></a>Změna signatury funkce aktivity

Změna podpisu odkazuje na změnu názvu, vstup nebo výstup z funkce. Pokud je funkce aktivity tento druh změny, to by mohlo narušit funkce orchestrátoru, který na něm závisí. Pokud aktualizujete funkce orchestrátoru přizpůsobil této změně, by mohlo poškodit existující instance vydávaných za pochodu.

Jako příklad předpokládejme, že máme následující funkce.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato funkce zjednodušenou přijímá výsledky **Foo** a předává jej do **panelu**. Předpokládejme, že chcete změnit návratový typ **Foo** z `bool` k `int` pro podporu více různých výsledné hodnoty. Výsledek vypadá takto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato změna funguje pro všechny nové instance funkce orchestrátoru, ale žádné vydávaných za pochodu instance přestane fungovat. Například vezměte si situaci, kde instance Orchestrace volá **Foo**, získá zpět logickou hodnotu a potom kontrolní body. Pokud změna podpisu je nasazena v tuto chvíli, byl vytvořen kontrolní bod instance selže okamžitě, když bude pokračovat a přehrává volání `context.CallActivityAsync<int>("Foo")`. Důvodem je, že je výsledek v tabulce historie `bool` ale nový kód se pokusí rekonstruovat do `int`.

Toto je pouze jedna z mnoha různými způsoby, změna podpisu může dojít k narušení existující instance. Obecně platí Pokud orchestrátoru je potřeba změnit způsob, jakým volá funkci, pak tato změna by mohla způsobovat problémy.

### <a name="changing-orchestrator-logic"></a>Změna logiky nástroje orchestrator

Jiné třídy problémy se správou verzí pocházet od změny kódu funkce produktu orchestrator tak, aby mate Logika opakování pro vydávaných za pochodu instancí.

Vezměte v úvahu následující funkce nástroje orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nyní předpokládejme, že chcete přidat další volání funkce zdánlivě nevinnosti změny.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Tato změna přidá nové volání funkce **SendNotification** mezi **Foo** a **panelu**. Neexistují žádné změny podpis. Vznikne problém, když existující instanci návratu z volání **panelu**. Během opětovného přehrání, pokud původní volání **Foo** vrátil `true`, pak zavolá opakování orchestrator **SendNotification** nenacházející se v historii spuštění. V důsledku toho rozhraní trvalý úlohy nezdaří a zobrazí se `NonDeterministicOrchestrationException` protože došlo ke volání **SendNotification** očekávaného zobrazíte volání **panelu**.

## <a name="mitigation-strategies"></a>Strategie omezení rizik

Tady jsou některé strategie pro řešení výzev Správa verzí:

* Neprovádět žádnou akci
* Zastavte všechny instance vydávaných za pochodu
* Nasazení vedle sebe

### <a name="do-nothing"></a>Neprovádět žádnou akci

Nejjednodušší způsob, jak zpracovat k zásadní změně je nechat vydávaných za pochodu Orchestrace selhat instance. Změněná kódová předchozí spuštění nové instance.

Ať už se jedná o problém závisí na důležitost instancím vydávaných za pochodu. Pokud jsou v aktivním vývoji a o vydávaných za pochodu instancí, může to být dostatečné. Musíte však výjimky a chyby v svůj kanál diagnostiku. Pokud chcete, aby se zabránilo tyto věci, zvažte další možnosti správy verzí.

### <a name="stop-all-in-flight-instances"></a>Zastavte všechny instance vydávaných za pochodu

Další možností je ukončit všechny instance vydávaných za pochodu. To můžete udělat zrušením obsah vnitřní **řízení fronty** a **pracovní položky fronty** fronty. Instance se navždy zablokuje, kde jsou, ale nebude dál sbližuje tyto telemetrická data pomocí zprávy o neúspěchu. To je ideální v prototypu rychlý vývoj.

> [!WARNING]
> Podrobnosti o tyto fronty může změnit v čase, takže nemusíte spoléhat na tuto techniku pro produkční úlohy.

### <a name="side-by-side-deployments"></a>Nasazení vedle sebe

Většina testování převzetí služeb při způsobem zajistit, že změny způsobující chyby nasazených bezpečně je jejich nasazení – souběžně se staršími verzemi. To lze provést pomocí kteréhokoli z následujících postupů:

* Nasazení všech aktualizací jako zcela nových funkcí (nové názvy).
* Všechny aktualizace nasaďte jako novou aplikaci function app s jiným účtem úložiště.
* Nasazení nové kopie aplikace function app, ale s aktualizovaným `TaskHub` název. Toto je doporučený postup.

### <a name="how-to-change-task-hub-name"></a>Změna názvu centra úloh

Centra úloh se dá nakonfigurovat v *host.json* to následujícím způsobem:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

Výchozí hodnota je `DurableFunctionsHub`.

Všechny entity služby Azure Storage jsou pojmenovány podle `HubName` hodnota konfigurace. Tím, že nový název centra úloh, zajistíte tím, že se vytvoří samostatné fronty a tabulky historie pro novou verzi vaší aplikace.

Doporučujeme nasadit novou verzi aplikace function app na nový [Slot nasazení](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Sloty nasazení umožňují provozovat několik kopií vašich funkce aplikace na straně po boku s pouze jedním z nich jako aktivní *produkční* slot. Až budete připravení ke zveřejnění nové logice Orchestrace vaší stávající infrastruktuře, může být stejně snadné jako přechodem na novou verzi do produkčního slotu.

> [!NOTE]
> Tato strategie je nejvhodnější při použití protokolu HTTP a webhookové aktivační události pro funkce nástroje orchestrator. Aktivace jiným protokolem než HTTP, třeba fronty a Event Hubs, by měl definice aktivační události [jsou odvozeny z nastavení aplikace](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , která aktualizuje jako součást operace prohození.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovat výkonu a škálování problémy](durable-functions-perf-and-scale.md)
