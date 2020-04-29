---
title: Jazyková podpora – rozhraní API Bingu pro vyhledávání zpráv
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, zemí a oblastí, které jsou podporovány rozhraní API Bingu pro vyhledávání zpráv.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220190"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Podpora jazyků a oblastí pro rozhraní API Bingu pro vyhledávání zpráv

Rozhraní API Bingu pro vyhledávání zpráv podporuje řadu zemí nebo oblastí, mnoho s více než jedním jazykem. Zadání země nebo oblasti s dotazem slouží hlavně k upřesnění výsledků hledání na základě zájmů v dané zemi nebo oblasti. Kromě toho můžou výsledky obsahovat odkazy na Bing a tyto odkazy můžou lokalizovat uživatelské prostředí Bingu podle konkrétní země nebo oblasti nebo jazyka.

Můžete zadat zemi nebo oblast pomocí parametru `cc` dotazu. Pokud zadáte zemi nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí hlavičky `Accept-Language` http. Podporované jazyky se liší podle počtu nebo oblasti; jsou uvedené pro každou zemi nebo oblast v tabulce trhy.

Alternativně můžete zadat trh pomocí parametru `mkt` dotazu a kódu z tabulky **trhy** . Určování trhu současně určuje zemi nebo oblast a preferovaný jazyk. V `setLang` tomto případě může být parametr dotazu nastaven na kód jazyka. obvykle se jedná o stejný jazyk, který `mkt` určíte, pokud uživatel neupřednostňuje zobrazení Bingu v jiném jazyce.

## <a name="supported-markets-for-news-search-endpoint"></a>Podporované trhy pro koncový bod hledání zpráv

`/news/search` V následující tabulce jsou uvedeny hodnoty kódu na trhu, které můžete použít k zadání parametru `mkt` dotazu. Bing vrátí obsah jenom pro tyto trhy. V seznamu se může změnit.  

Seznam kódů zemí a oblastí, které můžete zadat v parametru `cc` dotazu, najdete v tématu [kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Dánsko|Dánština|da-DK|
|Rakousko|Němčina|de-AT|
|Švýcarsko|Němčina|de-CH|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|EN-AU|
|Kanada|Angličtina|en-CA|
|Spojené království|Angličtina|en-GB|
|Indonésie|Angličtina|EN-ID|
|Irsko|Angličtina|EN-IE|
|Indie|Angličtina|en-IN|
|Malajsie|Angličtina|EN – MY|
|Nový Zéland|Angličtina|EN-NZ|
|Filipínská republika|Angličtina|EN-PH|
|Singapur|Angličtina|EN-SG|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|obecné|EN-WW|
|Angličtina|obecné|EN-XA|
|Jižní Afrika|Angličtina|EN-ZA|
|Argentina|Španělština|ES-AR|
|Chile|Španělština|ES-CL|
|Španělsko|Španělština|es-ES|
|Mexiko|Španělština|ES – MX|
|Spojené státy|Španělština|ES – US|
|Španělština|obecné|ES-XL|
|Finsko|Finština|fi-FI|  
|Francie|Francouzština|fr – bude|
|Kanada|Francouzština|fr – CA|
|Belgie|Nizozemština|NL|
|Švýcarsko|Francouzština|fr – CH|
|Francie|Francouzština|fr-FR|  
|Itálie|Italština|it-IT|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh – HK|  
|Tchaj-wan|Tradiční čínština|zh-TW|
|Japonsko|Japonština|ja-JP|  
|Jižní Korea|Korejština|ko-KR|  
|Nizozemsko|Nizozemština|nl-NL|  
|Čínská lidová republika|Chinese|zh-CN|  
|Brazílie|Portugalština|pt-BR|
|Rusko|Ruština|ru-RU|  
|Švédsko|Švédština|sv-SE|  
|Turecko|Turečtina|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Podporované trhy pro koncový bod zprávy
`/news` V následující tabulce jsou uvedeny hodnoty kódu na trhu, které můžete použít k zadání parametru `mkt` dotazu. Bing vrátí obsah jenom pro tyto trhy. V seznamu se může změnit.  

Seznam kódů zemí a oblastí, které můžete zadat v parametru `cc` dotazu, najdete v tématu [kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Dánsko|Dánština|da-DK|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|EN-AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Angličtina|obecné|EN-WW|
|Chile|Španělština|ES-CL|
|Mexiko|Španělština|ES – MX|
|Spojené státy|Španělština|ES – US|
|Finsko|Finština|fi-FI|  
|Kanada|Francouzština|fr – CA|
|Francie|Francouzština|fr-FR|  
|Itálie|Italština|it-IT|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Chinese|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Podporované trhy pro koncový bod vývoje novinek
`/news/trendingtopics` V následující tabulce jsou uvedeny hodnoty kódu na trhu, které můžete použít k zadání parametru `mkt` dotazu. Bing vrátí obsah jenom pro tyto trhy. V seznamu se může změnit.  

Seznam kódů zemí a oblastí, které můžete zadat v parametru `cc` dotazu, najdete v tématu [kódy zemí](#countrycodes).  

|Země|Jazyk|Kód trhu|  
|---------------------|--------------|-----------------|
|Německo|Němčina|de-DE|
|Austrálie|Angličtina|EN-AU|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Kanada|Angličtina|en-CA|
|Indie|Angličtina|en-IN|
|Francie|Francouzština|fr-FR|
|Kanada|Francouzština|fr – CA|
|Brazílie|Portugalština|pt-BR|
|Čínská lidová republika|Chinese|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kódy zemí  

Níže jsou uvedené kódy země/oblasti, které můžete zadat v parametru `cc` dotazu. V seznamu se může změnit.  

|Země|Kód země|  
|---------------------|------------------|  
|Argentina|AR|  
|Austrálie|AU|  
|Rakousko|AT|  
|Belgie|BE|  
|Brazílie|BR|  
|Kanada|CA|  
|Chile|CL|  
|Dánsko|DK|  
|Finsko|FI|  
|Francie|FR|  
|Německo|DE|  
|Hongkong – zvláštní správní oblast|HK|  
|Indie|IN|  
|Indonésie|ID|  
|Itálie|IT|  
|Japonsko|JP|  
|Jižní Korea|KR|  
|Malajsie|MY|  
|Mexiko|MX|  
|Nizozemsko|NL|  
|Nový Zéland|NZ|  
|Norsko|NO|  
|Čínská lidová republika|CN|  
|Polsko|PL|  
|Portugalsko|PT|  
|Filipínská republika|PH|  
|Rusko|RU|  
|Saúdská Arábie|SA|  
|Jižní Afrika|ZA|  
|Španělsko|ES|  
|Švédsko|SE|  
|Švýcarsko|CH|  
|Tchaj-wan|TW|  
|Turecko|TR|  
|Spojené království|GB|  
|Spojené státy|USA|

## <a name="next-steps"></a>Další kroky
Další informace o Vyhledávání zpráv Binguch koncových bodech najdete v referenčních informacích k [rozhraní vyhledávání zpráv API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
