---
title: Jak provádět dotazy na data grafu ve službě Azure Cosmos DB?
description: Zjistěte, jak zadat dotaz na data grafu z Azure Cosmos DB pomocí gremlinových dotazů
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.openlocfilehash: 1dde7bfc74c6a7b0c03d8f192f4e6eaf72e2de6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74870169"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Kurz: Dotazování rozhraní Gremlin API služby Azure Cosmos DB pomocí Gremlin

Rozhraní [Gremlin API](graph-introduction.md) služby Azure Cosmos DB podporuje dotazy [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Tento článek poskytuje ukázkové dokumenty a dotazy, které vám pomůžou začít. Podrobné referenční informace ke Gremlin najdete v článku [Podpora Gremlin](gremlin-support.md).

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování dat pomocí Gremlin

## <a name="prerequisites"></a>Požadavky

Aby tyto dotazy fungovaly, musíte mít účet služby Azure Cosmos DB a data grafu v kontejneru. Něco z toho nemáte? Vytvořte účet a naplňte databázi dokončením [5minutového rychlého startu](create-graph-dotnet.md) nebo [kurzu pro vývojáře](tutorial-query-graph.md). Následující dotazy můžete spustit pomocí [konzoly Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) nebo oblíbeného ovladače Gremlin.

## <a name="count-vertices-in-the-graph"></a>Výpočet vrcholů v grafu

Následující fragment kódu ukazuje, jak vypočítat počet vrcholů v grafu:

```
g.V().count()
```

## <a name="filters"></a>Filtry

Můžete provádět filtrování pomocí kroků Gremlin `has` a `hasLabel` a jejich kombinací pomocí operátorů `and`, `or` a `not` vytvářet složitější filtry. Azure Cosmos DB poskytuje na schématu nezávislé indexování všech vlastností v rámci vrcholů a stupňů pro zajištění rychlých dotazů:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projekce

Do výsledků dotazu můžete promítnout určité vlastnosti pomocí kroku `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Vyhledání souvisejících hran a vrcholů

Zatím jsme viděli pouze operátory dotazu, které fungují v jakékoli databázi. Grafy jsou rychlé a efektivní pro operace procházení, kdy potřebujete přecházet k souvisejícím hranám a vrcholům. Teď najdeme všechny přátele Thomase. Provedeme to pomocí kroku Gremlin `outE`, kterým vyhledáme všechny vnější hrany od Thomase, a pak pomocí kroku Gremlin `inV` přejdeme z těchto hran k vnitřním vrcholům:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Další dotaz provádí dva segmenty směrování a dvojím zavoláním kroků `outE` a `inV` vyhledá všechny přátele přátel Thomase. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Pomocí Gremlin můžete vytvářet i složitější dotazy a implementovat výkonnou logiku procházení grafů, včetně kombinování výrazů filtru, provádění smyček pomocí kroku `loop` a implementace podmíněné navigace pomocí kroku `choose`. Získejte další informace o tom, co můžete provádět díky [podpoře Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí Graphu. 

Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Cosmos DB.

> [!div class="nextstepaction"]
> [Globální distribuce](distribute-data-globally.md) 

