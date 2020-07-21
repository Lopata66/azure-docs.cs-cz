---
title: Mapování transformace Unpivot toku dat
description: Azure Data Factory transformace Unpivot toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: b7a05f33c199ab46508fa2b9bc0ef576f888ef27
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537710"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformace Unpivot Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte Unpivot v toku dat mapování ADF jako způsob, jak změnit nenormalizovanou datovou sadu na podrobnější verzi tím, že rozbalíte hodnoty z více sloupců v jednom záznamu do několika záznamů se stejnými hodnotami v jednom sloupci.

![Transformace Unpivot](media/data-flow/unpivot1.png "Unpivot možnosti 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Oddělit podle

![Transformace Unpivot](media/data-flow/unpivot5.png "Unpivot možnosti 2")

Nejprve nastavte sloupce, které chcete seskupit podle pro agregaci pivotu. Nastavte jeden nebo více sloupců pro odseskupení pomocí znaku + vedle seznamu sloupců.

## <a name="unpivot-key"></a>Unpivot klíč

![Transformace Unpivot](media/data-flow/unpivot6.png "Unpivot možnosti 3")

Kontingenční klíč je sloupec, který bude ADF od řádku po sloupec. Ve výchozím nastavení se všechny jedinečné hodnoty v datové sadě pro toto pole Překlopí do sloupce. Volitelně můžete zadat hodnoty z datové sady, které chcete překlopit do hodnot sloupců.

## <a name="unpivoted-columns"></a>Sloupce s nekontingenčními tabulkami

![Transformace Unpivot](media/data-flow//unpivot7.png "Unpivot možnosti 4")

Nakonec vyberte agregaci, kterou chcete použít pro transformované hodnoty, a určete, jak chcete, aby se sloupce zobrazovaly v nové výstupní projekci z transformace.

Volitelné Můžete nastavit vzor pojmenování s předponou, střední a příponou, která se má přidat do každého nového názvu sloupce z hodnot řádků.

Například při překlopení "prodej" podle "oblasti" jednoduše získáte nové hodnoty sloupce z každé hodnoty Sales. Například: "25", "50", "1000",... Pokud ale nastavíte hodnotu předpony Sales (prodej), bude hodnota Sales pro hodnoty předem pevně daná.

<img src="media/data-flow/unpivot3.png" width="400">

Když nastavíte uspořádání sloupců na "normální", budou se seskupovat všechny kontingenční sloupce s agregovanými hodnotami. Nastavení uspořádání sloupců na stranu "bočního" se bude střídat mezi sloupcem a hodnotou.

![Transformace Unpivot](media/data-flow//unpivot7.png "Unpivot možnosti 5")

Poslední nepivotovaná sada výsledků dat zobrazuje celkový počet sloupců, které se teď nepivotují na samostatné hodnoty řádků.

## <a name="next-steps"></a>Další kroky

K pivotování řádků na sloupce použijte [transformaci pivotu](data-flow-pivot.md) .
