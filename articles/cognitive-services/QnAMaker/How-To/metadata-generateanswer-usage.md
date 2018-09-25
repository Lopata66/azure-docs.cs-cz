---
title: Metadata s GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíč/hodnota do sad otázek a odpovědí. Tyto informace slouží k filtrování výsledků na dotazy uživatelů a k ukládání dalších informací, lze použít v následných konverzace.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: eef26cf1f5a11d7dcd1fdc41747aac675e0bc528
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031080"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Pomocí metadat a rozhraní API GenerateAnswer

Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíč/hodnota do sad otázek a odpovědí. Tyto informace slouží k filtrování výsledků na dotazy uživatelů a k ukládání dalších informací, lze použít v následných konverzace. Další informace najdete v tématu [znalostní báze](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Nástroj QnA Entity

Nejprve je důležité pochopit, jak QnA Maker ukládá data otázek a odpovědí. Následující obrázek znázorňuje QnA entity:

![Nástroj QnA Entity](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečný a trvalý ID. ID je možné provést aktualizace na konkrétní entitu QnA.

## <a name="generateanswer-api"></a>GenerateAnswer rozhraní API

Použití rozhraní API GenerateAnswer v váš Bot nebo aplikaci k dotazování znalostní báze se dotaz uživatele získat nejlepší shodu ze sady otázek a odpovědí.

### <a name="generateanswer-endpoint"></a>Koncový bod GenerateAnswer

Po publikování znalostní báze, buď z [portál QnA Maker](https://www.qnamaker.ai), nebo pomocí [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), můžete získat podrobné GenerateAnswer koncového bodu.

Získat podrobnosti o vašich koncových bodů:
1. Přihlaste se k [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. V **Moje znalostních bází**, klikněte na **zobrazit kód** pro znalostní báze.
![Moje znalostních bází](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Získáte podrobnosti o vašich GenerateAnswer koncového bodu.

![Podrobnosti o koncovém bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

Můžete také získat podrobnosti o vašem koncového bodu z **nastavení** kartu znalostní báze.

### <a name="generateanswer-request"></a>GenerateAnswer žádosti

Volání GenerateAnswer pomocí požadavku HTTP POST. Ukázkový kód, který ukazuje, jak volat GenerateAnswer, najdete v článku [rychlých startů](../quickstarts/csharp.md).

- **Adresa URL požadavku**: koncový bod Tvůrce https://{QnA} /knowledgebases/ {ID znalostní báze} / generateAnswer

- **Parametry žádosti**: 
    - **ID znalostní báze** (řetězec): GUID pro znalostní báze.
    - **Koncový bod rozhraní QnAMaker** (řetězec): název hostitele koncového bodu nasazené ve vašem předplatném Azure.
- **Hlavičky žádosti**
    - **Content-Type** (řetězec): typ média textu odeslaného do rozhraní API.
    - **Autorizace** (řetězec): klíč koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Text žádosti**
    - **dotaz** (řetězec): dotázán proti znalostní báze otázku uživatele.
    - **horní** (volitelné, celé číslo): číslo seřazený výsledků, které chcete zahrnout do výstupu. Výchozí hodnota je 1.
    - **ID uživatele** (volitelný, řetězec): jedinečné ID k identifikaci uživatele. Toto ID se zaznamená do protokolů chatu.
    - **strictFilters** (volitelný, řetězec):-li zadána, informuje nástroj QnA Maker vrátit pouze odpovědi, které mají zadanou metadat. Další informace najdete níže.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer odpovědi

- **Odpověď 200** – úspěšné volání vrátí výsledek na otázku. Odpověď obsahuje následující pole:
    - **odpovědi** – seznam odpovědi na dotaz uživatele, seřazené v sestupném pořadí podle pořadí skóre.
        - **skóre**: hodnocení 0 až 100.
        - **dotazy**: na otázky, které zadal uživatel.
        - **odpověď**: odpověď na dotaz.
        - **Zdroj**: název zdroje, ze kterého byla odpověď extrahovat nebo uložit znalostní báze knowledge base.
        - **metadata**: metadata přidružená k odpovědi.
            - Název: název metadat. (maximální délka řetězce: 100, povinné)
            - hodnota: hodnota metadat. (maximální délka řetězce: 100, povinné)
        - **ID**: jedinečné ID přiřazené k odpovědi.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Příklad metadat

Vezměte v úvahu následující nejčastější dotazy týkající se data pro restaurace v Hyderabad. Kliknutím na ikonu ozubeného kola přidáte metadata do znalostní báze.

![Přidání metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrování výsledků s strictFilters

Zvažte otázku uživatele "Když se tento hotelu zavřít?" kde je implicitní záměr pro restaurace "Paradise."

Protože výsledky se vyžaduje jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer metadat "Restaurace Name", následujícím způsobem.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Udržovat kontextu
Odpověď GenerateAnswer obsahuje odpovídající informace metadat sady odpovídající otázek a odpovědí, následujícím způsobem.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

Tyto informace slouží k zaznamenání kontextu předchozí konverzace pro použití v pozdější konverzace. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./create-knowledge-base.md)
