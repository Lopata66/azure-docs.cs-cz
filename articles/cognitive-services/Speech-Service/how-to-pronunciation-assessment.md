---
title: Jak používat sadu Speech SDK pro vyhodnocení výslovnosti
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK podporuje vyhodnocení výslovnosti, které posuzuje kvalitu výslovnosti vstupu řeči s indikátory přesnosti, Fluency, úplností atd.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 8602d43113f4ce21cdb430e1fa3e83f006c64753
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185559"
---
# <a name="pronunciation-assessment"></a>Posouzení výslovnosti

Posouzení výslovnosti vyhodnocuje výslovnost řeči a dává mluvčí názory na přesnost a Fluency mluveného zvuku.
S hodnocením výslovnosti mohou jazyky naučit postupovat, získávat okamžitou zpětnou vazbu a zlepšovat jejich výslovnost, aby mohli mluvit a prezentovat s jistotou.
Pedagogé můžou využít schopnost vyhodnotit výslovnost více mluvčích v reálném čase.

V tomto článku se dozvíte, jak nastavit `PronunciationAssessmentConfig` a načíst `PronunciationAssessmentResult` pomocí sady Speech SDK.

> [!NOTE]
> Funkce hodnocení výslovnosti je aktuálně dostupná jenom v oblastech `westus` `eastasia` a `centralindia` podporuje jenom jazyk `en-US` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Posouzení výslovnosti pomocí sady Speech SDK

V níže uvedených ukázkách vytvoříte a `PronunciationAssessmentConfig` pak použijete na `SpeechRecognizer` .

Následující fragmenty kódu ukazují, jak používat automatické rozpoznávání jazyka ve vašich aplikacích:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronResult.pronunciationScore;
        var wordLevelResult = pronResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parametry konfigurace vyhodnocení výslovnosti

Tato tabulka uvádí parametry konfigurace pro posouzení výslovnosti.

| Parametr | Popis | Povinné? |
|-----------|-------------|---------------------|
| ReferenceText | Text, proti kterému bude výslovnost vyhodnocena. | Vyžadováno |
| GradingSystem | Systém bodů pro kalibraci skóre. `FivePoint`Systém poskytne skóre 0-5 s plovoucí desetinnou čárkou a `HundredMark` poskytne 0-100 skóre s plovoucí desetinnou čárkou. Výchozí: `FivePoint`. | Volitelné |
| Členitost | Členitost vyhodnocení. Přípustné hodnoty jsou, což zobrazuje skóre pro úplný text, na úrovni aplikace Word a foném, ve kterém se zobrazuje skóre `Phoneme` `Word` pro úplný text a na úrovni slova, ve `FullText` kterém se zobrazuje skóre pouze na úrovni celého textu. Výchozí: `Phoneme`. | Volitelné |
| EnableMiscue | Povolí výpočet miscue. Když je tato možnost povolená, vyslovované slova se porovnají s referenčním textem a budou označená vynechání nebo vložení na základě porovnání. Přijaté hodnoty jsou `False` a `True` . Výchozí: `False`. | Volitelné |
| ScenarioId | Identifikátor GUID označující systém přizpůsobeného bodu. | Volitelné |

### <a name="pronunciation-assessment-result-parameters"></a>Parametry výsledku vyhodnocení výslovnosti

Tato tabulka obsahuje seznam výsledných parametrů pro vyhodnocení výslovnosti.

| Parametr | Popis |
|-----------|-------------|
| `AccuracyScore` | Přesnost řeči v výslovnosti. Přesnost určuje, jak úzce se fonémy shoduje s výslovností nativního mluvčího. Skóre přesnosti aplikace Word a úplného textu je agregované ze foném skóre úrovně. |
| `FluencyScore` | Fluency zadaného řeči. Fluency označuje, jak blízkoně řeč odpovídá použití tichého zalomení mezi slovy v nativním mluvčím. |
| `CompletenessScore` | Úplnost rozpoznávání řeči určené výpočtem poměru slov, která jsou vyhodnocena jako odkaz na textové zadání. |
| `PronunciationScore` | Celkové skóre označující kvalitu výslovnosti daného řeči. To je agregované z `AccuracyScore` `FluencyScore` a `CompletenessScore` s váhou. |
| `ErrorType` | Tato hodnota označuje, zda je slovo vynecháno, vloženo nebo špatně vyslovované, ve srovnání s `ReferenceText` . Možné hodnoty jsou `None` (to znamená, že toto slovo neobsahuje žádnou chybu), `Omission` `Insertion` a `Mispronunciation` . |

## <a name="next-steps"></a>Další kroky

<!-- TODO: update JavaScript sample links after release -->

::: zone pivot="programming-language-csharp"
* Posouzení výslovnosti najdete v [ukázkovém kódu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) na GitHubu.
::: zone-end

::: zone pivot="programming-language-cpp"
* Posouzení výslovnosti najdete v [ukázkovém kódu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) na GitHubu.
::: zone-end

::: zone pivot="programming-language-java"
* Posouzení výslovnosti najdete v [ukázkovém kódu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) na GitHubu.
::: zone-end

::: zone pivot="programming-language-python"
* Posouzení výslovnosti najdete v [ukázkovém kódu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) na GitHubu.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Posouzení výslovnosti najdete v [ukázkovém kódu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) na GitHubu.
::: zone-end

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
