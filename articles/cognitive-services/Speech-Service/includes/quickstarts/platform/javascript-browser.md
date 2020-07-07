---
title: 'Rychlý Start: sada Speech SDK pro nastavení platformy JavaScript (browser) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro použití JavaScriptu (prohlížeče) se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 53cabaec21daafb910b958bb4b573dd033ca6283
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035735"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro JavaScript pro použití s webovou stránkou.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Vytvoření složky nového webu

Vytvořte novou prázdnou složku. Pokud chcete ukázku hostovat na webovém serveru, ujistěte se, že má webový server k této složce přístup.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozbalení sady Speech SDK pro JavaScript do příslušné složky

Stáhněte si sadu Speech SDK jako [balíček .zip](https://aka.ms/csspeech/jsbrowserpackage) a rozbalte ho do nově vytvořené složky. To má za následek pět souborů, které jsou rozbalené:
* `microsoft.cognitiveservices.speech.sdk.bundle.js`Verze sady Speech SDK pro lidskou čitelnost.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map`Soubor mapy používaný pro ladění kódu sady SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts`Definice objektů pro použití s TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js`Minifikovaného verze sady Speech SDK.
* `speech-processor.js`Kód pro zlepšení výkonu v některých prohlížečích.

## <a name="create-an-indexhtml-page"></a>Vytvoření stránky index.html

Vytvořte ve složce nový soubor `index.html` a otevřete ho v textovém editoru.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]