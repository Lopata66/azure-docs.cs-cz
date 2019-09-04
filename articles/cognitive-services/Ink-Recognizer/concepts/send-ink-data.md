---
title: Odesílání dat do rozhraní API pro rozpoznávání rukopisu
titleSuffix: Azure Cognitive Services
description: Další informace o volání rozhraní API analyzátoru barev pro různé aplikace
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274476"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Odesílání dat do rozhraní API pro rozpoznávání rukopisu 

Termín digitální rukopis označuje technologie, které umožňují digitální reprezentaci výstupu, jako jsou rukopis a kresby. To se obvykle dosahuje pomocí digitizéru, který zachycuje přesuny vstupních zařízení, jako je Stylus. Zařízení stále víc podporují bohatá prostředí digitálních rukopisů a umělá inteligence a strojové učení umožňují rozpoznávání psaných tvarů a textu v libovolném kontextu. Rozhraní API pro rozpoznávání rukopisu umožňuje odeslat tahy perem a získat podrobné informace o nich. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Rozhraní API pro rozpoznávání rukopisu vs. Služby OCR

Rozhraní API pro rozpoznávání rukopisu nepoužívá optické rozpoznávání znaků (OCR). Služba OCR zpracovává data v pixelech z obrázků, aby poskytovala rozpoznávání rukopisu a textu. To se někdy nazývá rozpoznávání offline. Místo toho rozhraní API pro rozpoznávání rukopisu vyžaduje, aby se při použití vstupního zařízení použila data digitálního tahu. Zpracování dat digitálního inkoustu tímto způsobem může dosáhnout přesnější výsledků rozpoznávání v porovnání se službami OCR. 

## <a name="sending-ink-data"></a>Odesílání dat rukopisu

Rozhraní API pro rozpoznávání rukopisu vyžaduje souřadnice X a Y, které reprezentují tahy rukopisu vytvořené vstupním zařízením, od okamžiku, kdy se dotkne roviny detekce na okamžik, kdy se přeruší. Body každého tahu musí být řetězcem hodnot oddělených čárkami a formátovány ve formátu JSON, podobně jako v následujícím příkladu. Každý tah inkoustu navíc musí mít v každém požadavku jedinečný identifikátor. Pokud se ID opakuje v rámci stejné žádosti, rozhraní API vrátí chybu. Pro nejpřesnější výsledky rozpoznávání musí mít za desetinnou čárkou aspoň osm číslic. Počátek (0, 0) plátna se považuje za levý horní roh plátna pro rukopis.

> [!NOTE]
> Následující příklad není platný formát JSON. Na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089909)můžete najít úplný požadavek na JSON pro rozpoznávání rukopisu.
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Odpověď na rozpoznávání rukopisu

Rozhraní API pro rozpoznávání rukopisu vrátí odpověď na analýzu pro objekty, které rozpoznal obsah inkoustu. Odpověď obsahuje jednotky pro rozpoznávání, které popisují vztahy mezi různými tahy perem. Například tahy, které vytvářejí samostatné, oddělené tvary budou obsaženy v různých jednotkách. Každá jednotka obsahuje podrobné informace o jeho tazích pera, včetně rozpoznaného objektu, jeho souřadnic a dalších atributů kreslení.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Obrazce rozpoznané rozhraním API pro rozpoznávání rukopisu

Rozhraní API pro rozpoznávání rukopisu dokáže identifikovat nejčastěji používané tvary v pořizování. Následující obrázek ukazuje několik základních příkladů. Úplný seznam obrazců a dalšího inkoustového obsahu rozpoznávaného rozhraním API najdete v [článku referenční informace k rozhraní API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Seznam obrazců rozpoznaných rozhraním API pro rozpoznávání rukopisu](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Doporučené vzory volání

Můžete zavolat Nástroj pro rozpoznávání rukopisu REST API v různých vzorech podle vaší aplikace. 

### <a name="user-initiated-api-calls"></a>Uživatelem iniciovaná volání rozhraní API

Pokud vytváříte aplikaci, která přijímá vstup uživatele (například pořizování nebo anotace aplikace), můžete jim poskytnout kontrolu nad tím, kdy a který rukopis se pošle do rozhraní API pro rozpoznávání rukopisu. Tato funkce je užitečná hlavně v případě, že je text a obrazce na plátně přítomné a uživatelé pro ně chtějí provádět různé akce. Zvažte přidání funkcí výběru (například laso nebo jiného nástroje pro geometrické výběry), které uživatelům umožňují vybrat, co se má do rozhraní API odeslat.  

### <a name="app-initiated-api-calls"></a>Volání rozhraní API iniciovaná aplikací

Aplikaci můžete také volat rozhraní API pro rozpoznávání rukopisu po vypršení časového limitu. Tím, že se aktuální tahy rukopisu odesílají do rozhraní API rutinně, můžete ukládat výsledky rozpoznávání při jejich vytváření a zároveň zlepšit dobu odezvy rozhraní API. Například po zjištění, že uživatel dokončil rozpoznávání, můžete do rozhraní API odeslat řádek ručně psaného textu. 

Výsledkem rozpoznávání se předem získáte informace o vlastnostech tahů perem, které se navzájem týkají. Například, které tahy jsou seskupeny tak, aby tvořily stejné slovo, řádek, seznam, odstavec nebo tvar. Tyto informace mohou zlepšit funkce výběru inkoustu vaší aplikace, například možnost vybrat skupiny tahů najednou.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrace rozhraní API pro rozpoznávání rukopisu pomocí Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) nabízí nástroje a technologie, které umožňují digitální psaní digitálních zážitků na nejrůznějších zařízeních. Platformu Windows Ink API můžete kombinovat s rozhraním API pro rozpoznávání rukopisu k vytváření aplikací, které zobrazují a interpretují tahy digitálního pera.

## <a name="next-steps"></a>Další postup

* [Co je rozhraní API pro rozpoznávání rukopisu?](../overview.md)
* [Odkaz na REST API pro rozpoznávání rukopisu](https://go.microsoft.com/fwlink/?linkid=2089907)

* Spustit posílání dat digitálního inkoustového tahu pomocí:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
