---
title: 'Úvodní příručka: Provedení vyhledávání zpráv pomocí souboru Node.js – rozhraní REST API pro vyhledávání zpráv Bing'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odeslání požadavku do rozhraní REST API hledání zpráv bingu pomocí souboru Node.js a obdržíte odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 42ac6cac972374dbd1db42b75742212046d2ce3e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75383128"
---
# <a name="quickstart-perform-a-news-search-using-nodejs-and-the-bing-news-search-rest-api"></a>Úvodní příručka: Provedení vyhledávání zpráv pomocí souboru Node.js a rozhraní REST API pro vyhledávání zpráv Bingu

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání obrázků a dostanete odpověď ve formátu JSON. Tato jednoduchá aplikace JavaScriptu odesílá vyhledávací dotaz do rozhraní API a zobrazuje nezpracované výsledky.

I když je tato aplikace napsaná v JavaScriptu a běží v Node.js, jsou toto rozhraní API a webová služba RESTful kompatibilní s většinou programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingNewsSearchv7.js).

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/)

* [Knihovna žádostí JavaScriptu](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru nový soubor JavaScriptu a nastavte striktnost a požadavky protokolu HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, cestu k rozhraní API pro vyhledávání obrázků, klíč předplatného a hledaný výraz. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek. 

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/news/search';
    let term = 'Microsoft';
    ```

## <a name="handle-and-parse-the-response"></a>Zpracování a parsování odpovědi

1. Definujte funkci s názvem `response_handler`, která jako parametr přijímá volání protokolu HTTP `response`. V rámci této funkce proveďte následující kroky:

    1. Definujte proměnnou, která bude obsahovat text odpovědi JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Uložte text odpovědi při volání příznaku **data**.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Když je signalizován příznak **konce,** JSON a záhlaví lze zobrazit.

        ```javascript
        response.on('end', function () {
            console.log('\nRelevant Headers:\n');
            for (var header in response.headers)
                // header keys are lower-cased by Node.js
                if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                     console.log(header + ": " + response.headers[header]);
            body = JSON.stringify(JSON.parse(body), null, '  ');
            console.log('\nJSON Response:\n');
            console.log(body);
         });
        ```

## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
