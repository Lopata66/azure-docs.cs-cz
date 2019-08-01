---
title: 'Rychlý start: Náhled adresy URL projektu, Node. js'
titlesuffix: Azure Cognitive Services
description: Začněte používat funkci náhledu adresy URL ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 4a1045be62f3bdbf75f399c894f825fa99f8e671
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706898"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Rychlý start: Náhled adresy URL s Node. js 

Následující příklady v jazyce Node vytvoří náhled adresy URL pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

## <a name="code-scenario"></a>Scénář kódu 

Následující kód získá data náhledu adresy URL.
Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte adresu URL dotazu k náhledu a přidejte parametr dotazu.  
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku *Ocp-Apim-Subscription-Key*.
5. Spusťte funkci vyhledávání. 

Následuje celý kód pro tuto ukázku:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Další kroky
- [Ukázkový kód v jazyce C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Python](python-quickstart.md)
