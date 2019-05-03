---
title: Dovednosti kognitivního vyhledávání extrakce klíčových frází – Azure Search
description: Vyhodnotí nestrukturovaného textu a pro každý záznam, vrátí seznam hodnot klíčových frází v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1d221e3bcdfd781da79c73e8f228b9e449a7f5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021816"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Extrakce frází klíč kognitivních dovedností

**Extrakce frází klíč** dovednosti vyhodnotí nestrukturovaného textu a pro každý záznam, vrátí seznam hodnot klíčových frází. Tato dovednosti pomocí strojového učení modelů poskytované [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službě Cognitive Services.

Tato možnost je užitečná, pokud je potřeba rychle identifikovat hlavní témata body v záznamu. Například daný vstupní text "potravinovém byla chutný a bylo vynikající pracovníci", služba vrátí "potravin" a "vynikající zaměstnanci".

> [!NOTE]
> Jak můžete rozšířit rozsah zvýšení četnosti zpracování, přidání více dokumentů nebo přidání další algoritmy AI, budete muset [připojení účtovaných prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API ve službě Cognitive Services a extrakci image jako součást fáze hádání dokumentu ve službě Azure Search. Neúčtují žádné poplatky pro extrakci textu z dokumentů.
>
> Provádění předdefinované dovednosti, se účtuje za stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakce Image je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním k extrakci klíčových frází, zvažte použití [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Vstupy                | Popis |
|---------------------|-------------|
| defaultLanguageCode | (Volitelné) Kód jazyka, který chcete použít pro dokumenty, které nejsou explicitně zadat jazyk.  Pokud výchozí kód jazyka není zadaný, angličtina (en) se použije jako výchozí kód jazyka. <br/> Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Volitelné) Maximální počet klíčových frází k vytvoření. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupy     | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  Řetězec označující jazyk záznamy. Pokud není tento parametr zadán, použije se výchozí kód jazyka k analýze záznamy. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Ukázková definice

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Chyby a upozornění
Pokud zadáte kód nepodporovaný jazyk, je vygenerována chyba a nejsou extrahována klíčové fráze.
Pokud je text prázdný, budou vytvářet upozornění.
Pokud text je delší než 50 000 znaků, jenom prvních 50 000 znaků se dají analyzovat a objeví se upozornění.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
