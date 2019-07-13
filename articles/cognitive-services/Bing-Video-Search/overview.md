---
title: Co je rozhraní API Bingu pro vyhledávání videí?
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak hledat videa na webu, pomocí rozhraní API Bingu pro vyhledávání videa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/26/2019
ms.author: scottwhi
ms.openlocfilehash: 64d6613a5bfb9b13500d2263d2f826d342972639
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867847"
---
# <a name="what-is-the-bing-video-search-api"></a>Co je rozhraní API Bingu pro vyhledávání videí?

Video API Bingu pro vyhledávání umožňuje snadno přidat video možnosti vyhledávání do služby a aplikace. Odesláním uživatele vyhledávací dotazy s rozhraním API měli lepší přehled a zobrazí relevantní a kvalitního videa podobný [Video Bingu](https://www.bing.com/video). Pomocí tohoto rozhraní API pro výsledky hledání, které obsahují pouze videa. [API vyhledávání na webu Bingu](../bing-web-search/search-the-web.md) může vrátit jiné typy webového obsahu, včetně webových stránek, videa, zprávy a obrázky.

## <a name="bing-video-search-api-features"></a>Funkce API pro vyhledávání videí Bingu

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezit výsledky videa.](concepts/get-videos.md#filtering-videos)                      | Filtrování videí vrátil tak, že upravíte parametry dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Upravit a zobrazit náhledy pro videa, která vrácený API Bingu pro vyhledávání videí.                                                                                      |
| [Získat populární videa](trending-videos.md) | Hledání populárních videí z celého světa.                                                                                                          |
| [Získejte nové poznatky z videí](video-insights.md) | Upravte vyhledávání populárních videí z celého světa.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

API pro vyhledávání videí Bingu je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](csharp.md) nebo sady [SDK](video-search-sdk-quickstart.md).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odeslat požadavek na rozhraní API pomocí platný vyhledávacího dotazu.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.


## <a name="next-steps"></a>Další postup

API pro vyhledávání videí Bingu [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) ukazuje, jak můžete přizpůsobit vyhledávací dotaz a vyhledávání na webu pro videa.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Použití [rychlý Start](csharp.md) rychle začít s první žádosti o rozhraní API.

## <a name="see-also"></a>Viz také:

* [API Bingu pro vyhledávání videí v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) referenční stránka obsahuje seznam koncových bodů, hlaviček a parametrů dotazu používaná pro požádání o výsledcích hledání.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.