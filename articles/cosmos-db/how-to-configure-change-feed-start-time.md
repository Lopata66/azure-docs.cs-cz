---
title: Jak nakonfigurovat čas spuštění procesoru změny kanálu – Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat procesor změn kanálu tak, aby začal číst z konkrétní datum a čas.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 00b1553a018e816b4ec1672d27534d576e71a7f6
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990771"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Jak nakonfigurovat počáteční čas procesoru změny kanálu

Tento článek popisuje, jak můžete nakonfigurovat [procesor změn kanálu](./change-feed-processor.md) tak, aby se spouštěl z konkrétního data a času.

## <a name="default-behavior"></a>Výchozí chování

Když se procesor pro změnu kanálu poprvé spustí, inicializuje kontejner zapůjčení a spustí jeho [životní cyklus zpracování](./change-feed-processor.md#processing-life-cycle). Všechny změny, ke kterým došlo v kontejneru předtím, než byl procesor Change feed inicializován poprvé, se nezjistí.

## <a name="reading-from-a-previous-date-and-time"></a>Čtení z předchozího data a času

Je možné inicializovat procesor změn, aby bylo možné číst změny od **určitého data a času**, a to předáním instance `DateTime` `WithStartTime` do rozšíření tvůrce:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Procesor změnového kanálu se inicializuje pro konkrétní datum a čas a začne číst změny, ke kterým došlo po.

## <a name="reading-from-the-beginning"></a>Čtení od začátku

V jiných scénářích, jako jsou migrace dat nebo analýza celé historie kontejneru, musíme načíst kanál změn od **začátku životnosti tohoto kontejneru**. K tomu můžeme použít `WithStartTime` rozšíření tvůrce, ale předáním `DateTime.MinValue.ToUniversalTime()`, což vygeneruje reprezentaci UTC minimální `DateTime` hodnoty, například:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Procesor změny kanálu bude inicializován a začne číst změny od začátku životnosti kontejneru.

> [!NOTE]
> Tyto možnosti přizpůsobení fungují pouze k nastavení počátečního bodu v čase procesoru Change feed. Po prvním spuštění kontejneru zapůjčení, změna nemá žádný vliv.

> [!NOTE]
> Při zadávání bodu v čase budou čteny pouze změny pro položky, které aktuálně existují v kontejneru. Pokud se položka odstranila, odebere se i její historie kanálu změn.

## <a name="additional-resources"></a>Další zdroje

* [Sada Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Ukázky použití na GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Další ukázky na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Další kroky

Teď můžete pokračovat a získat další informace o procesoru Change feed v následujících článcích:

* [Přehled procesoru Change feed](change-feed-processor.md)
* [Použití Estimator kanálu změn](how-to-use-change-feed-estimator.md)
