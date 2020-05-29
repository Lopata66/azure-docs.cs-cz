---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: b842084d00c1ce8ec347994371a55c97b89ba54f
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140698"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace V3](https://aka.ms/azsdk-python-textanalytics-ref-docs)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  knihovny v3 [balíček V3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics)  |  knihovny v2 [balíček v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/)  |  [ukázky v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), který obsahuje příklady kódu v tomto rychlém startu. 

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), který obsahuje příklady kódu v tomto rychlém startu. 

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu a vytvořte proměnné pro koncový bod a klíč předplatného prostředku Azure.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektový model

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod, jak analyzovat text jako dávku. 

Když je text dávkového zpracování odeslán do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

Objekt Response je seznam obsahující informace o analýze pro každý dokument. 

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí několik metod analýzy textu, jako jeden řetězec nebo dávku. 

Text se pošle do rozhraní API jako seznam `documents` objektů, což jsou `dictionary` objekty obsahující kombinaci `id` `text` atributů, a `language` v závislosti na použité metodě. `text`Atribut ukládá text, který má být analyzován v původním umístění `language` , a `id` může být libovolná hodnota. 

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Analýza textu klientské knihovny pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner) 
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte funkci pro vytvoření instance `TextAnalyticsClient` objektu pomocí `key` a `endpoint` vytvořeného výše. Pak vytvořte nového klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Vytvořte funkci pro vytvoření instance `TextAnalyticsClient` objektu pomocí `key` a `endpoint` vytvořeného výše. Pak vytvořte nového klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `sentiment_analysis_example()` , která převezme klienta jako argument, a pak zavolá `analyze_sentiment()` funkci. Vrácený objekt Response bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Výstup

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Ověřte objekt klienta a zavolejte funkci [mínění ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) . Iterujte výsledky a vytiskněte ID každého dokumentu a mínění skóre. Skóre Blíže k 0 označuje negativní mínění, zatímco skóre Blíže k hodnotě 1 označuje kladný mínění.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `language_detection_example()` , která převezme klienta jako argument, a pak zavolá `detect_language()` funkci. Vrácený objekt Response bude obsahovat nalezený jazyk v `primary_language` případě úspěchu a v `error` případě potřeby.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `country_hint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Výstup

```console
Language:  French
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak projdete výsledky a vytiskněte ID každého dokumentu a první vrácený jazyk.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Výstup

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

> [!NOTE]
> Ve verzi `3.0` : 
> * Odkaz na entitu je samostatný požadavek než NER.

Vytvořte novou funkci s názvem `entity_recognition_example` , která převezme klienta jako argument, poté zavolá `recognize_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entity` případě úspěchu, a `error` Pokud ne. U každé zjištěné entity vytiskněte kategorii a podkategorii, pokud existuje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Výstup

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

## <a name="entity-linking"></a>Entity Linking

Vytvořte novou funkci s názvem `entity_linking_example()` , která převezme klienta jako argument, poté zavolá `recognize_linked_entities()` funkci a projde výsledky. Vrácený objekt Response bude obsahovat seznam zjištěných entit v `entities` případě úspěchu, a `error` Pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `entity` objektu jako seznam `match` objektů.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Výstup

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Pomocí dříve vytvořeného klienta volejte funkci [Entities ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a entity, které jsou v něm obsažené.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Výstup

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází


#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `key_phrase_extraction_example()` , která převezme klienta jako argument, a pak zavolá `extract_key_phrases()` funkci. Výsledek bude obsahovat seznam zjištěných klíčových frází v `key_phrases` případě úspěchu a v `error` případě potřeby. Vytiskněte všechny zjištěné klíčové fráze.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Výstup

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Pomocí dříve vytvořeného klienta volejte funkci [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) a získejte výsledek. Pak Projděte výsledky a vytiskněte ID každého dokumentu a klíčové fráze, které jsou v něm obsažené.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Výstup

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---
