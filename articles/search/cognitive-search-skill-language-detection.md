---
title: Jazyk detekce kognitivního vyhledávání dovednosti – Azure Search
description: Vyhodnotí nestrukturovaného textu a pro každý záznam, vrátí se skóre označující sílu analýzy v kanálu služby Azure Search obohacování identifikátor jazyka.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 4ba597d28ddf4bbf1f2546c2bc7b498103b50efa
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959472"
---
#   <a name="language-detection-cognitive-skill"></a>Kognitivní dovednosti detekce jazyka

Až 120 jazyků **rozpoznávání jazyka** dovednosti jazyk vstupního textu zjišťuje a vykazuje jeden jazyk kódu pro každý dokument podání žádosti. Kód jazyka je spárovaná s skóre označuje sílu analýzy. Tato dovednosti pomocí strojového učení modelů poskytované [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službě Cognitive Services.

Tato funkce je obzvláště užitečná při budete muset zadat jazyk textu jako vstup pro další dovednosti (například [analýzu subjektivního hodnocení dovednosti](cognitive-search-skill-sentiment.md) nebo [dovedností rozdělit textové](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> Od 21. prosince 2018 můžete [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) s dovednosti Azure Search. To umožňuje spouštění poplatků za využití jeho dovedností. K tomuto datu také začali účtovat pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů dál nabízet bez dalších poplatků.
>
> [Integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) spuštění, se účtuje [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services), na stejné přenosové rychlosti, jako by měl provést úlohu přímo. Extrakce Image je poplatek za Azure Search, v současné době nabízena na ceny verze preview. Podrobnosti najdete v tématu [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) nebo [fakturace](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním analýza mínění, můžete použít [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Vstupy dovedností

Parametry rozlišují malá a velká písmena.

| Vstupy     | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupního    | Popis |
|--------------------|-------------|
| languageCode | Kód jazyka ISO 6391 pro jazyky identifikovat. Například "en". |
| LanguageName | Název jazyka. Například "Angličtina". |
| skóre | Hodnota mezi 0 a 1. Pravděpodobnost, že je správně identifikovat jazyk. Skóre může být nižší než 1, pokud věty se smíšenými jazyků.  |

##  <a name="sample-definition"></a>Ukázková definice

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
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
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Případy chyb
Pokud textu je vyjádřena v nepodporovaný jazyk, je vygenerována chyba a vrátí se žádný identifikátor jazyka.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
