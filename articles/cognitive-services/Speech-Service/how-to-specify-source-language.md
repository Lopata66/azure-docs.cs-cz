---
title: Určení zdrojového jazyka pro převod řeči na text
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK umožňuje určit zdrojový jazyk při převodu řeči na text. Tento článek popisuje, jak pomocí metod FromConfig a SourceLanguageConfig, aby služba rozpoznávání řeči znala zdrojový jazyk a poskytovala vlastní cíl modelu.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 3d761bbad4cb2cd2cdd1c34459f25c811bb41c7e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918685"
---
# <a name="specify-source-language-for-speech-to-text"></a>Určení zdrojového jazyka pro převod řeči na text

V tomto článku se dozvíte, jak určit zdrojový jazyk pro zvukový vstup předaný do sady Speech SDK pro rozpoznávání řeči. Kromě toho je k dispozici vzorový kód pro určení vlastního modelu řeči pro lepší rozpoznávání.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Určení zdrojového jazyka v jazyce C #

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí `SpeechRecognizer` konstrukce.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je k dispozici zdrojový jazyk pomocí `SourceLanguageConfig` . Pak `sourceLanguageConfig` je předána jako parametr pro `SpeechRecognizer` konstrukci.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig` . Pak `sourceLanguageConfig` je předána jako parametr pro `SpeechRecognizer` konstrukci.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` a `EndpointId` metody set jsou zastaralé z `SpeechConfig` třídy v jazyce C#. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Určení zdrojového jazyka v jazyce C++

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí `FromConfig` metody.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je k dispozici zdrojový jazyk pomocí `SourceLanguageConfig` . Pak `sourceLanguageConfig` je `FromConfig` při vytváření rozhraní předána jako parametr `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig` . `sourceLanguageConfig`Je předán jako parametr `FromConfig` při vytváření `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` a `SetEndpointId` jsou zastaralé metody ze `SpeechConfig` třídy v jazyce C++ a Java. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Určení zdrojového jazyka v jazyce Java

V tomto příkladu je zdrojový jazyk k dispozici explicitně při vytváření nového `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je k dispozici zdrojový jazyk pomocí `SourceLanguageConfig` . Pak `sourceLanguageConfig` je při vytváření nového nového předán jako parametr `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig` . Pak `sourceLanguageConfig` je při vytváření nového nového předán jako parametr `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` a `setEndpointId` jsou zastaralé metody ze `SpeechConfig` třídy v jazyce C++ a Java. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Určení zdrojového jazyka v Pythonu

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí `SpeechRecognizer` konstrukce.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

V tomto příkladu je k dispozici zdrojový jazyk pomocí `SourceLanguageConfig` . Pak `SourceLanguageConfig` je předána jako parametr pro `SpeechRecognizer` konstrukci.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig` . Pak `SourceLanguageConfig` je předána jako parametr pro `SpeechRecognizer` konstrukci.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` a `endpoint_id` vlastnosti jsou zastaralé ze `SpeechConfig` třídy v Pythonu. Použití těchto vlastností se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak určit zdrojový jazyk v JavaScriptu

Prvním krokem je vytvoření `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

V dalším kroku určete zdrojový jazyk vašeho zvuku `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Pokud používáte vlastní model pro rozpoznávání, můžete koncový bod zadat pomocí `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Určení zdrojového jazyka v cíli – C

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí `SPXSpeechRecognizer` konstrukce.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

V tomto příkladu je k dispozici zdrojový jazyk pomocí `SPXSourceLanguageConfiguration` . Pak `SPXSourceLanguageConfiguration` je předána jako parametr pro `SPXSpeechRecognizer` konstrukci.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SPXSourceLanguageConfiguration` . Pak `SPXSourceLanguageConfiguration` je předána jako parametr pro `SPXSpeechRecognizer` konstrukci.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` a `endpointId` vlastnosti jsou zastaralé ze `SPXSpeechConfiguration` třídy v cíli-C. Použití těchto vlastností se nedoporučuje a nemělo by se používat při sestavování `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Viz také

* Seznam podporovaných jazyků a národních prostředí pro převod řeči na text najdete v tématu [Podpora jazyků](language-support.md).

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)