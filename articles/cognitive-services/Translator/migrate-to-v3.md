---
title: Migrace na V3 – Translator Text API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat z V2 na V3 Translator Text API.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: fa1733ddd70de3f8ef93452a880094ccf8b9ef56
ms.sourcegitcommit: 807c318f5c034f8256f91c241e9d6f8f4d7de90a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951472"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 na V3 migrace

> [!NOTE]
> 30. dubna 2018 se přestala nabízet v2. Migrujte prosím svoje aplikace na V3, abyste mohli využívat nové funkce je dostupná výhradně ve verzi 3.
> 
> Centrum Microsoft Translator se vyřadí 17. května 2019. [Zobrazit informace o migraci důležité a data](https://www.microsoft.com/translator/business/hub/).  

Tým Microsoft Translatoru vydala rozhraní Translator Text API verze 3 (V3). Tato verze obsahuje nové funkce, nepoužívané metody a nový formát pro odesílání a přijímání dat ze služby Microsoft Translator. Tento dokument obsahuje informace pro změnu aplikace, aby používaly V3. 

Konci tohoto dokumentu obsahuje užitečné odkazy pro vás další informace.

## <a name="summary-of-features"></a>Přehled funkcí

* Žádné trasování – v V3 bez trasování se vztahuje na všechny cenové úrovně na webu Azure Portal. Tato funkce znamená, že žádný text, odešle do rozhraní API V3 se uloží společností Microsoft.
* JSON – XML je nahrazena JSON. Všechna data ve službě odeslané a přijaté ze služby je ve formátu JSON.
* Více jazyků cíl v jednom požadavku – The přeložit metoda přijímá několik jazyků 'do' pro překlad v jedné žádosti. Jeden požadavek může být třeba angličtina z a do, němčina, španělština a japonština nebo jiná skupina jazyků.
* Dvojjazyčný slovník – metoda dvojjazyčného slovníku se přidala rozhraní API. Tato metoda zahrnuje "vyhledávání" a "Příklady".
* Transkripce – metoda transliterate byl přidán do rozhraní API. Tato metoda převede slov a vět jeden skript (například Arabské) do jiného skriptu (například Latinka).
* Jazyky – novou metodu "v jazycích" poskytuje informace jazyk, ve formátu JSON pro použití s "přeložit", "slovník" a "transkripce" metody.
* Nové přeložit – nové funkce byly přidány k metodě "přeložit" pro podporu některých funkcí, které byly v rozhraní API V2 jako samostatné metody. Příkladem je TranslateArray.
* Metoda mluvit – funkce pro převod textu na řeč je již nejsou podporovány v rozhraní Microsoft Translator API. Převod textu na řeč funkce jsou dostupné v [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Následující seznam metod V2 a V3 identifikuje rozhraní API, která poskytne funkci, která byla součástí V2 a V3 metody.

| V2 Metoda API   | V3 Kompatibilitu s rozhraními API |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Jazyky](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Jazyky](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Zjištění](reference/v3-0-detect.md)         |
| `DetectArray`     | [Zjištění](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkce se už nepodporuje.       |
| `AddTranslationArray`    | Funkce se už nepodporuje.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkce se už nepodporuje.         |
| `GetTranslationsArray`      | Funkce se už nepodporuje.         |

## <a name="move-to-json-format"></a>Přesunout do formátu JSON

Microsoft Translator Text překlad V2 přijmout a vrátil data ve formátu XML. Ve verzi 3 všech dat odeslaných a přijatých pomocí rozhraní API je ve formátu JSON. XML se už přijata nebo vráceny ve verzi 3.

Tato změna bude týkat několik aspektů aplikace napsané pro rozhraní API pro překlad textu V2. Jako příklad: Rozhraní API pro jazyky vrátí informace o jazyka pro překlady textů, přepis a metody dvě slovníku. Můžete požádat o všechny informace o jazyk pro všechny metody v jednom volání nebo o ně požádat samostatně.

Metoda jazyky nevyžaduje ověření; Po kliknutí na následující odkaz zobrazí všechny informace o jazyku pro V3 ve formátu JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ověřovací klíč

Pro V3 se nebudou přijímat ověřovací klíč, který používáte pro V2. Nemusíte si nové předplatné. Budete moci kombinovat V2 a V3 ve vašich aplikacích v průběhu migrace yearlong usnadnit vydání nové verze, zatímco stále migrujete z V2 XML na V3 JSON.

## <a name="pricing-model"></a>Cenový model

Microsoft Translator V3 se účtuje podle stejným způsobem, který se stanoví V2; na znak včetně mezer. Nové funkce ve verzi 3 provést nějaké změny v jaké znaky se počítají pro fakturaci.

| Metoda v3   | Znaky počítá fakturace |
|:----------- |:-------------|
| `Languages`     | Odeslané žádné znaky, none nepočítají, žádné poplatky.          |
| `Translate`     | Počet je založen na tom, kolik znaků se odešlou pro překlad a kolik jazycích jsou znaky přeloženy do. odeslání 50 znaků a 5 jazyky požadovaný bude 50 × 5.           |
| `Transliterate`     | Počet odeslaných k transkripci znaků, které se počítají.         |
| `Dictionary lookup & example`     | Se počítají počet znaků, odešle ke slovníku lookup a examples.         |
| `BreakSentence`     | Bez poplatků.       |
| `Detect`     | Bez poplatků.      |

## <a name="v3-end-points"></a>V3 koncové body

Globální

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metody překlady text v3 API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilita a přizpůsobení

> [!NOTE]
> 
> Centrum Microsoft Translator se vyřadí 17. května 2019. [Zobrazit informace o migraci důležité a data](https://www.microsoft.com/translator/business/hub/).   

Ve výchozím nastavení používá Microsoft Translator V3 Neurální strojový překlad. V důsledku toho jej nelze použít v centru Microsoft Translator. Překladač rozbočovače podporuje pouze starší verze statistické strojového překladu. Přizpůsobení pro Neurální překladové je nyní dostupné s použitím překladač vlastní. [Další informace o přizpůsobení Neurální strojový překlad](custom-translator/overview.md)

Neurální překladové s V3 text API nepodporuje použití standardní kategorie (SMT, řeči, odborný, generalnn).

| |Koncový bod|    Dodržování předpisů GDPR procesoru|  Použití centra Translator| Použít vlastní Translator (Preview)|
|:-----|:-----|:-----|:-----|:-----|
|Translator Text API Version 2| api.microsofttranslator.com|    Ne  |Ano    |Ne|
|Translator Text API Version 3| api.cognitive.microsofttranslator.com|  Ano|    Ne| Ano|

**Translator Text API Version 3**
* Je obecně dostupné a plně podporovaná.
* Splňuje všechny požadavky 20001 a 20018 a SOC 3 certifikace ISO je jako zpracovatel dodržovat nařízení GDPR. 
* Umožňuje vyvolat systémů překladu neuronové sítě, které jste upravili s vlastní překlady (Preview), nové funkce Translator NMT vlastního nastavení. 
* Neposkytuje přístup k vlastní překlad systémy vytvořené pomocí Microsoft Translatoru Hub.

Pokud používáte api.cognitive.microsofttranslator.com koncový bod používáte verze 3 Translator Text API.

**Translator Text API Version 2**
* Nevyhovuje všechny ISO 20001,20018 a požadavky na certifikaci SOC 3. 
* Neumožňuje k vyvolání systémů překladu neuronové sítě, které můžete přizpůsobit pomocí funkce Translator vlastního nastavení.
* Poskytuje přístup k vlastní překlad systémy vytvořené pomocí Microsoft Translatoru Hub.
* Pokud používáte api.microsofttranslator.com koncový bod používáte verze 2 rozhraní Translator Text API.

Žádná verze Translator API vytvoří záznam překlady. Překlady jsou nikdy sdílet s kýmkoli. Další informace o [bez trasování Translator](https://www.aka.ms/NoTrace) webové stránky.

## <a name="links"></a>Odkazy

* [Zásady ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)
* [Právní informace týkající se Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Podmínky online služeb](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazit dokumentaci verze 3.0](reference/v3-0-reference.md)
