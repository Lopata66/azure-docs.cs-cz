---
title: Ingestování ukázková data do Průzkumníku dat Azure
description: Další informace o tom, jak ingestování (načíst) týkající se počasí s ukázková data do Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e80322cda671e2145cf3e65aa1457f1fa1827737
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759275"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Ingestování ukázková data do Průzkumníku dat Azure

Tento článek ukazuje, jak ingestování (načíst) ukázková data do databáze aplikace Průzkumník dat Azure. Existují [několik způsobů, jak ingestovat data](ingest-data-overview.md); v tomto článku se zaměřuje na základní přístup, který je vhodný pro účely testování.

> [!NOTE]
> Pokud už máte tato data [rychlý start: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Požadavky

[Testovací cluster a databázi](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingestace dat

Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Přihlaste se k webu [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. V levém horním rohu aplikace vyberte **Přidat cluster**.

1. V **clusteru přidat** dialogového okna zadejte adresu URL vašeho clusteru ve formě `https://<ClusterName>.<Region>.kusto.windows.net/`a pak vyberte **přidat**.

1. V následujícím příkazu vložte a vyberte **spustit**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po dokončení ingestování, vložte do ní následující dotaz, vyberte v okně dotazu a vyberte **spustit**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Dotaz vrátí následující výsledky z hodnot zpracovaných ukázková data.

    ![Výsledky dotazu](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Dotazování dat v Průzkumníku dat Azure](web-query-data.md)

> [!div class="nextstepaction"]
> [Zápis dotazů](write-queries.md)

> [!div class="nextstepaction"]
> [Příjem dat Azure Průzkumník dat](ingest-data-overview.md)