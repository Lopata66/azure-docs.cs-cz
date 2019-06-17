---
title: Pro automatické návrhy hledané termíny – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Pair – rozhraní API pro vyhledávání Bingu webové rozhraní API Bingu pro automatické návrhy uživatelům poskytnout prostředí pro hledání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384865"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Pro automatické návrhy Bingu hledaný text v aplikaci

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Poté, co uživatel zadá hledaný termín, musí být před kódování URL [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) je nastaven parametr dotazu. Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

Pokud výraz dotazu obsahuje pravopisné chyby, obsahuje odpovědi na vyhledávání [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objektu. Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Tyto informace můžete použít, umožníte uživateli vědět, změnit jejich řetězci dotazu při zobrazení výsledků hledání.

![Příklad dotazu kontextu uživatelského rozhraní](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Další postup  

* Zkontrolujte ukázky [odpovědi rozhraní API webové vyhledávání Bingu](search-responses.md).  

## <a name="see-also"></a>Další informace najdete v tématech  

* [Referenční dokumentace rozhraní API webové vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
