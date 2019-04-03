---
title: Zpráva k vydání verze – Service rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Zpráva k vydání verze pro službu rozhraní API pro rozpoznávání tváře patří historii změn pro vydání verze pro různé verze.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: yluiu
ms.openlocfilehash: 02e4e73bbbc7f843a3483bab4791484b89f97dd7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862025"
---
# <a name="face-api-release-notes"></a>Zpráva k vydání verze rozhraní API pro rozpoznávání tváře

V tomto článku se vztahují na službu rozhraní API pro rozpoznávání tváře verze 1.0.

### <a name="release-changes-in-march-2019"></a>Vydávejte změny 2019. března

* Přidat nový model rozpoznávání tváře s zpřesnění. Použít prostřednictvím [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [jeden objekt PersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) a [ LargePersonGroup – vytvořit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) tak, že zadáte nový název model rozpoznávání tváře `recognition_02` v `recognitionModel` parametru. Další informace naleznete v [určení model rozpoznávání](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Vydávejte změny 2019. ledna

* Snímek funkce pro podporu migrace dat mezi předplatnými: [Snímek](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot). Další informace naleznete v [jak migrovat vaše data rozpoznávání tváře do jiného předplatného pro rozpoznávání tváře](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Vydávejte změny. října 2018

* Popis pro kontrast `status`, `createdDateTime`, `lastActionDateTime`, a `lastSuccessfulTrainingDateTime` v [jeden objekt PersonGroup – získání stavu školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup – získání stavu školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)a [ LargeFaceList - získá stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Verze změny v květnu 2018

* Vylepšené `gender` atribut výrazně a také vylepšené `age`, `glasses`, `facialHair`, `hair`, `makeup` atributy. Používat je prostřednictvím [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru. 

* Limit pro velikost souboru zvýšenou vstupního obrázku z 4 MB na 6 MB [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [jeden objekt PersonGroup uživatel – přidat Rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a [osoba LargePersonGroup – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Vydávejte změny. března 2018

* Přidání Miliónové škále kontejneru: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) a [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Další informace naleznete v [jak používat funkci ve velkém měřítku](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zvýšit [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametr z [1, 5] na [1, 100] a výchozí až 10.

### <a name="release-changes-in-may-2017"></a>Vydávejte změny. května 2017

* Přidání `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, a `noise` atributů [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru.

* 10 tisíc osob v jeden objekt PersonGroup podporovány a [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Nepodporuje stránkování v [jeden objekt PersonGroup uživatele – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) s volitelnými parametry: `start` a `top`.

* Podporované souběžnosti v tváří proti různých FaceLists a jiné osoby v jeden objekt PersonGroup přidání nebo odstranění.

### <a name="release-changes-in-march-2017"></a>Verze změny v březnu 2017
* Přidání `emotion` atribut [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru.

* Oprava, typ písma nelze znovu rozpoznáno s obdélníku, který vrací z [pro rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako `targetFace` v [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Pevná velikost zjistitelné pro rozpoznávání tváře abyste měli jistotu, že jde o nezbytnou mezi 36 x 36 do 4096 × 4096 pixelů.

### <a name="release-changes-in-november-2016"></a>Změny verze v listopadu 2016
* Přidání standardní úložiště pro rozpoznávání tváře předplatnému uložit další trvalé tváře při použití [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) nebo [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pro identifikaci nebo hledání podobnosti odpovídající. Za 1 000 uložených obrázků tváří se účtuje 0,5 USD. Tato sazba se každý den poměrně přepočítává. Předplatná na úrovni Free i nadále omezený na celkem 1 000 osob.

### <a name="release-changes-in-october-2016"></a>Změny vydání v říjnu 2016
* Změnil se chybová zpráva z více než jeden tvář targetFace z "existuje více než jeden tváří na obrázku' do 'Je více než jeden tváří na obrázku' [FaceList – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) a [jeden objekt PersonGroup uživatele – přidání rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Vydávejte změny. července 2016
* Podporované pro rozpoznávání tváře ověřování objektu osoba v [pro rozpoznávání tváře – ověření](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Přidat volitelné `mode` parametr povoluje výběr ze dvou režimů používaných pracovní: `matchPerson` a `matchFace` v [rozpoznávání tváře – Najít podobné](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a výchozí hodnota je `matchPerson`.

* Přidat volitelné `confidenceThreshold` parametr pro uživatele nastavit mezní hodnota určuje, zda jeden pro rozpoznávání tváře patří do objektu osoba v [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Přidat volitelné `start` a `top` parametry v [jeden objekt PersonGroup – seznam](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) umožňující uživateli zadat počáteční bod a celkového počtu objektů Persongroup do seznamu.

### <a name="v10-changes-from-v0"></a>Verze 1.0 se změní z V0
* Aktualizovat koncový bod služby root z ```https://westus.api.cognitive.microsoft.com/face/v0/``` k ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Změny se použily: [Rozpoznávání tváře – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [pro rozpoznávání tváře – identifikace](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [pro rozpoznávání tváře – vyhledání podobných](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) a [pro rozpoznávání tváře – seskupit](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Aktualizuje velikost minimální zjistitelné pro rozpoznávání tváře na 36 x 36 pixelů. Nerozpozná tváří menší než 36 x 36 pixelů.

* Zastaralá data jeden objekt PersonGroup a osoby v V0 pro rozpoznávání tváře. Data nelze přistupovat pomocí služby pro rozpoznávání tváře V1.0.

* Zastaralé V0 koncový bod rozhraní API pro rozpoznávání tváře na 30. června 2016.
