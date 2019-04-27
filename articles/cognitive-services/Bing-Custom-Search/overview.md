---
title: Co je rozhraní API pro vlastní vyhledávání Bingu?
titlesuffix: Azure Cognitive Services
description: Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvořit míru vyhledávací prostředí pro témata, které vás zajímají.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 03/4/2019
ms.author: aahi
ms.openlocfilehash: e788da047cb0567fc00f27130621a2f21e575dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335524"
---
# <a name="what-is-the-bing-custom-search-api"></a>Co je rozhraní API pro vlastní vyhledávání Bingu?

Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvořit míru bez reklam vyhledávací prostředí pro témata, které vás zajímají. Můžete určit domén a webových stránek pro Bingu pro vyhledávání, jakož i kód pin, zvýšení nebo snížení úrovně konkrétnímu obsahu a vytvořit vlastní zobrazení webu pomáhají zajistit uživatelům rychle najít relevantní výsledky vyhledávání. 

## <a name="features"></a>Funkce

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání vlastních v reálném čase](define-custom-suggestions.md)     | Pošlete nám návrhy hledání, které mohou být zobrazeny jako rozevíracího seznamu jako typ uživatele.       | 
|[Možnosti hledání vlastní image](get-images-from-instance.md)     | Povolte uživatelům vyhledávání obrázků z domény a webů podle vaší instance vlastního hledání.        |        
|[Prostředí pro vlastní vyhledávání videí](get-videos-from-instance.md)     | Umožní vašim uživatelům hledání videí z domény a weby určené ve vaší instance vlastního hledání.        |    
|[Sdílet vaše instance vlastního vyhledávání](share-your-custom-search.md)     | Společně upravovat a testovat vaše hledání instance při sdílení se členy vašeho týmu.        | 
|[Konfigurace uživatelského rozhraní pro aplikace a weby](hosted-ui.md)     | Společně upravovat a testovat vaše hledání instance při sdílení se členy vašeho týmu.        | 
## <a name="workflow"></a>Pracovní postup

Můžete vytvořit vlastní hledání instance pomocí [vlastní vyhledávání Bingu portál](https://customsearch.ai). Na portálu můžete vytvořit instanci vlastního vyhledávání, která určuje domén, webů a webových stránek, které chcete, aby Bingu pro vyhledávání, spolu s těmi, které nechcete, aby ji k prohledávání. Můžete taky použít portál pro: Seznámení s prostředím vyhledávání, upravte pořadí hledání, které poskytuje rozhraní API a volitelně nakonfigurujete prohledávatelná uživatelského rozhraní mají být vykresleny v k webům a aplikacím.

Po vytvoření instance vyhledávání, můžete integrovat ho (a případně uživatelské rozhraní) na webu nebo aplikaci pomocí volání rozhraní API pro vlastní vyhledávání Bingu:

![Obrázek znázorňující, že se můžete připojit k prostřednictvím rozhraní API pro vlastní vyhledávání Bingu](media/BCS-Overview.png "funguje jak pro vlastní vyhledávání Bingu.")


## <a name="next-steps"></a>Další postup

Pokud chcete rychle začít, přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).

Podrobnosti o přizpůsobení instance vyhledávání najdete v tématu [Definice instance vlastního vyhledávání](define-your-custom-view.md).

Nezapomeňte si přečíst [požadavky na zobrazení a použití Bingu](./use-and-display-requirements.md) pro práci s výsledky hledání do vaší služby a aplikace.

Seznamte se s referenčním obsahem pro jednotlivé koncové body vlastního vyhledávání. Tyto referenční informace obsahují koncové body, hlavičky a parametry dotazů, které můžete použít při odesílání požadavků na výsledky hledání. Obsahují také definice objektů odpovědi.

- [Rozhraní API pro vlastní vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Rozhraní API pro vlastní vyhledávání videí Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Rozhraní API pro vlastní automatické návrhy Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)

