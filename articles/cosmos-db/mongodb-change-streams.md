---
title: Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se používat rozhraní Change Streams n Azure Cosmos DB API pro MongoDB k získání změn provedených ve vašich datech.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: srchi
ms.openlocfilehash: 4b159ef897185dc0c886b525e5fdf38cd919b8cc
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465707"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB

Podpora [kanálů změn](change-feed.md) v rozhraní Azure Cosmos DB API pro MongoDB je k dispozici prostřednictvím rozhraní API Change Streams. Pomocí rozhraní Change Streams API můžou vaše aplikace získat změny provedené v kolekci nebo na položky v jednom horizontálních oddílů. Později můžete na základě výsledků provádět další akce. Změny položek v kolekci jsou zachyceny v pořadí podle doby jejich úpravy a je zaručeno pořadí řazení podle horizontálních oddílů klíče.

> [!NOTE]
> Chcete-li použít změnu datových proudů, vytvořte účet s verzí 3,6 rozhraní API Azure Cosmos DB pro MongoDB nebo novější verzi. Pokud spustíte příklady pro Stream změn v předchozí verzi, může se zobrazit `Unrecognized pipeline stage name: $changeStream` Chyba.

## <a name="current-limitations"></a>Aktuální omezení

Při použití datových proudů změn platí následující omezení:

* `operationType`Vlastnosti a `updateDescription` se zatím nepodporují ve výstupním dokumentu.
* `insert` `update` Typy operací, a `replace` jsou aktuálně podporovány. 
* Operace odstranění nebo jiné události ještě nejsou podporované.

V důsledku těchto omezení jsou vyžadovány $match fáze, $project fáze a možnosti fullDocument, jak je znázorněno v předchozích příkladech.

Na rozdíl od kanálu změn v rozhraní SQL API Azure Cosmos DB není k dispozici samostatná [Knihovna pro změny kanálu](change-feed-processor.md) , která by mohla využívat datové proudy změn, nebo vyžaduje kontejner zapůjčení. V současné době není podporovaná podpora pro [Azure Functions triggery](change-feed-functions.md) pro zpracování datových proudů změn.

## <a name="error-handling"></a>Zpracování chyb

Při použití datových proudů změn jsou podporovány následující chybové kódy a zprávy:

* **Kód chyby HTTP 16500** – Pokud je datový proud změny omezený, vrátí prázdnou stránku.

* **NamespaceNotFound (typem operace OperationType unvalidate)** – Pokud spustíte datový proud změn v kolekci, která neexistuje, nebo pokud je kolekce vyřazena, `NamespaceNotFound` vrátí se chyba. Vzhledem k tomu, že `operationType` vlastnost nemůže být vrácena ve výstupním dokumentu, místo `operationType Invalidate` chyby se `NamespaceNotFound` vrátí chyba.

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak získat datové proudy změn pro všechny položky v kolekci. Tento příklad vytvoří kurzor pro sledování položek při jejich vložení, aktualizaci nebo nahrazení. `$match` `$project` `fullDocument` K získání datových proudů změn jsou vyžadovány fáze, fáze a možnosti. Sledování operací odstranění pomocí datových proudů není aktuálně podporováno. Jako alternativní řešení můžete přidat měkké označení pro položky, které se odstraňují. Můžete například přidat atribut v položce s názvem "odstraněno". Pokud chcete položku odstranit, můžete nastavit hodnotu "odstraněno" na `true` a nastavit hodnotu TTL pro položku. Vzhledem k tomu, že aktualizace "Deleted" na `true` aktualizaci je aktualizace, tato změna se zobrazí v datovém proudu změn.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```
# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Změny v rámci jednoho horizontálních oddílů

Následující příklad ukazuje, jak získat změny položek v rámci jednoho horizontálních oddílů. Tento příklad načte změny položek, které mají klíč horizontálních oddílů se rovná "a" a hodnotu klíče horizontálních oddílů rovnající se 1. Je možné, že různé klienty čtou změny z různých horizontálních oddílů paralelně.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuální omezení

Při použití datových proudů změn platí následující omezení:

* `operationType`Vlastnosti a `updateDescription` se zatím nepodporují ve výstupním dokumentu.
* `insert` `update` Typy operací, a `replace` jsou aktuálně podporovány. Operace odstranění nebo jiné události ještě nejsou podporované.

V důsledku těchto omezení jsou vyžadovány $match fáze, $project fáze a možnosti fullDocument, jak je znázorněno v předchozích příkladech.

## <a name="error-handling"></a>Zpracování chyb

Při použití datových proudů změn jsou podporovány následující chybové kódy a zprávy:

* **Kód chyby HTTP 429** – Pokud je datový proud změny omezený, vrátí prázdnou stránku.

* **NamespaceNotFound (typem operace OperationType unvalidate)** – Pokud spustíte datový proud změn v kolekci, která neexistuje, nebo pokud je kolekce vyřazena, `NamespaceNotFound` vrátí se chyba. Vzhledem k tomu, že `operationType` vlastnost nemůže být vrácena ve výstupním dokumentu, místo `operationType Invalidate` chyby se `NamespaceNotFound` vrátí chyba.

## <a name="next-steps"></a>Další kroky

* [Využijte čas k automatickému vypršení platnosti dat v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-time-to-live.md)
* [Indexování v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-indexing.md)