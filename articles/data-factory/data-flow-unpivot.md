---
title: Transformace toku dat mapování Azure Data Factory převést na řádky
description: Transformace toku dat mapování Azure Data Factory převést na řádky
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348151"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory převést řádky transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Použijte Unpivot v ADF mapování toku dat jako způsob, jak proměnit Nenormalizovaná datová sada více normalizované verzi tak, že rozbalíte hodnoty z více sloupců v jednom záznamu do více záznamy pomocí stejných hodnot v jednom sloupci.

![Převést na řádky transformace](media/data-flow/unpivot1.png "převést řádky možnosti 1")

## <a name="ungroup-by"></a>Oddělit pomocí

![Převést na řádky transformace](media/data-flow/unpivot5.png "převést řádky možnosti 2")

Nejprve nastavte sloupce, které chcete seskupit podle vaší pivot agregace. Nastavit jeden nebo více sloupců pro oddělení s + znaménko vedle seznamu sloupců.

## <a name="unpivot-key"></a>Převést na řádky klíč

![Převést na řádky transformace](media/data-flow/unpivot6.png "převést řádky možnosti 3")

Klíče kontingenční tabulky je sloupec, který bude ADF otáčení z řádku na sloupec. Ve výchozím nastavení bude otáčení každou jedinečnou hodnotu v datové sadě pro toto pole na sloupec. Však můžete volitelně zadat hodnoty z datové sady, kterou chcete přenechte obranu hodnot sloupců.

## <a name="unpivoted-columns"></a>Sloupce převedené na řádky

![Převést na řádky transformace](media/data-flow//unpivot7.png "převést možnosti 4 řádky")

A konečně zvolte agregaci, kterou chcete použít pro pivotovaná hodnoty a jak byste chtěli sloupce, který se má zobrazit v nové projekce výstup z transformace.

(Volitelné) Můžete nastavit vzoru pro pojmenovávání s předponou, střední a příponu je třeba přidat do každého nový název sloupce z hodnoty řádků.

Například přesouvání "prodeje" "Oblasti" by jednoduše získáte hodnoty nového sloupce od každé hodnoty prodeje. Příklad: "25", "50", "1000", ... Ale pokud nastavíte hodnotu předpony "Prodeje", pak "Prodeje" bude začínat na hodnoty.

<img src="media/data-flow/unpivot3.png" width="400">

Nastavení uspořádání sloupců "Normální" seskupí dohromady pivotovaná sloupce s jejich agregované hodnoty. Mezi sloupci a hodnota se alternativní nastavení uspořádání sloupců na "Laterální".

![Převést na řádky transformace](media/data-flow//unpivot7.png "převést řádky možnosti 5")

Převedené na řádky dat výsledek sady zobrazí sloupec součtů nyní převedené na řádky na samostatném řádku hodnoty.

## <a name="next-steps"></a>Další postup

Použití [otáčení transformace](data-flow-pivot.md) na pivot řádky na sloupce.
