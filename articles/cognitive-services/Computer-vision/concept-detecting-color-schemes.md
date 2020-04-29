---
title: Detekce barevného schématu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s detekcí barevného schématu v obrázcích pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244728"
---
# <a name="detect-color-schemes-in-images"></a>Detekce barevných schémat na obrázcích

Počítačové zpracování obrazu analyzuje barvy v obrázku, aby poskytovaly tři různé atributy: dominantní barva popředí, dominantní barva pozadí a sada dominantních barev pro obrázek jako celek. Vrácené barvy patří do množiny: černá, modrá, hnědá, šedá, zelená, oranžová, růžová, fialová, červená, šedozelená, bílá a žlutá. 

Počítačové zpracování obrazu také extrahuje zvýrazňující barvu, která představuje nejvíce zářivé barvy v obrázku na základě kombinace dominantních barev a sytosti. Barva zvýraznění se vrátí jako hexadecimální kód HTML barvy. 

Počítačové zpracování obrazu také vrátí logickou hodnotu, která označuje, zda je obrázek černobílý.

## <a name="color-scheme-detection-examples"></a>Příklady detekce barevného schématu

Následující příklad znázorňuje odpověď JSON vrácenou Počítačové zpracování obrazu při zjišťování barevného schématu ukázkového obrázku. V takovém případě se příklad obrázku nejedná o černý a bílý obrázek, ale dominantní barvy popředí a pozadí jsou černé a dominantní barvy obrázku jako celku jsou černá a bílá.

![Venkovní horská oblast na západce s silhouetteou osoby](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Příklady převládajících barev

V následující tabulce jsou zobrazeny vrácené barvy popředí, pozadí a obrázku pro každý vzorový obrázek.

| Image | Dominantní barvy |
|-------|-----------------|
|![Bílá květina se zeleným pozadím](./Images/flower.png)| Popředí: černá<br/>Background: bílá<br/>Barvy: černá, bílá, zelená|
![Vlak provozovaný přes stanici](./Images/train_station.png) | Popředí: černá<br/>Pozadí: černá<br/>Barvy: černá |

### <a name="accent-color-examples"></a>Příklady barev zvýraznění

 Následující tabulka ukazuje vrácenou barvu zvýraznění jako hexadecimální hodnotu barvy HTML pro každý příklad obrázku.

| Image | Doplňková barva |
|-------|--------------|
|![Osoba, která stojí na horských rockech v slunce](./Images/mountain_vista.png) | #BB6D10 |
|![Bílá květina se zeleným pozadím](./Images/flower.png) | #C6A205 |
|![Vlak provozovaný přes stanici](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Příklady detekce černého & bílé

Následující tabulka ukazuje Počítačové zpracování obrazu černé a bílé vyhodnocení v ukázkových obrázcích.

| Image | Černý & bílá? |
|-------|----------------|
|![Černý a bílý obrázek budov v Manhattan](./Images/bw_buildings.png) | true |
|![Modrý dům a přední yard](./Images/house_yard.png) | false (nepravda) |

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce barevného schématu je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Do `Color` parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"color"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rychlý Start: analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
