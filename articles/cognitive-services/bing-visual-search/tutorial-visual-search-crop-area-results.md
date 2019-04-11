---
title: 'Kurz: Oříznutí obrázku se sadou SDK vizuální vyhledávání Bingu'
description: Pomocí sady SDK vizuální vyhledávání Bingu získáte přehledné informace od konkrétní arech na bitovou kopii.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/03/2019
ms.author: rosh
ms.openlocfilehash: a6a98d9ddf76e86feb35c6d3d71f7777b0edd51d
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471762"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Kurz: Oříznutí obrázku s SDK vizuální vyhledávání Bingu proC#

Sady SDK Visual pro vyhledávání Bingu umožňuje oříznutí obrázku před hledání podobných obrázků online. Tuto aplikaci ořízne jedné osobě z bitové kopie obsahující několik lidí a vrátí výsledky hledání obsahují podobné obrázky nalezena online.

Úplný zdrojový kód pro tuto aplikaci je k dispozici další chyba zpracování a poznámky na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Tento kurz ukazuje, jak:

> [!div class="checklist"]
> * Odeslat žádost o pomocí sady SDK vizuální vyhledávání Bingu
> * Oříznout oblast obrázku na hledání pro vizuální vyhledávání Bingu
> * Příjem a zpracování odpovědi
> * Vyhledání adresy URL akce v odpovědi

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
* Nainstalovaný balíček [NuGet pro vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    - V Průzkumníku řešení v sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet** z nabídky. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Zadejte oblast oříznutí obrázku

Tuto aplikaci ořízne oblasti tohoto obrázku vedení týmů ve společnosti Microsoft. Tato oblast oříznutí je definován pomocí souřadnice levého a pravého dolního, reprezentovaný jako procento celého obrázku:  

![Tým nejvyššího vedení Microsoftu](./media/MS_SrLeaders.jpg)

Tento image je oříznutý tak, že vytvoříte `ImageInfo` objektu z oblasti oříznutí a načítání `ImageInfo` objektů do `VisualSearchRequest`. `ImageInfo` Objekt zahrnuje také adresa URL obrázku:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Hledání podobně jako oblast oříznutí obrázků

Proměnná `VisualSearchRequest` obsahuje informace o oblasti oříznutí obrázku a její adresu URL. `VisualSearchMethodAsync()` Metoda získá výsledky:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Získání adresy URL dat z `ImageModuleAction`

Výsledky pro vizuální vyhledávání Bingu jsou `ImageTag` objekty. Každá značka obsahuje seznam objektů `ImageAction`. Každý `ImageAction` obsahuje `Data` pole, což je seznam hodnot, které závisí na typu akce.

Můžete vytisknout různé typy s následujícím kódem:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Dokončená aplikace vrátí:

|Typ akce  |zprostředkovatele identity  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Jak je uvedeno výše, `Entity` ActionType obsahuje vyhledávací dotaz Bingu, který vrací informace o rozpoznatelných osobě, místě nebo věc. Typy `TopicResults` a `ImageResults` obsahují dotazy pro související obrázky. Adresy URL v seznamu odkazují na výsledky vyhledávání Bingu.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Získání adres URL pro `PagesIncluding` `ActionType` bitové kopie

Získání skutečné adresy URL obrázku vyžaduje přetypování, které čte `ActionType` jako `ImageModuleAction`, obsahující element `Data` se seznamem hodnot. Každá hodnota je adresa URL obrázku. Následující přetypování `PagesIncluding` typ akce `ImageModuleAction` a načte hodnoty:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Vytvořit pro vizuální vyhledávání jednostránkovou webovou aplikaci](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech
> [Co je rozhraní API pro vizuální vyhledávání Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
