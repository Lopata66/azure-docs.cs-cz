---
title: Monitorování a ladění pomocí metrik v Azure Cosmos DB
description: Pomocí metrik v Azure Cosmos DB můžete ladit běžné problémy a monitorovat databázi.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246458"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitorování a ladění pomocí metrik v Azure Cosmos DB

Azure Cosmos DB poskytuje metriky pro propustnost, úložiště, konzistenci, dostupnost a latenci. Azure Portal nabízí agregované zobrazení těchto metrik. Metriky služby Azure Cosmos DB můžete zobrazit také s využitím rozhraní API služby Azure Monitor. Další informace o tom, jak zobrazit metriky z Azure monitoru, najdete v článku o [získání metrik z Azure monitor](cosmos-db-azure-monitor-metrics.md) . 

Tento článek vás provede běžnými případy použití a Azure Cosmos DB metriky, které je možné použít k analýze a ladění těchto problémů. Metriky se shromažďují každých pět minut a uchovávají se po dobu sedmi dní.

## <a name="view-metrics-from-azure-portal"></a>Zobrazit metriky z Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete podokno **metriky** . Ve výchozím nastavení zobrazuje podokno metriky metriky úložiště, index, počet jednotek žádostí pro všechny databáze v účtu Azure Cosmos. Tyto metriky můžete filtrovat podle databáze, kontejneru nebo oblasti. Metriky můžete filtrovat také v konkrétní časové členitosti. Další podrobnosti o propustnosti, úložišti, dostupnosti, latenci a metrikě konzistence jsou k dispozici na jednotlivých kartách. 

   ![Cosmos DB metriky výkonu v Azure Portal](./media/use-metrics/performance-metrics.png)

V podokně **metriky** jsou k dispozici následující metriky: 

* **Metriky propustnosti** – Tato metrika zobrazuje počet použitých nebo neúspěšných požadavků (kód odpovědi 429), protože byla překročena propustnost nebo kapacita úložiště zřízená pro kontejner.

* **Metriky úložiště** – Tato metrika zobrazuje velikost dat a využití indexu.

* **Metriky dostupnosti** – Tato metrika zobrazuje procento úspěšných požadavků v rámci celkových požadavků za hodinu. Míra úspěšnosti je definována Azure Cosmos DB SLA.

* **Metriky latence** – Tato metrika zobrazuje latenci čtení a zápisu zjištěnou Azure Cosmos DB v oblasti, ve které váš účet pracuje. V geograficky replikovaném účtu můžete vizualizovat latenci napříč oblastmi. Tato metrika nepředstavuje koncovou latenci požadavku.

* **Metriky konzistence** – Tato metrika ukazuje, jakým způsobem je možné konzistence modelu konzistence zvolit. U účtů s více oblastmi zobrazuje tato metrika také latenci replikace mezi oblastmi, které jste vybrali.

* **Metriky systému** – Tato metrika ukazuje, kolik požadavků na metadata jsou obsluhovány hlavním oddílem. Pomůže vám také identifikovat omezené požadavky.

V následujících částech se vysvětlují běžné scénáře, kdy můžete použít Azure Cosmos DB metriky. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Vysvětlení, kolik požadavků je úspěšných nebo způsobujících chyby

Začněte tím, že přejdete na [Azure Portal](https://portal.azure.com) a přejdete na okno **metriky** . V okně vyhledejte * * počet požadavků překročil kapacitu na 1 minutu. Tento graf znázorňuje minuty z celkového počtu minut segmentované na základě stavového kódu. Další informace o stavových kódech HTTP najdete v tématu [stavové kódy HTTP pro Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Nejběžnější stavový kód chyby je 429 (omezení rychlosti/omezování). Tato chyba znamená, že požadavky na Azure Cosmos DB jsou vyšší než zajištěná propustnost. Nejběžnějším řešením tohoto problému je [horizontální navýšení kapacity ru](./set-throughput.md) pro danou kolekci.

![Počet požadavků za minutu](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Určení distribuce propustnosti mezi oddíly

Dobré mohutnosti klíčů oddílů je nezbytné pro libovolnou škálovatelnou aplikaci. Chcete-li určit rozdělení propustnosti děleného kontejneru rozděleného podle oddílů, přejděte do okna **metriky** v [Azure Portal](https://portal.azure.com). Na kartě **propustnost** se rozpis úložiště zobrazuje v poli **maximální spotřebované ru/sekund každého grafu fyzického oddílu** . Následující obrázek znázorňuje příklad špatné distribuce dat, jak znázorňuje zkosený oddíl úplně vlevo.

![Jeden oddíl se silným využitím v 3:05. odp.](media/use-metrics/metrics-17.png)

Nerovnoměrné rozdělení propustnosti může způsobovat *aktivní* oddíly, což může vést k omezení požadavků a může vyžadovat přerozdělení na oddíly. Další informace o dělení v Azure Cosmos DB najdete v tématu [dělení a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Určení distribuce úložiště mezi oddíly

Dobré mohutnosti oddílu je nezbytné pro všechny škálovatelné aplikace. Chcete-li určit distribuci úložiště děleného kontejneru rozděleného podle oddílů, přejděte na okno metriky v [Azure Portal](https://portal.azure.com). Na kartě úložiště se rozpis úložiště zobrazuje v grafu data a indexu spotřebovaného v horních klíčích oddílů. Následující obrázek znázorňuje špatnou distribuci úložiště dat, jak znázorňuje šikmý oddíl úplně vlevo.

![Příklad špatné distribuce dat](media/use-metrics/metrics-07.png)

Můžete hlavní příčinu, která klíč oddílu zkosí rozdělení, kliknutím na oddíl v grafu.

![Klíč oddílu zkosí distribuci.](media/use-metrics/metrics-05.png)

Po zjištění, který klíč oddílu způsobuje rozdělení v distribuci, bude pravděpodobně nutné znovu rozdělit svůj kontejner na více distribuovaných klíčů oddílu. Další informace o dělení v Azure Cosmos DB najdete v tématu [dělení a škálování v Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Porovnat velikost dat s velikostí indexu

V Azure Cosmos DB celkové spotřebované úložiště je kombinací velikosti dat i velikosti indexu. Velikost indexu je obvykle zlomkem velikosti dat. V okně metriky v [Azure Portal](https://portal.azure.com)karta úložiště prezentuje rozdělení spotřeby úložiště na základě dat a indexu.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Pokud chcete chránit místo v indexu, můžete upravit [zásady indexování](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Ladění, proč jsou dotazy spuštěné pomalu

V sadách SDK SQL API Azure Cosmos DB poskytuje statistiku spouštění dotazů.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* poskytuje podrobné informace o tom, jak dlouho trvalo provádění každé součásti dotazu. Nejběžnější hlavní příčinou dlouhotrvajících dotazů je prohledávání, což znamená, že dotaz nedokázal využít indexy. Tento problém lze vyřešit pomocí lepší podmínky filtru.

## <a name="next-steps"></a>Další kroky

Nyní jste zjistili, jak monitorovat a ladit problémy pomocí metriky, které jsou k dispozici v Azure Portal. Další informace o vylepšení výkonu databáze najdete v následujících článcích:

* Další informace o tom, jak zobrazit metriky z Azure monitoru, najdete v článku o [získání metrik z Azure monitor](cosmos-db-azure-monitor-metrics.md) . 
* [Testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md)
* [Tipy pro zvýšení výkonu pro službu Azure Cosmos DB](performance-tips.md)
