---
title: Příklady pro Azure Cosmos DB Node. js
description: Na GitHubu najdete příklady v Node.js pro běžné úlohy ve službě Azure Cosmos DB, včetně operací CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: dech
ms.openlocfilehash: 640f6a12f3df1bb8ded69c66eda3b307e6bdb74c
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384878"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Příklady v Node.js pro Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Příklady v .NET](sql-api-dotnet-samples.md)
> * [Příklady v Javě](sql-api-java-samples.md)
> * [Příklady v asynchronní Javě](sql-api-async-java-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Ukázková řešení, která provádí operace CRUD a další běžné operace s prostředky služby Azure Cosmos DB, jsou součástí úložiště [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) na GitHubu. Tento článek obsahuje:

* Odkazy na úlohy v jednotlivých ukázkových souborech projektů v Node.js.
* Odkazy na související referenční obsah rozhraní API

**Požadavky**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Můžete [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Vaše předplatné sady Visual Studio vám každý měsíc dává kredity, které můžete použít pro placené služby Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potřebujete také sadu [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Každá ukázka je samostatná, sama se nastaví a po ukončení příkladu vyčistí svoje prostředky. Ukázky provedou několik volání metody [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). Pokaždé, když k tomu dojde, se bude pro vaše předplatné účtovat 1 hodina využití pro úroveň výkonu vytvářeného kontejneru.
   > 
   > 

## <a name="database-examples"></a>Příklady pro databáze
Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) projektu [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) ukazuje, jak provádět následující úlohy. Další informace o databázích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu. 

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření databáze, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Výpis databází pro účet](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Čtení databáze podle ID](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |
| [Odstranění databáze](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |

## <a name="container-examples"></a>Příklady pro kontejnery
Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) projektu [ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) ukazuje, jak provádět následující úlohy. Informace o kolekcích Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu. 

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření kontejneru, pokud ještě neexistuje](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Výpis kontejnerů pro účet](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Čtení kolekce podle ID](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |
| [Odstranění kontejneru](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |

## <a name="item-examples"></a>Příklady pro položky
Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) projektu [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) ukazuje, jak provádět následující úlohy. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](databases-containers-items.md) konceptu. 

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření položek](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Čtení všech položek v kontejneru](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Čtení položky podle ID](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Čtení položky pouze v případě, že se změnila](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Dotazování dokumentů](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Nahrazení položky](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Nahrazení položky pomocí podmíněné kontroly ETag](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Odstranění položky](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Příklady indexování
Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) projektu [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) ukazuje, jak provádět následující úlohy.  Další informace o indexování v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [zásadách indexování](index-policy.md), [typech indexování](index-types.md)a [indexovaných cestách](index-paths.md) . 

| Úloha | API – referenční informace |
| --- | --- |
| [Ruční indexování konkrétního dokumentu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Ruční vyloučení konkrétního dokumentu z indexu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Použití opožděného indexování pro hromadný import nebo čtení velkých kontejnerů](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Vyloučení cesty z indexu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Povolení prohledávání v cestě řetězce během operace rozsahu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Vytvoření indexu rozsahu v cestě řetězce](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Vytvoření kontejneru s výchozí zásadou indexPolicy a její následná online aktualizace](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Příklady programování na straně serveru
Soubor [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) projektu [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) ukazuje, jak provádět následující úlohy. Další informace o programování na straně serveru v Azure Cosmos DB před spuštěním následujících ukázek najdete v článku o [uložených procedurách, triggerech a uživatelsky definovaných funkcích](stored-procedures-triggers-udfs.md) koncepční článek. 

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Spuštění uložené procedury](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Další informace o programování na straně serveru najdete v tématu [Azure Cosmos DB programování na straně serveru: Uložené procedury, triggery databáze a UDF](stored-procedures-triggers-udfs.md).

