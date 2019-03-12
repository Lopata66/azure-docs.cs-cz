---
title: Nejčastější dotazy (FAQ) - API pro vyhledávání obrázků Bingu
titleSuffix: Azure Cognitive Services
description: Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: troubleshooting
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: a6d5d7b3ab83c8e405c6866562b4095434b9acff
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539016"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Nejčastější dotazy (FAQ) k rozhraní API Bingu pro vyhledávání obrázků

Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se rozhraní API Bingu pro vyhledávání obrázků služeb Microsoft Cognitive Services v Azure.

## <a name="response-headers-in-javascript"></a>Hlavičky odpovědi v jazyce JavaScript

Následující hlavičky mohou nastat v odpovědi rozhraní API Bingu pro vyhledávání obrázků.

|||
|-|-|
|`X-MSEdge-ClientID`|Jedinečné ID, které Bing přiřazeno uživateli|
|`BingAPIs-Market`|Na trhu, který byl použit ke splnění žádosti|
|`BingAPIs-TraceId`|Záznam protokolu na serveru rozhraní API Bingu pro tuto žádost (podpora)|

To je zvláště důležité pro zachování ID klienta a vrátit ho s dalšími požadavky. Když toto provedete, bude hledání využívat dřívější kontext v pořadí výsledky hledání a také poskytovat konzistentní uživatelské prostředí.

Ale při volání rozhraní API Bingu pro vyhledávání obrázků z jazyka JavaScript, integrované bezpečnostní funkce v prohlížeči (CORS) může zabránit vám přístup k hodnoty z těchto záhlaví.

K získání přístupu k záhlaví, můžete provést požadavek na rozhraní API pro vyhledávání obrázků Bingu prostřednictvím proxy serveru CORS. Odpověď z takového proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Je snadné k instalaci proxy CORS a povolit naše [ukázková aplikace](tutorial-bing-image-search-single-page-app.md) záhlaví volitelný klientský přístup. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Potom zadejte následující příkaz z příkazového řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod rozhraní API pro vyhledávání obrázků Bingu v souboru HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědi v produkčním prostředí

Přístup proxy CORS, je popsáno v předchozí odpověď je vhodný pro vývoj, testování a učení.

V produkčním prostředí ale byste neměli hostit skript na straně serveru ve stejné doméně jako webovou stránku, která používá rozhraní API webové vyhledávání Bingu. Tento skript by ve skutečnosti provádět volání rozhraní API na vyžádání z webové stránky JavaScript a předávat všechny výsledky, včetně záhlaví, zpět do klienta. Protože tyto dva prostředky (stránku a skript) sdílení původ, CORS nepřejde do hry a speciálními záhlavími jsou dostupné na JavaScript na webové stránce.

Tento přístup taky chrání svůj klíč rozhraní API vystavení public, protože pouze pro skript na straně serveru potřebuje. Skript můžete použít jinou metodu (například odkazující HTTP) abyste měli jistotu, že je požadavek autorizován.

## <a name="next-steps"></a>Další postup

Je vaše otázky týkající se chybějící funkce nebo funkce? Vezměte v úvahu požádat nebo pro ni hlasovat o našich [webu User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Další informace najdete v tématech

 [Přetečení zásobníku: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)
