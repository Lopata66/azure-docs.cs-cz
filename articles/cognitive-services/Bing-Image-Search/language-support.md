---
title: Jazyková podpora – rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Zjistěte, které země nebo oblasti a jazyky podporuje rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "68881926"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Podpora jazyků a oblastí pro rozhraní API Bingu pro vyhledávání obrázků

Rozhraní API Bingu pro vyhledávání obrázků podporuje více než tři desítkové země nebo oblasti, mnoho s více než jedním jazykem. Zadání země nebo oblasti s dotazem slouží hlavně k upřesnění výsledků hledání na základě zájmů v dané zemi nebo oblasti. Kromě toho můžou výsledky obsahovat odkazy na Bing a tyto odkazy můžou lokalizovat uživatelské prostředí Bingu podle zadaných zemí nebo oblastí nebo jazyka.

Pokud chcete zadat zemi nebo oblast a jazyk, nastavte parametr `mkt` dotazu (na trhu) na kód z tabulky **trhy** níže. Tento trh určuje zemi nebo oblast i jazyk. Pokud uživatel upřednostňuje zobrazení textu v jiném jazyce, nastavte `setLang` parametr dotazu na příslušný kód jazyka.

Případně můžete zadat zemi nebo oblast pomocí parametru `cc` dotazu. Pokud zadáte zemi nebo oblast, musíte zadat také jeden nebo více kódů jazyka pomocí hlavičky `Accept-Language` http. Podporované jazyky se liší podle země nebo oblasti; jsou uvedené pro každou zemi nebo oblast v tabulce trhy.

> [!NOTE]
> Rozhraní API pro vývojové obrázky aktuálně podporuje jenom tyto trhy:
> - EN-US (angličtina, USA)
> - en-CA (angličtina, Kanada)
> - EN-AU (angličtina, Austrálie)
> - zh-CN (čínština, Čína)

## <a name="countriesregions"></a>Země nebo oblasti

|Země|kód|
|-------|----|
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
|Čína|CN|
|Polsko|PL|
|Portugalsko|PT|
|Filipíny|PH|
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


## <a name="markets"></a>Trhy

|Země|Jazyk|Kód trhu|
|-------|--------|-----------|
|Argentina|Španělština|ES-AR|
|Austrálie|Angličtina|EN-AU|
|Rakousko|Němčina|de-AT|
|Belgie|Nizozemština|NL|
|Belgie|Francouzština|fr – bude|
|Brazílie|Portugalština|pt-BR|
|Kanada|Angličtina|en-CA|
|Kanada|Francouzština|fr – CA|
|Chile|Španělština|ES-CL|
|Dánsko|Dánština|da-DK|
|Finsko|Finština|fi-FI|
|Francie|Francouzština|fr-FR|
|Německo|Němčina|de-DE|
|Hongkong – zvláštní správní oblast|Tradiční čínština|zh – HK|
|Indie|Angličtina|en-IN|
|Indonésie|Angličtina|EN-ID|
|Itálie|Italština|it-IT|
|Japonsko|Japonština|ja-JP|
|Jižní Korea|Korejština|ko-KR|
|Malajsie|Angličtina|EN – MY|
|Mexiko|Španělština|ES – MX|
|Nizozemsko|Nizozemština|nl-NL|
|Nový Zéland|Angličtina|EN-NZ|
|Čína|Chinese|zh-CN|
|Polsko|Polština|pl-PL|
|Portugalsko|Portugalština|pt-PT|
|Filipíny|Angličtina|EN-PH|
|Rusko|Ruština|ru-RU|
|Saúdská Arábie|Arabština|ar-SA|
|Jižní Afrika|Angličtina|EN-ZA|
|Španělsko|Španělština|es-ES|
|Švédsko|Švédština|sv-SE|
|Švýcarsko|Francouzština|fr – CH|
|Švýcarsko|Němčina|de-CH|
|Tchaj-wan|Tradiční čínština|zh-TW|
|Turecko|Turečtina|tr-TR|
|Spojené království|Angličtina|en-GB|
|Spojené státy|Angličtina|cs-CZ|
|Spojené státy|Španělština|ES – US|

## <a name="next-steps"></a>Další kroky
Další informace o Vyhledávání zpráv Binguch koncových bodech najdete v referenčních informacích k [rozhraní API pro novinky vyhledávání obrázků v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
