---
title: S Azure Media Clipperem vytvářet | Dokumentace Microsoftu
description: Přehled služby Azure Media Clipperem, nástroj pro vytváření multimédia z prostředků
services: media-services
keywords: Galerie, dílčí klip, kódování, médií
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 35f1f359b44af00000ccd9047673b80ca541d376
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61243859"
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytvářet s Azure Media Clipperem 

Azure Media Clipperem je bezplatné knihovna jazyka JavaScript, který umožňuje vývojářům webů, jejich uživatelům poskytnout rozhraní pro vytvoření multimédia. Tento nástroj je možné integrovat do libovolné webové stránce a poskytuje rozhraní API pro načítání prostředků a odesílání úloh oříznutí.

Azure Media Clipperem vám umožní:
- Trim břidlicová před a po slatu z za provozu archivy 
- Vytváření video nejzajímavější body z události v reálném čase, živých nebo soubory videa na Vyžádání fMP4 AMS 
- CONCATENATE videa z více zdrojů 
- Vytvořit souhrn klipy z vašich mediálních materiálů AMS 
- Oříznutí videa s přesností na rámec 
- Generovat filtry dynamických manifestů přes existující za provozu a videa na Vyžádání prostředky s přesností skupiny z obrázků (GOP) 
- Vytvořit kódovací úlohy s prostředky ve vašem účtu media services

Požádat o nové funkce, zadejte své nápady nebo připomínky, odešlete do [UserVoice pro službu Azure Media Services](https://aka.ms/amsvoice/). Pokud máte a konkrétní problémy, dotazy nebo najít všechny chyby, přetažení Media Services team řádek na amcinfo@microsoft.com.

Následující obrázek ukazuje Clipperem rozhraní: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Poznámky k verzi
Najdete v následujícím seznamu Clipperem blogový příspěvek, různé známé problémy a protokolu změn pro nejnovější verzi Clipperem:
- [Blogový příspěvek](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Seznam známých problémů](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Podpora prohlížeče
Azure Media Clipperem se vytvořil pomocí moderních technologií HTML5 a podporují následující prohlížeče:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Aktuálně podporuje pouze přehrávání HTML5 streamovaných ze služby Azure Media Services.

## <a name="language-support"></a>Podpora jazyků
Widget Clipperu je k dispozici v následujících 18 jazycích:
- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Holandština Vlámsky
- Angličtina
- Francouzština
- Němčina
- Maďarština
- italština
- Japonština
- Korejština
- polština
- Portugalština (Brazílie)
- Portugalština (Portugalsko)
- ruština
- Španělština
- švédština
- turečtina

## <a name="next-steps"></a>Další postup
Abyste mohli začít používat Azure Media Clipperem, přečtěte si [Začínáme](media-services-azure-media-clipper-getting-started.md) , kde najdete podrobnosti o tom, jak nasadit widgetu.
