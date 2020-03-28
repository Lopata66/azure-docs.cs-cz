---
title: Co je rozhraní API Bingu pro vyhledávání obrázků?
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání obrázků umožňuje využívat kognitivní možnosti vyhledávání obrázků Bingu ve vlastní aplikaci. Prostřednictvím odesílání vyhledávacích dotazů uživatelů pomocí rozhraní API můžete získat a zobrazit relevantní a vysoce kvalitní obrázky podobně jako v Obrázcích Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6bd8c472aa659bec31440292e20328865b1cb531
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79134523"
---
# <a name="what-is-the-bing-image-search-api"></a>Co je rozhraní API Bingu pro vyhledávání obrázků?

Rozhraní API pro vyhledávání obrázků Bingu umožňuje používat funkce vyhledávání obrázků bingu ve vaší aplikaci. Odesláním vyhledávacích dotazů do rozhraní API můžete získat obrázky ve vysoké kvalitě podobné [bing.com/images](https://www.bing.com/images).

Zatímco rozhraní API pro vyhledávání obrázků Bingu poskytuje výsledky hledání pouze s obrázky, můžete kombinovat nebo používat další dostupná [rozhraní API pro vyhledávání Bing](../bing-web-search/bing-api-comparison.md) a najít mnoho typů obsahu na webu.

## <a name="bing-image-search-features"></a>Funkce rozhraní API Bingu pro vyhledávání obrázků

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) můžete vylepšit prostředí své aplikace tak, aby se při psaní zobrazovaly návrhy hledaných termínů. |
| [Filtrování a omezování výsledků hledání obrázků](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Obrázky, které Bing vrací, můžete filtrovat úpravou parametrů dotazu.                                                                                                       |
| [Ořezávání, změna velikosti a zobrazování miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Pro obrázky vrácené rozhraním API Bingu pro vyhledávání obrázků můžete upravovat a zobrazovat náhledy miniatur.                                                                                      |
| [Otáčení a rozšiřování vyhledávacích dotazů uživatelů](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Možnosti vyhledávání můžete rozšířit tím, že pro dotazy zahrnete a zobrazíte hledané termíny navrhované Bingem.                                                                    |
| [Získání obrázků, které jsou v kurzu](trending-images.md)                                                                     | Přizpůsobte si vyhledávání obrázků z celého světa, které jsou právě v kurzu.                                                                                                          |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání obrázků je webová služba RESTful a díky tomu se snadno volá z jakéhokoli programovacího jazyka, který dokáže provádět požadavky HTTP a parsovat JSON. Tuto službu můžete využívat pomocí rozhraní [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) nebo sady [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným [vyhledávacím dotazem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) rozhraní API Bingu pro vyhledávání obrázků.
Tato ukázka znázorňuje, jak můžete rychle přizpůsobit vyhledávací dotaz a procházet obrázky na webu.

Až budete připraveni volat rozhraní API, vytvořte [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Pokud chcete rychle začít s prvním požadavkem na rozhraní API, můžete si přečíst informace o následujících tématech:

* [Odesílání vyhledávacích dotazů do Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) s využitím rozhraní REST API
* [Vyžádání a filtrování](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrázků vrácených Bingem pomocí sady SDK

## <a name="see-also"></a>Viz také

* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro vyhledávací api Bingu. 

* Referenční část [rozhraní API pro vyhledávání obrázků Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) obsahuje informace o koncových bodech rozhraní API, záhlavích, odpovědích rozhraní API a parametrech dotazu.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* Získávání obrázků z webu pomocí článku [rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) popisuje, jak vyhledávat a získat obrázky z webu.

* Článek [Odesílání a práce s vyhledávacími dotazy](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) popisuje, jak vytvořit, přizpůsobit a kontingenční vyhledávací dotazy.

* Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.
