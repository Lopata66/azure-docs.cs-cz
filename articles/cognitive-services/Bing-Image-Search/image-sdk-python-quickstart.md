---
title: 'Rychlý start: Hledání obrázků - Bingu Image Search SDK pro Python'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Pythonu posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cabf29423123d988b08e002077b4782529e96aa0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095632"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Rychlý start: Hledání obrázků s SDK vyhledávání obrázků Bingu pro Python

Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Pythonu posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) s dalším zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Požadavky
Načtěte si pod **Search** (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Python 2.7 nebo 3.4](https://www.python.org/) a vyšší.

* Sada [ SDK Vyhledávání obrázků Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) pro Python
    * Dá se nainstalovat pomocí `pip install azure-cognitiveservices-search-imagesearch`.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Ve vašem oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový skript Pythonu a přidejte následující importy:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnné pro klíč předplatného a hledaný výraz.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Vytvoření klienta vyhledávání obrázků

1. Vytvořte instanci třídy `CognitiveServicesCredentials` a použijte ji k vytvoření instance klienta:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Odešlete vyhledávací dotaz do rozhraní API Bingu pro vyhledávání obrázků:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Zpracování a zobrazení výsledků

Parsujte výsledky obrázků vrácené v odpovědi.


Pokud odpověď obsahuje výsledky hledání, uloží se první výsledek a vytisknou se jeho podrobnosti, třeba adresa URL miniatury a původní adresa URL, společně s celkovým počtem nalezených obrázků.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Ukázky Pythonu pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
