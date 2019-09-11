---
title: 'Rychlý start: Vytvoření indexu vyhledávání v Pythonu pomocí rozhraní REST API – Azure Search'
description: Vysvětluje, jak vytvořit index, načíst data a spustit dotazy pomocí Pythonu, poznámkových bloků Jupyter a REST API Azure Search.
ms.date: 09/10/2019
author: heidisteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: 273cd690c56ef01b4fd38398aaef85570dd758a2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881550"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Rychlý start: Vytvoření indexu Azure Search v Pythonu pomocí poznámkových bloků Jupyter
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Post (REST)](search-get-started-postman.md)
> * [Azure Portal](search-create-index-portal.md)
> 

Sestavte Jupyter Poznámkový blok, který vytváří, načítá a odesílá dotazy Azure Search indexu pomocí Pythonu a [rozhraní Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Tento článek vysvětluje, jak vytvořit Poznámkový blok krok za krokem. Případně můžete [Stáhnout a spustit dokončený Poznámkový blok Pythonu Jupyter](https://github.com/Azure-Samples/azure-search-python-samples).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu jsou vyžadovány následující služby a nástroje. 

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), který poskytuje Poznámkový blok Python 3. x a Jupyter.

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Úroveň Free můžete použít pro tento rychlý Start. 

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení k Azure Search

V této úloze spusťte Poznámkový blok Jupyter a ověřte, že se můžete připojit k Azure Search. Provedete to tak, že si vyžádáte seznam indexů z vaší služby. Ve Windows s Anaconda3 můžete použít Anaconda Navigator ke spuštění poznámkového bloku.

1. Vytvoření nového poznámkového bloku python3

1. V první buňce načtěte knihovny používané pro práci s JSON a formulujte požadavky HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Do druhé buňky zadejte prvky požadavku, které budou konstanty u všech požadavků. Nahraďte název vyhledávací služby (klíč-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (kód-správce-rozhraní API-KEY) pomocí platných hodnot. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Pokud získáte ConnectionError `"Failed to establish a new connection"`, ověřte, že klíč rozhraní API je primární nebo sekundární klíč správce a že jsou nastavené všechny úvodní a koncové znaky (`?` a `/`).

1. V třetí buňce formulujte požadavek. Tento požadavek GET cílí na kolekci indexů vaší vyhledávací služby a vybere vlastnost název existujících indexů.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Spusťte jednotlivé kroky. Pokud indexy existují, obsahuje odpověď seznam názvů indexů. Na následujícím snímku obrazovky již služba obsahuje index azureblobu-index a realestate-US-Sample.

   ![Skript Pythonu v poznámkovém bloku Jupyter s požadavky HTTP na Azure Search](media/search-get-started-python/connect-azure-search.png "Skript Pythonu v poznámkovém bloku Jupyter s požadavky HTTP na Azure Search")

   Naproti tomu prázdná kolekce indexů vrátí tuto odpověď:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1\. Vytvoření indexu

Pokud portál nepoužíváte, musí ve službě existovat index, aby bylo možné načíst data. Tento krok používá [REST API vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) k odeslání schématu indexu do služby.

Požadované prvky indexu zahrnují název, kolekci polí a klíč. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se pole používá (například zda je fulltextově prohledávatelné, filtrovatelné nebo dá být možné ve výsledcích hledání). V indexu musí být jedno z polí typu `Edm.String` určeno jako *klíč* pro identitu dokumentu.

Tento index má název "hotely-rychlý Start" a obsahuje definice polí, které vidíte níže. Jedná se o podmnožinu většího [indexu hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) používaných v jiných návodech. V tomto rychlém startu jsme ho pro zkrácení vystříhat.

1. V další buňce vložte následující příklad do buňky pro zadání schématu. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. V jiné buňce formulujte požadavek. Tato žádost o vložení cílí na kolekci indexů vaší vyhledávací služby a vytvoří index založený na schématu indexu, které jste zadali v předchozí buňce.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Spusťte jednotlivé kroky.

   Odpověď obsahuje reprezentace schématu ve formátu JSON. Následující snímek obrazovky ukazuje jenom část odpovědi.

    ![Požadavek na vytvoření indexu](media/search-get-started-python/create-index.png "Požadavek na vytvoření indexu")

> [!Tip]
> Dalším způsobem, jak ověřit vytvoření indexu, je zkontrolovat seznam indexy na portálu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. načtení dokumentů

K odesílání dokumentů použijte požadavek HTTP POST na koncový bod adresy URL vašeho indexu. REST API je [Přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty pocházejí z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) na GitHubu.

1. V nové buňce poskytněte čtyři dokumenty, které odpovídají schématu indexu. Zadejte akci odeslání pro každý dokument.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. V jiné buňce formulujte požadavek. Tento požadavek POST cílí na kolekci docs pro index pro rychlý začátek a vložení dokumentů uvedených v předchozím kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Spusťte jednotlivé kroky a nahrajte dokumenty do indexu ve vyhledávací službě. Výsledky by měly vypadat podobně jako v následujícím příkladu. 

    ![Odeslat dokumenty do indexu](media/search-get-started-python/load-index.png "Odeslat dokumenty do indexu")

## <a name="3---search-an-index"></a>3\. Prohledání indexu

V tomto kroku se dozvíte, jak zadat dotaz na index pomocí [vyhledávacích dokumentů REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. V buňce zadejte výraz dotazu, který spustí prázdné vyhledávání (Search = *) a vrátí Neseřazený seznam (hledání skóre = 1,0) libovolných dokumentů. Ve výchozím nastavení Azure Search vrátí 50 shod v čase. Jako strukturovaný tento dotaz vrátí celou strukturu dokumentů a hodnot. Přidejte $count = true pro získání počtu všech dokumentů ve výsledcích.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. V nové buňce zadejte následující příklad pro hledání podmínek "hotely" a "WiFi". Přidejte $select pro určení, která pole se mají zahrnout do výsledků hledání.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. V jiné buňce formulujte požadavek. Tento požadavek GET cílí na kolekci docs v indexu pro rychlé zprovoznění a připojí dotaz, který jste zadali v předchozím kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Spusťte jednotlivé kroky. Výsledky by měly vypadat podobně jako v následujícím výstupu. 

    ![Hledání v indexu](media/search-get-started-python/search-index.png "Hledání v indexu")

1. Vyzkoušejte si několik dalších příkladů dotazů, které vám pomohou s syntaxí. Můžete nahradit `searchstring` následujícími příklady a pak znovu spustit požadavek hledání. 

   Použít filtr: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Využijte horních dvou výsledků:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Seřadit podle konkrétního pole:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další postup

V rámci zjednodušení se v tomto rychlém startu používá zkrácená verze indexu hotelů. Můžete vytvořit úplnou verzi a vyzkoušet si zajímavější dotazy. Chcete-li získat úplnou verzi a všechny dokumenty 50, spusťte průvodce **importem dat** a vyberte možnost *hotely-ukázka* z vestavěných ukázkových zdrojů dat.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vytvoření indexu v Azure Portal](search-get-started-portal.md)
