---
title: Jazyková podpora – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam přirozených jazyků podporovaných funkcemi Počítačové zpracování obrazu. Optické rozpoznávání znaků, analýza obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 649f1e6cf79aa17d053cc547debf5232120bb4fe
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760630"
---
# <a name="language-support-for-computer-vision"></a>Jazyková podpora pro Počítačové zpracování obrazu

Některé funkce Počítačové zpracování obrazu podporují více jazyků. všechny funkce, které tady nejsou uvedené, podporují jenom angličtinu.

## <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (OCR)

Rozhraní API pro optické Počítačové zpracování obrazu podporují několik jazyků. Nevyžadují, abyste zadali kód jazyka. Další informace naleznete v tématu [optické rozpoznávání znaků (OCR)](concept-recognizing-text.md) .

|Jazyk| Kód jazyka | ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ | Číst v 3.0 | Verze Public Preview pro čtení v 3.1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabština | `ar`|✔ | | |
|Čínština (zjednodušená) | `zh-Hans`|✔ | |✔ |
|Čínština (tradiční) | `zh-Hant`|✔ | | |
|Čeština | `cs` |✔ | | |
|Dánština | `da` |✔ | | |
|Nizozemština | `nl` |✔ |✔ |✔ |
|Angličtina | `en` |✔ |✔ |✔ |
|Finština | `fi` |✔ | | |
|Francouzština | `fr` |✔ |✔ |✔ |
|Němčina | `de` |✔ |✔ |✔ |
|Řečtina | `el` |✔ | | |
|Maďarština | `hu` |✔ | | |
|Italština | `it` |✔ |✔ |✔ |
|Japonština | `ja` |✔ | | |
|Korejština | `ko` |✔ | | |
|Norština | `nb` |✔ | | |
|Polština | `pl` |✔ | | |
|Portugalština | `pt` |✔ |✔ |✔ |
|Rumunština | `ro` |✔ | | |
|Ruština | `ru` |✔ | | |
|Srbština (cyrilice) | `sr-Cyrl` |✔ | | |
|Srbština (latinka) | `sr-Latn` |✔ | | |
|Slovenština | `sk` |✔ | | |
|Španělština | `es` |✔ |✔ |✔ |
|Švédština | `sw` |✔ | | |
|Turečtina | `tr` |✔ | | |

## <a name="image-analysis"></a>Analýza obrázků

Některé akce rozhraní API [analyzovat-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mohou vracet výsledky v jiných jazycích, které jsou zadány pomocí `language` parametru dotazu. Jiné akce vrátí výsledky v angličtině bez ohledu na to, jaký jazyk je zadán, a jiné vyvolají výjimku pro nepodporované jazyky. Akce jsou zadány s `visualFeatures` `details` parametry dotazu a. Další informace najdete v [přehledu](home.md) o všech akcích, které můžete provádět s analýzou obrázků.

|Jazyk | Kód jazyka | Kategorie | Značky | Popis | Pro dospělé | Značky | Color | Tváře | ImageType | Objekty | Celebrity | Orientační body tváře |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Čínština | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angličtina | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonština | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalština | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Španělština | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Další kroky

Začněte využívat Počítačové zpracování obrazu funkce, které jsou uvedené v této příručce.

* [Analýza místní Image (REST)](./quickstarts/csharp-analyze.md)
* [Extrahovat vytištěný text (REST)](./quickstarts/csharp-print-text.md)
