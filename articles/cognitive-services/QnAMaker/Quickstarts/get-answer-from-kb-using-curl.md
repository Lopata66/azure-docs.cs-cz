---
title: 'Rychlý start: Používáme nástroj cURL k získání odpovědí ze znalostní báze – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start vás provede získat odpověď ze znalostní báze pomocí příkazu cURL.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: ad4b40d649d4e4cbc17d6aec5d8bc7308012b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913740"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Rychlý start: Získání odpovědí ze znalostní báze pomocí cURL

Tento rychlý start na základě cURL provede získat odpověď ze znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Nejnovější [ **cURL**](https://curl.haxx.se/).
* Musíte mít [služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md) a mít [znalostní báze knowledge base s otázkami a odpověďmi](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publikování na získání koncového bodu

Až budete připravení ke generování odpověď na dotaz zadaný ze znalostní báze [publikovat](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) znalostní báze.

## <a name="use-production-endpoint-with-curl"></a>Produkční koncový bod pomocí cURL

Při publikování znalostní báze **publikovat** zobrazí se stránka nastavení požadavku HTTP ke generování odpovědi. **CURL** karta zobrazuje nastavení vyžadovaných ke generování odpověď z nástroje příkazového řádku [CURL](https://www.getpostman.com).

[![Publikování výsledků](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Ke generování odpovědi pomocí CURL, proveďte následující kroky:

1. Zkopírujte text v kartě CURL. 
1. Otevřete příkazový řádek nebo terminálu a vložit text.
1. Upravte dotaz a byly relevantní pro znalostní báze. Dejte pozor, abyste odebrat obsahující JSON okolní otázku.
1. Zadejte příkaz. 
1. Odpověď obsahuje důležité informace o odpovědi. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
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

## <a name="use-staging-endpoint-with-curl"></a>Použít pracovní koncový bod pomocí cURL

Pokud chcete získat odpověď z pracovní koncového bodu, použijte `isTest` vlastnost body.

```json
isTest:true
```

## <a name="next-steps"></a>Další postup

Stránka publikovat také obsahuje informace, které [generovat odpověď](get-answer-from-kb-using-postman.md) pomocí nástroje Postman. 

> [!div class="nextstepaction"]
> [Metadata použít při generování odpověď.](../How-to/metadata-generateanswer-usage.md)
