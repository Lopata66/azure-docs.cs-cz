---
title: Přehled Azure Media Services V3
titleSuffix: Azure Media Services
description: Podrobný přehled Azure Media Services V3 s odkazy na rychlé starty, kurzy a ukázky kódu.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/03/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: df1602fe82b4a625dd5f01de2bd7236816fbb0ac
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164657"
---
# <a name="azure-media-services-v3-overview"></a>Přehled Azure Media Services V3

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Bez ohledu na to, jestli jste vývojář aplikace, centrum volání, státní úřad nebo zábava, Media Services vám pomůže vytvářet aplikace, které dodávají mediální prostředí vynikající kvality pro velké cílové skupiny na nejoblíbenějších mobilních zařízeních a prohlížečích.

Sady SDK Media Services V3 jsou založené na [Media Services V3 openapi Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> V současné době nemůžete použít Azure Portal ke správě prostředků v3. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Azure Media Services a nesmíte používat Media Services ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo kteří můžou být pro ostatní škodlivé.

Před nahráním videa nebo obrázku do Media Services musíte mít všechna správná práva k používání videa nebo obrázku, včetně, pokud to vyžaduje zákon, všech nezbytných souhlasů od jednotlivců (pokud existují) na videu nebo obrázku, pro použití, zpracování a ukládání svých dat v Media Services a Azure. Některé jurisdikce můžou u této kolekce stanovit zvláštní zákonné požadavky, online zpracování a ukládání určitých kategorií dat, jako je biometriková data. Než začnete používat Media Services a Azure pro zpracování a ukládání jakýchkoli dat, která se vztahují na zvláštní zákonné požadavky, musíte zajistit dodržování předpisů u všech právních požadavků, které se na vás můžou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v Media Services najdete na webu Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). Pro závazky společnosti Microsoft, postupy pro zpracování a uchovávání dat, včetně toho, jak odstranit vaše data, přečtěte si [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky používání služeb Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Pomocí Media Services souhlasíte s tím, že DPA a prohlášením o zásadách ochrany osobních údajů.
 
## <a name="what-can-i-do-with-media-services"></a>Co všechno jde dělat v Media Services?

Media Services umožňuje vytvářet různé pracovní postupy pro média v cloudu. Mezi příklady toho, co můžete s Media Services provádět, patří:

* Vytvářet videa v různých formátech pro přehrávání v široké škále prohlížečů a zařízení. V případě doručování na vyžádání a živého streamování do různých klientů (mobilní zařízení, televize, počítač atd.) je potřeba správně kódovat a zabalit obsah videa a zvuku. Jak takový obsah dodávat a streamovat, se dozvíte v článku [Rychlý start: kódování a streamování souborů](stream-files-dotnet-quickstart.md).
* Streamujte živé sportovní události na velkou online cílovou skupinu, jako je fotbal, baseball, školní a vysoký školní Sport a další.
* Vysílat veřejné schůzky a události, jako je městská sály, schůze města a právní orgány.
* Analyzovat zaznamenaný videoobsah a zvukový obsah. Pro větší komfort při sledování může například organizace u svého obsahu převést řeč na text nebo vytvořit index vyhledávání a řídicí panel. Může také analyzovat nejčastější témata stížností, jejich zdroje a další související data.
* Vytvořit službu odběru videa a streamovat obsah chráněný technologií DRM, pokud zákazník (například filmové studio) potřebuje omezit přístup a pracuje s díly chráněnými autorským zákonem.
* Poskytovat offline obsah pro přehrávání v letadlech, vlacích a automobilech. Zákazník si může obsah stáhnout do svého telefonu nebo tabletu pro pozdější přehrání, pokud očekává nedostupnost sítě.
* Implementujte výukovou platformu pro vzdělávací e-Learning s využitím Azure Media Services a [Azure rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pro titulkování na text, překladu na více jazyků a tak dále.
* Pomocí Azure Media Services společně se službou [Azure rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) můžete přidat titulky a titulky do videí do služby stravování pro širší cílovou skupinu (například lidem s postižením sluchu nebo lidem, kteří chtějí načítat v jiném jazyce).
* Umožněte Azure CDN dosáhnout velkého měřítka, aby bylo možné lépe zvládnout okamžité vysoké zátěže (například začátek události spuštění produktu).

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3? 

Naučte se kódovat a zabalit obsah, streamovat videa na vyžádání, živě vysílat a analyzovat vaše videa pomocí Media Services V3. Kurzy, reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat živé video a video na vyžádání nebo zvukový stream, který se škáluje milionům uživatelů.

> [!TIP]
> Než začnete s vývojem, přečtěte si:<br/>* [základní koncepty](concepts-overview.md) (incudes důležité koncepty, jako je balení, kódování a ochrana)<br/>* [vývoj s využitím rozhraní Media Services V3 API](media-services-apis-overview.md) (zahrnuje informace o přístupu k rozhraním API, konvencím pojmenování atd.)

### <a name="sdks"></a>Sady SDK

Začněte vyvíjet pomocí [sad SDK klienta Azure Media Services V3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Šablony Rychlý start  

V rychlých startech se při rychlém vyzkoušení Media Services v nových zákaznících zobrazují základní pokyny od dne 1.

* [Streamování souborů videa – .NET](stream-files-dotnet-quickstart.md)
* [Streamování souborů videa – CLI](stream-files-cli-quickstart.md)
* [Streamování souborů videa – Node. js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Kurzy

Kurzy ukazují postupy založené na scénářích pro některé z hlavních Media Services úloh.

* [Zakódovat vzdálené souborové video a streamování – REST](stream-files-tutorial-with-rest.md)
* [Kódování nahraného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)

### <a name="samples"></a>Ukázky

Pomocí [tohoto prohlížeče ukázek](https://docs.microsoft.com/samples/browse/?products=azure-media-services) můžete procházet Azure Media Services ukázky kódu.

### <a name="how-to-guides"></a>Návody

Návody obsahují ukázky kódu, které demonstrují, jak dokončit úlohu. V této části najdete spoustu příkladů. Tady je několik z nich:

* [Vytvoření účtu – rozhraní příkazového řádku](create-account-cli-how-to.md)
* [Přístup k rozhraním API – CLI](access-api-cli-how-to.md)
* [Kódování pomocí protokolu HTTPS jako vstup úlohy – .NET](job-input-from-http-how-to.md)  
* [Monitorování událostí – portál](monitor-events-portal-how-to.md)
* [Dynamické šifrování pomocí více DRM – technologie .NET](protect-with-drm.md) 
* [Postup kódování pomocí vlastního transformačního rozhraní příkazového řádku](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Seznamte se se základními koncepty](concepts-overview.md)
