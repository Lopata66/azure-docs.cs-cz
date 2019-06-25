---
title: Koncový bod webové vyhledávání
titleSuffix: Azure Cognitive Services
description: Souhrn koncový bod rozhraní Web search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c55e2f8b1685893ecc813c0d5d94e894f66fb186
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390458"
---
# <a name="web-search-endpoint"></a>Koncový bod webové vyhledávání

**Webové rozhraní API pro vyhledávání** vrátí webové stránky, zpráv, obrázky, videa, a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo téma.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat výsledky hledání na webu pomocí rozhraní API Bingu pro odeslání `GET` požadavek na následující koncový bod. Specifikace definovat další hlaviček a parametrů adresy URL.

**Koncový bod**: Webové výsledky, které jsou relevantní pro určené uživatele vyhledávací dotaz vrátí `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Koncový bod: Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby a další, najdete v článku [webového rozhraní API Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON

Odpověď na webový požadavek hledání obsahuje všechny výsledky jako objekty JSON. Analýza kódu výsledku vyžaduje postupy, které zpracovávají elementy jednotlivé typy. Zobrazit [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a [zdrojový kód](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) příklady.

## <a name="next-steps"></a>Další postup

**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyka a oblasti, longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro webové vyhledávání, najdete v článku [rychlé zprovoznění webové vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
