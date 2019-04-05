---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Node.js'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 9414bac220d928618b403aa2f7df7748772e0e9a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047564"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API vizuální vyhledávání Bingu a zobrazení výsledků hledání. Tato jednoduchá aplikace JavaScript odešle obrázek do rozhraní API a zobrazí informace vrácené o něm. Zatímco tato aplikace je napsána v jazyce JavaScript, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Při nahrávání místní image, musí obsahovat data formuláře `Content-Disposition` záhlaví. Je nutné nastavit jeho `name` parametr "image" a `filename` parametr lze nastavit na libovolný řetězec. Obsah ve formátu zahrnout binární data bitové kopie. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* Modul požadavku pro jazyk JavaScript. Můžete použít `npm install request` příkaz k instalaci modulu.
* Modul dat formuláře. Můžete použít `npm install form-data` příkaz k instalaci modulu. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte soubor jazyka JavaScript v vaše oblíbené prostředím IDE nebo editorem a nastavte následující požadavky:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Vytváření proměnných pro váš koncový bod rozhraní API, klíč předplatného a cestu k bitové kopie:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Vytvoření funkce s názvem `requestCallback()` k tisku odpověď z rozhraní API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Sestavit a odeslat žádost o vyhledávání

1. Vytvořte nový **FormData** pomocí `FormData()`a připojte vaše cesta k bitové kopii, pomocí `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Použít knihovnu žádost odešlete image a volat `requestCallback()` k tisku odpověď. Nezapomeňte přidat klíč předplatného. do hlavičky požadavku:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace pro vizuální vyhledávání](../tutorial-bing-visual-search-single-page-app.md)
