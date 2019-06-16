---
title: Azure Data Factory mapování režim ladění toku dat
description: Spusťte relaci interaktivní ladění při vytváření datové toky
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239195"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapování režim ladění toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapování toku dat má režimu ladění, které lze přepnout toku dat ladění tlačítko v horní části návrhové plochy. Při navrhování toků dat, nastavení režimu ladění vám umožní interaktivně sledovat data tvarovat transformace při sestavení a ladění datové toky. V toku dat seminářů zaměřených na návrh i při spuštění ladění kanálů toků dat je možné relace ladění.

![Ladění tlačítko](media/data-flow/debugbutton.png "tlačítko ladit.")

## <a name="overview"></a>Přehled
Po zapnutí režimu ladění vytvoříte interaktivně váš tok dat s aktivním clusteru Spark. Relace se zavře, jakmile vypnete ladění ve službě Azure Data Factory. Měli byste vědět hodinové poplatky vzniklé během doby, kterou máte zapnuté relaci ladění v Azure Databricks.

Ve většině případů je vhodné vytvořit vaše toky dat v režimu ladění, mohli ověřit vaši obchodní logiku a zobrazit transformaci dat před publikováním svou práci ve službě Azure Data Factory. K testování toku dat v rámci kanálu by měl použít taky "Ladění" tlačítko na panelu kanálu.

> [!NOTE]
> Světlý režim ladění je zelené na panelu nástrojů služby Data Factory, účtuje se sazbou toku dat ladění 8 jader/hod obecné výpočetní s 60 minut time-to-live 

## <a name="debug-mode-on"></a>Režim ladění na
Po přepnutí na režim ladění, zobrazí výzva s formulářem postranní panel, který si vyžádá přejděte ke svému clusteru interaktivní Azure Databricks a vyberte možnosti pro vzorkování zdroje. Musíte použít cluster interactive z Azure Databricks a vyberte velikost vzorkování z každého transformace vaší zdrojové nebo vyberte textový soubor vhodný pro vaše testovací data.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Při spuštění v režimu ladění v toku dat, vaše data nebudou zapsány do jímky transformace. Ladicí relaci má sloužit jako test > prostředí pro transformaci. Jímky nejsou vyžadovány během ladění a jsou ignorovány ve svém toku data. Pokud chcete otestovat zápisu dat > vaše Sink spuštění toku dat z kanálu služby Azure Data Factory a použít spuštění ladění z kanálu.

## <a name="debug-settings"></a>Nastavení ladění
Nastavení ladění může být každý zdroj z toku dat se zobrazí v postranní panel a můžete také upravit tak, že vyberete "nastavení zdroje" na panelu nástrojů návrháře se předávají Data. Omezení a/nebo zdrojový soubor pro použití u každého transformace zdroje Tady můžete vybrat. Omezení řádků v tomto nastavení platí pouze pro aktuální relaci ladění. Můžete také použít nastavení vzorkování ve zdroji k omezení řádků do transformace zdroje.

## <a name="cluster-status"></a>Stav clusteru
Je indikátor stavu clusteru v horní části návrhové plochy, která se změní na zelenou při bude cluster připravený pro ladění. Pokud váš cluster již je teplo, se prakticky okamžitě zobrazí zelený ukazatel. Pokud váš cluster nebyla spuštěna při zadání režimu ladění, budete muset počkat 5-7 minut, než se cluster tak, aby mohli spustit. Až bude připravená, bude žlutý indikátoru. Jakmile cluster je připravený k ladění toku dat, se změní na zelenou indikátoru.

Až skončíte s vaší ladění, vypněte ladění přepínač tak, aby váš cluster Azure Databricks můžete ukončit a budou už se vám účtovat aktivity ladění.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Náhled dat
Ladicí program na kartě Náhled dat bude světla nahoru na dolním panelu. Bez režimu ladění na toku dat zobrazí pouze aktuální metadata do a z transformace na kartě zkontrolujte, jestli se. Náhled dat bude pouze dotaz na počet řádků, které jste nastavili jako svůj limit v nastavení ladění. Budete možná muset kliknout na "Načíst data" aktualizace dat ve verzi preview.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Data profilů
Výběrem jednotlivých sloupců na kartě preview vaše data budou automaticky otevírané okno grafu na vaší datové mřížce se podrobné statistikami o jednotlivých polích úplně vpravo. Azure Data Factory způsobí, že zjišťování na základě dat vzorkování, který typ grafu pro zobrazení. Vysoká Kardinalita pole bude ve výchozím nastavení s hodnotou NULL nebo NOT NULL grafy, zatímco zařazené do kategorií a číselná data, která má nízkou Kardinalita se zobrazí pruhové grafy znázorňující frekvenci hodnotu data. Zobrazí se také maximální / délka délka pole řetězců, minimální / maximální hodnoty v číselné pole, standardní dev, percentily, počty a průměr. 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>Další postup

Po dokončení sestavování a ladění váš tok dat [spustit ho v kanálu.](control-flow-execute-data-flow-activity.md)

Při testování svůj kanál s tokem dat, použití kanálu [provádění možnost Spustit ladění.](iterative-development-debugging.md)
