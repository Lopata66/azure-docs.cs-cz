---
title: Typy aplikací pro Microsoft identity platform | Azure
description: Typy aplikací a scénáře podporované koncovým bodem Microsoft identity platform (verze 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac3dbd3f2148c14780c380cc0c7f2fab2a41e165
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482402"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikací pro Microsoft identity platform

Koncový bod Microsoft identity platform (v2.0) podporuje ověřování pro celou řadu architektur moderních aplikací, je založené na standardních oborových protokolů všechny [OAuth 2.0 nebo OpenID Connect](active-directory-v2-protocols.md). Tento článek popisuje typy aplikací, které můžete vytvářet pomocí Microsoft identity platform bez ohledu na to upřednostňovaný jazyk nebo platformu. Tyto informace slouží k vám pomůže pochopit scénáře vysoké úrovně před [zahájení práce s kódem](v2-overview.md#getting-started).

> [!NOTE]
> Koncový bod Microsoft identity platform nepodporuje všechny scénáře Azure Active Directory (Azure AD) a funkce. Pokud chcete zjistit, zda by měl použít koncový bod Microsoft identity platform, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

Je nutné zaregistrovat každé aplikaci, která používá koncový bod Microsoft identity platform na novém [portálu registrace aplikace](https://go.microsoft.com/fwlink/?linkid=2083908). Proces registrace aplikace shromáždí a přiřadí tyto hodnoty pro vaši aplikaci:

* **ID aplikace (klient)** , který jednoznačně identifikuje vaši aplikaci
* A **identifikátor URI pro přesměrování** , můžete použít k cílení odpovědí zpět do vaší aplikace
* Několik ostatní typy účtů, jako podporované hodnoty specifické pro scénář

Podrobnosti, zjistěte, jak [registrace aplikace](quickstart-register-app.md).

Po registraci aplikace aplikace komunikuje s platformou identity Microsoft pomocí zasílání požadavků do koncového bodu. Poskytujeme opensourcových architektur a knihoven, které zpracovávají podrobnosti o příslušných požadavcích. Máte také možnost implementovat logiku ověřování tak, že vytvoříte žádosti s těmito koncovými body:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Jednostránkové aplikace (JavaScript)

Řada moderních aplikací mají front-end jednostránkové aplikace, která primárně je napsána v jazyce JavaScript. Často je zapsán pomocí architektury, jako jsou Angular, React nebo Vue. Koncový bod Microsoft identity platform podporuje tyto aplikace s použitím [implicitního toku OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

V tomto toku aplikace obdrží tokeny přímo v platformě Microsoft identity zajistí autorizaci koncového bodu, bez jakékoli výměny server-to-server. Všechna logika ověřování a relace zpracování trvá umístit zcela v klientovi JavaScript bez přesměrování další stránky.

![Znázorňuje tok implicitní ověřování](./media/v2-app-types/convergence-scenarios-implicit.svg)

Pokud chcete zobrazit tento scénář v akci, zkuste použít jeden z ukázky kódu jednostránkové aplikace [platforma identit Microsoft Začínáme](v2-overview.md#getting-started) oddílu.

## <a name="web-apps"></a>Webové aplikace

Pro webové aplikace (.NET, PHP, Java, Ruby, Python, uzel), ke kterým uživatel přistupuje prostřednictvím prohlížeče, můžete použít [OpenID Connect](active-directory-v2-protocols.md) pro přihlášení uživatele. V OpenID Connect webová aplikace obdrží ID token. ID tokenu je token zabezpečení, která ověří identitu uživatele a poskytuje informace o uživateli ve formě deklarací identity:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Další podrobnosti o různých typů tokeny použité v Microsoft identity platform endpoint jsou k dispozici v [přístupový token](access-tokens.md) odkaz a [id_token odkaz](id-tokens.md)

Ve službě web apps server má tok ověřování přihlášení těchto kroků:

![Znázorňuje tok ověřování webové aplikace](./media/v2-app-types/convergence-scenarios-webapp.svg)

Zajistíte tím, že ověří ID token pomocí veřejného podpisového klíče přijatého z koncového bodu Microsoft identity platform identitu uživatele. Je nastavena souboru cookie relace, které lze použít k identifikaci uživatele požadavky na dalších stránkách.

Pokud chcete zobrazit tento scénář v akci, zkuste použít jeden z ukázek kódu přihlášení webové aplikace v [platforma identit Microsoft Začínáme](v2-overview.md#getting-started) oddílu.

Vedle jednoduchého přihlášení může být nutné může webová aplikace pro přístup k jiné webové služby, jako je například rozhraní REST API. V takovém případě webová aplikace mezi kombinované tok OpenID Connect a OAuth 2.0, pomocí [tok autorizačního kódu OAuth 2.0](active-directory-v2-protocols.md). Další informace o tomto scénáři, přečtěte si informace o [Začínáme s web apps a webovým rozhraním API](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webová rozhraní API

Koncový bod Microsoft identity platform můžete použít k zabezpečení webových služeb, jako jsou rozhraní RESTful API webové aplikace. Místo ID tokeny a soubory cookie relace webové rozhraní API používá přístupový token OAuth 2.0 k zabezpečení svých dat a ověřují příchozí požadavky. Volající webového rozhraní API připojí přístupového tokenu v autorizační hlavičce požadavku protokolu HTTP, například takto:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webové rozhraní API používá přístupový token k ověření identity volajícího a extrahovat informace o volajícím z deklarací identity zakódovaných v tokenu přístupu. Další podrobnosti o různých typů tokeny použité v Microsoft identity platform endpoint jsou k dispozici v [přístupový token](access-tokens.md) odkaz a [id_token odkaz](id-tokens.md)

Webové rozhraní API můžete uživatelům udělit schopnost vyjádřit výslovný souhlas nebo vyjádřit výslovný nesouhlas konkrétní funkce nebo data zveřejněním oprávnění, označované také jako [obory](v2-permissions-and-consent.md). Volající aplikace k získání oprávnění pro obor musí uživatel souhlasit oboru během toku. Koncový bod Microsoft identity platform vyzve uživatele k zadání oprávnění a pak záznamy oprávnění ve všech přístupové tokeny, které přijímá webového rozhraní API. Webové rozhraní API ověří přístupové tokeny přijme při každém volání a provádí kontroly autorizace.

Webové rozhraní API může přijímat tokeny přístup ze všech typů aplikací, včetně aplikací webového serveru, desktop a mobilní aplikace, jednostránkové aplikace, démonů na straně serveru a dokonce i v dalších webových rozhraní API. Základní tok pro webové rozhraní API vypadá takto:

![Ukazuje webové rozhraní API tok ověřování](./media/v2-app-types/convergence-scenarios-webapi.svg)

Zjistěte, jak zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth2, projděte si ukázky kódu webového rozhraní API [platforma identit Microsoft Začínáme](v2-overview.md#getting-started) oddílu.

V mnoha případech webová rozhraní API také nutné požadavky na odchozích jiné podřízené webové rozhraní API zabezpečené pomocí Microsoft identity platform. V takovém případě webová rozhraní API můžou využít výhod **On-Behalf-Of** tok, který umožňuje webového rozhraní API k výměně příchozí přístupového tokenu pro dalšího přístupového tokenu, který se má použít v odchozích požadavků. Další informace najdete v tématu [platforma identit Microsoft a tok OAuth 2.0 On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobilní a nativní aplikace

Zařízení nainstalované aplikace, jako jsou mobilní a desktopové aplikace, často potřebují přístup k back endovým službám nebo webovým rozhraním API, která ukládají data a provádět funkce jménem uživatele. Tyto aplikace můžete přidat přihlašování a autorizace pro back endovým službám pomocí [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

V tomto toku aplikace obdrží autorizační kód z koncového bodu Microsoft identity platform při přihlášení uživatele. Autorizační kód představuje oprávnění aplikace volat back endové služby jménem uživatele, který je přihlášen. Aplikace si mohou vyměňovat autorizační kód na pozadí pro přístupový token OAuth 2.0 a aktualizační token. Aplikace můžete použít přístupový token k ověření k webovým rozhraním API v požadavcích HTTP a použít obnovovací token k získání nových přístupových tokenů, když vyprší platnost těchto tokenů starší přístup.

![Znázorňuje tok ověřování nativní aplikace](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Procesy démon a aplikace na straně serveru

Aplikace, které mají dlouho běžící procesy nebo které pracují bez interakce s uživatelem také potřebují způsob, jak přistupovat k zabezpečeným prostředkům, jako je například webová rozhraní API. Tyto aplikace můžete ověřit a získat tokeny pomocí identity aplikace, spíše než uživatele delegovaná identita s tok přihlašovacích údajů klienta OAuth 2.0. Může být velmi identity aplikace pomocí tajného klíče klienta nebo certifikát. Další informace najdete v tématu [ověřování k platformě Microsoft identity v aplikace démonů s certifikáty](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

V tomto toku aplikace komunikuje přímo `/token` koncového bodu získat přístup:

![Znázorňuje tok démon procesu ověřování aplikace](./media/v2-app-types/convergence-scenarios-daemon.svg)

Můžete vytvořit aplikaci, proces démon, najdete v článku [údajů klienta, které dokumentace](v2-oauth2-client-creds-grant-flow.md), nebo to zkuste [ukázkovou aplikaci .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
