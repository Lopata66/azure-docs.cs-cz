---
title: 'Rychlý Start: rozpoznávání řeči uložených v BLOB Storage C++ , – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 313332689b0f2df9698f868297c72be7d8bde5bb
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829063"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v sadě Visual Studio

Prvním krokem je ujistit se, že máte projekt otevřený v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `helloworld.cpp`.

## <a name="add-a-references"></a>Přidat odkazy

K urychlení vývoje kódu budeme používat několik externích komponent:
* [Cpp REST SDK](https://github.com/microsoft/cpprestsdk) Klientská knihovna pro volání REST pro službu REST.
* [nlohmann/JSON](https://github.com/nlohmann/json) Užitečnou knihovnu JSON pro analýzu/serializaci/deserializaci.

Obě můžou být nainstalované pomocí [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(Hodnoty `YourSubscriptionKey`, `YourServiceRegion`a `YourFileUrl` budete muset nahradit vlastními hodnotami.)

## <a name="json-wrappers"></a>Obálky JSON

Jak REST API přijímá požadavky ve formátu JSON a také vrátí výsledky ve formátu JSON, můžeme s nimi pracovat jenom s řetězci, ale to se nedoporučuje.
Aby bylo možné žádosti a odpovědi snadněji spravovat, deklarujeme několik tříd, které se použijí k serializaci/deserializaci formátu JSON a některých metod, které vám pomůžou nlohmann/JSON.

Pokračujte a před `recognizeSpeech`uveďte jejich deklarace.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento kód do `recognizeSpeech` [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento kód do `recognizeSpeech` [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat žádost a ověřit její stav
Nyní pošleme požadavek službě rozpoznávání řeči a zkontrolujeme kód prvotní odezvy. Tento kód odpovědi jednoduše indikuje, jestli služba požadavek přijala. Služba vrátí adresu URL v hlavičkách odpovědi, kde je umístění, kde bude uložený stav přepisu.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte, až se přepis dokončí.
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme dotazovat se na jeho stav, a to v každém případě často. Zkontrolujeme každých 5 sekund.

Stav můžeme zjistit načtením obsahu na adrese URL, kterou jsme dostali při odeslání žádosti. Když získáme obsah zpátky, deserializovatme ho do jedné z našich pomocných tříd, aby bylo snazší pracovat s.

Toto je kód pro cyklické dotazování se zobrazením stavu pro vše s výjimkou úspěšného dokončení. provedeme to ještě dál.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Po úspěšném dokončení přepisu budou výsledky uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Stáhneme obsah této adresy URL, deserializaci JSON a projdete výsledky vytištěním zobrazovaného textu.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Kontrolovat kód
V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]