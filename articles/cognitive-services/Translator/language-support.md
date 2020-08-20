---
title: Jazyková podpora – Překladatel
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator podporuje následující jazyky pro překlad textu na text pomocí NMT (neuronové Machine Translation).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: a0ea0a7580bbb7a6106880d2733f36f5ab7a7690
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639779"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Podpora jazyků a oblastí pro převod textu a řeči

Použijte překladatele k překladu do a z libovolného z 70 a textových jazyků pro překlad textu. Neuronové Machine Translation (NMT) je nový standard pro vysoce kvalitní překlady počítačů s podporou AI a je k dispozici jako výchozí v případě, že je k dispozici systém neuronové.

Překladatele můžete také použít ve spojení s vlastním překladatelem k vytváření neuronové překladových systémů, které rozumí terminologii používané ve vašem podniku a průmyslu, a pomocí služby Microsoft Speech Service k přidání překladu řeči do aplikace.

[Další informace o tom, jak strojový překlad funguje](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>překlad textu,
Překlad textu je k dispozici pomocí operace překladu do nebo z libovolného jazyka, který je k dispozici v překladateli. Rozhraní API také nabízí detekci jazyka pomocí operace zjišťování, použití operace přepřepisu a dvojjazyčné slovníky pomocí operací vyhledávání slovníku a slovníku. Dostupné jazyky pro každou z těchto operací jsou uvedeny níže. 

### <a name="translate"></a>Překlad

Překladatel podporuje pro převod textu na text následující jazyky. 

[Referenční dokumentace operace zobrazení překladu](reference/v3-0-translate.md)

|Jazyk|  Kód jazyka|
|:-----|:-----:|
|Afrikánština| `af`|
|Arabština|    `ar`    |
|Bengálština|    `bn`    |
|Bosenština (latinka)|   `bs`    |
|Bulharština| `bg`    |
|Kantonština (tradiční)|   `yue`|
|Katalánština|   `ca`    |
|Čínština (zjednodušená)|    `zh-Hans`|
|Čínština (tradiční)|   `zh-Hant`       |
|Chorvatština|  `hr`    |
|Čeština| `cs`    |
|Dáríština|  `prs`   |
|Dánština|    `da`        |
|Nizozemština| `nl`|
|Angličtina|   `en`    |
|Estonština|  `et`    |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
|Finština|   `fi`    |
|Francouzština|    `fr`    |
|Němčina|    `de`    |
|Řečtina| `el`    |
|Gudžarátština|  `gu`    |
|Haitská kreolština|    `ht`        |
|Hebrejština |`he`   |
|Hindština| `hi`    |
|Hmong Macek| `mww`   |
|Maďarština| `hu`    |
|Islandština| `is`    |
|Indonéština|    `id`    |
|Irština | `ga`|
|Italština|   `it`    |
|Japonština|  `ja`    |
|Kannadština|`kn`|
|Kazaština|`kk`|
|Svahilština| `sw`    |
|Klingon|   `tlh-Latn`  |
|Klingon (plqaD)|   `tlh-Piqd`  |
|Korejština |`ko`   |
|Lotyština|   `lv`    |
|Litevština|    `lt`    |
|Malgašština|  `mg`    |
|Malajština| `ms`        |
|Malajalámština| `ml` |
|Maltština|   `mt`    |
|Maorština| `mi`  |
|Maráthština| `mr`  |
|Norština| `nb`    |
|Udijština|  `or`    |
|Paštština|    `ps`    |
|Perština|   `fa`    |
|Polština|    `pl`    |
|Portugalština (Brazílie)|   `pt-br` |
|portugalština (Portugalsko)| `pt-pt` |
|Paňdžábština|`pa`|
|Queretaro Otomi|   `otq`   |
|Rumunština|  `ro`    |
|Ruština|   `ru`    |
|Samoan|    `sm`    |
|Srbština (cyrilice)|    `sr-Cyrl`|
|Srbština (latinka)|   `sr-Latn`       |
|Slovenština|    `sk`    |
|Slovinština| `sl`    |
|Španělština|   `es`    |
|Švédština|   `sv`    |
|Tahitian|  `ty`    |
|Tamilština| `ta`    |
|Telugština|    `te`    |
|Thajština|  `th`    |
|Tongánština|    `to`    |
|Turečtina|   `tr`        |
|Ukrajinština| `uk`    |
|Urdština|  `ur`    |
|Vietnamština|    `vi`    |
|Velština| `cy`    |
|Yucatec Maya|  `yua`   |

> [!NOTE]
> Kód jazyka `pt` bude výchozí `pt-br` , portugalština (Brazílie).

### <a name="detect"></a>Zjišťování

Překladatel detekuje následující jazyky pro překlad a převod.

[Referenční dokumentace k operaci zjišťování zobrazení](reference/v3-0-detect.md)

|Jazyk|  Kód jazyka|
|:-----|:-----:|
|Afrikánština| `af`|
|Arabština|    `ar`    |
|Bulharština| `bg`    |
|Katalánština|   `ca`    |
|Čínština (zjednodušená)|    `zh-Hans`|
|Čínština (tradiční)|   `zh-Hant`       |
|Chorvatština|  `hr`    |
|Čeština| `cs`    |
|Dánština|    `da`        |
|Nizozemština| `nl`|
|Angličtina|   `en`    |
|Estonština|  `et`    |
|Finština|   `fi`    |
|Francouzština|    `fr`    |
|Němčina|    `de`    |
|Řečtina| `el`    |
|Gudžarátština|  `gu`    |
|Haitská kreolština|    `ht`        |
|Hebrejština |`he`   |
|Hindština| `hi`    |
|Maďarština| `hu`    |
|Islandština| `is`    |
|Indonéština|    `id`    |
|Irština | `ga`|
|Italština|   `it`    |
|Japonština|  `ja`    |
|Svahilština| `sw`    |
|Klingon|   `tlh-Latn`  |
|Korejština |`ko`   |
|Lotyština|   `lv`    |
|Litevština|    `lt`    |
|Malajština| `ms`        |
|Maltština|   `mt`    |
|Norština| `nb`    |
|Perština|   `fa`    |
|Polština|    `pl`    |
|Portugalština (Brazílie)|   `pt-br` |
|portugalština (Portugalsko)| `pt-pt` |
|Rumunština|  `ro`    |
|Ruština|   `ru`    |
|Srbština (cyrilice)|    `sr-Cyrl`|
|Srbština (latinka)|   `sr-Latn`       |
|Slovenština|    `sk`    |
|Slovinština| `sl`    |
|Španělština|   `es`    |
|Švédština|   `sv`    |
|Tahitian|  `ty`    |
|Thajština|  `th`    |
|Turečtina|   `tr`        |
|Ukrajinština| `uk`    |
|Urdština|  `ur`    |
|Vietnamština|    `vi`    |
|Velština| `cy`    |
|Yucatec Maya|  `yua`   |

### <a name="transliterate"></a>Transliterace

Metoda přepisu podporuje následující jazyky. V "do/z", "<-->" označuje, že jazyk lze přepřepisovat z nebo na některý z uvedených skriptů. "-->" označuje, že jazyk lze přepřepisovat pouze z jednoho skriptu na druhý.

[Zobrazit referenční dokumentaci k operaci přepřepisu](reference/v3-0-translate.md)


| Jazyk    | Kód jazyka | Skript | Směr | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabština | `ar` | Arabština `Arab` | <--> | Znak `Latn` |
| Bengálština  | `bn` | Bengálština `Beng` | <--> | Znak `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Znak `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Tradiční čínština `Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Znak `Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Zjednodušená čínština `Hans` |
| Gudžarátština | `gu`  | Gudžarátština `Gujr` | --> | Znak `Latn` |
| Hebrejština | `he` | Hebrejština `Hebr` | <--> | Znak `Latn` |
| Hindština | `hi` | Koncový `Deva` | <--> | Znak `Latn` |
| Japonština | `ja` | Japonština `Jpan` | <--> | Znak `Latn` |
| Kannadština | `kn` | Kannadština `Knda` | --> | Znak `Latn` |
| Malajalámština | `ml` | Malajalámština `Mlym` | --> | Znak `Latn` |
| Maráthština | `mr` | Koncový `Deva` | --> | Znak `Latn` |
| Udijština | `or` | Krí `Orya` | <--> | Znak `Latn` |
| Paňdžábština | `pa` | Západní `Guru`  | <--> | Znak `Latn`  |
| Srbština (cyrilice) | `sr-Cyrl` | B `Cyrl`  | --> | Znak `Latn` |
| Srbština (latinka) | `sr-Latn` | Znak `Latn` | --> | B `Cyrl`|
| Tamilština | `ta` | Tamilština `Taml` | --> | Znak `Latn` |
| Telugština | `te` | Telugština `Telu` | --> | Znak `Latn` |
| Thajština | `th` | Thajština `Thai` | --> | Znak `Latn` |

### <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky pro nebo z angličtiny pomocí vyhledávacích a ukázkových metod.

Podívejte se na referenční dokumentaci pro [hledání slovníku](reference/v3-0-dictionary-lookup.md) a [Příklady operací slovníku](reference/v3-0-dictionary-examples.md) .

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| Arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Katalánština      | `ca`          |
| Čínština (zjednodušená)      | `zh-Hans`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| Dánština      | `da`          |
| Nizozemština      | `nl`          |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Macek      | `mww`          |
| Maďarština      | `hu`          |
| Islandština    | `is`  |
| Indonéština      | `id`          |
| Italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie)     | `pt-br`          |
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Švédština      | `sv`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Přístup k seznamu jazyků překladatele prostřednictvím kódu programu

Seznam podporovaných jazyků pro překladatele můžete načíst pomocí metody languages. Můžete zobrazit seznam podle funkcí, kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Služba Microsoft Translator automaticky aktualizuje tento seznam, protože jsou k dispozici nové jazyky.

[Referenční dokumentace operací zobrazení jazyků](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro přizpůsobení nebo z angličtiny pomocí [vlastního překladatele](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Čínština (zjednodušená)      | `zh-Hans`          |
|Čínština (tradiční)|   `zh-Hant`   |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| Dánština      | `da`          |
| Nizozemština      | `nl`          |
| Angličtina    | `en`     |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Gudžarátština| `gu`    |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Maďarština      | `hu`          |
| Islandština | `is` |
| Indonéština|   `id`    |
| Irština | `ga`  |
| Italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština|    `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašština| `mg`    |
| Malajština|    `ms`        |
| Maorština| `mi`  |
| Maráthština| `mr`  |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie) | `pt-br` |
| Paňdžábština|`pa`|
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Samoan|   `sm`    |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Švédština      | `sv`          |
| Thajština      | `th`          |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština| `ur`    |
| Vietnamština      | `vi`          |
| Velština | `cy` |

## <a name="speech-translation"></a>Překlad řeči
Překlad řeči je k dispozici pomocí překladatele se službou Cognitive Services Speech. V [dokumentaci ke službě Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/) najdete další informace o použití překladu řeči a k zobrazení všech [dostupných jazykových možností](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support).

### <a name="speech-to-text"></a>Převod řeči na text
Převod řeči na text, aby se přeložil do jazyka textu dle vašeho výběru. Převod řeči na text se používá pro převod řeči na text nebo pro překlad řeči na řeč při použití ve spojení s syntézou řeči.

| Jazyk    |
|:----------- |
|Arabština|
|Kantonština (tradiční)|
|Katalánština|
|Čínština (zjednodušená)|
|Čínština (tradiční)|
|Dánština|
|Nizozemština|
|Angličtina|
|Finština|
|Francouzština|
|Němčina|
|Gudžarátština|
|Hindština|
|Italština|
|Japonština|
|Korejština|
|Maráthština|
|Norština|
|Polština|
|Portugalština (Brazílie)|
|portugalština (Portugalsko)|
|Ruština|
|Španělština|
|Švédština|
|Tamilština|
|Telugština|
|Thajština|
|Turečtina|

### <a name="text-to-speech"></a>Převod textu na řeč
Převod textu na řeč Převod textu na řeč se používá k přidání zvukového výstupu výsledků překladu nebo převodu řeči na řeč při použití s převodem na text. 

| Jazyk    |
|:----------- |
|Arabština|
|Bulharština|
|Kantonština (tradiční)|
|Katalánština|
|Čínština (zjednodušená)|
|Čínština (tradiční)|
|Chorvatština|
|Čeština|
|Dánština|
|Nizozemština|
|Angličtina|
|Finština|
|Francouzština|
|Němčina|
|Řečtina|
|Hebrejština|
|Hindština|
|Maďarština|
|Indonéština|
|Italština|
|Japonština|
|Korejština|
|Malajština|
|Norština|
|Polština|
|Portugalština (Brazílie)|
|portugalština (Portugalsko)|
|Rumunština|
|Ruština|
|Slovenština|
|Slovinština|
|Španělština|
|Švédština|
|Tamilština|
|Telugština|
|Thajština|
|Turečtina|
|Vietnamština|

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Zobrazit seznam jazyků na webu Microsoft Translator

Pokud chcete rychle zobrazit jazyky, na webu Microsoft Translator se zobrazí všechny jazyky, které Překladatel podporuje pro překlad textu a službu Speech pro překlad řeči. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
