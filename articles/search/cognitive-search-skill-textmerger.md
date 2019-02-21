---
title: Text sloučení kognitivního vyhledávání dovednosti – Azure Search
description: Sloučení textu z kolekce polí do jednoho konsolidované pole. Pomocí této kognitivních dovedností v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a769a71fe1e99467121eb49a490fa2d0ab4339d3
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446406"
---
#    <a name="text-merge-cognitive-skill"></a>Kognitivní dovednosti sloučení textu

**Sloučení textu** dovednosti konsoliduje text z kolekce polí do jednoho pole. 

> [!NOTE]
> Tato dovednosti není vázán na API služeb Cognitive Services a se vám neúčtují poplatky k jeho používání. Měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), ale k přepsání **Free** resource – možnost, která omezuje vám malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| insertPreTag  | Řetězec, které se mají zahrnout před každou vložení. Výchozí hodnota je `" "`. Pokud chcete vynechat, nechte pole, nastavte hodnotu na `""`.  |
| insertPostTag | Řetězce mají být zahrnuty po každé vložení. Výchozí hodnota je `" "`. Pokud chcete vynechat, nechte pole, nastavte hodnotu na `""`.  |


##  <a name="sample-input"></a>Ukázkový vstup
Dokument JSON poskytuje použitelné vstup pro tuto dovednost může být:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup
Tento příklad ukazuje výstup předchozího vstup předpokladu, že *insertPreTag* je nastavena na `" "`, a *insertPostTag* je nastavena na `""`. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>Rozšířené ukázková definice dovedností

Běžný scénář použití sloučení textu je sloučit textovou reprezentaci řetězce obrázků (text ze OCR dovedností nebo popisek image) do pole obsahu dokumentu. 

Následující příklad dovednosti používá dovednosti optické rozpoznávání znaků k extrakci textu z imagí vloží do dokumentu. V dalším kroku se vytvoří *merged_text* pole tak, aby obsahovala původní i OCRed text z každé image. Další informace o dovednosti OCR [tady](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad předpokládá, že existuje pole normalized bitové kopie. Chcete-li získat pole normalized imagí, nastavte *imageAction* konfigurace definice indexeru pro *generateNormalizedImages* jak je znázorněno níže:

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
