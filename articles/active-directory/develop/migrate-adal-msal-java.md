---
title: Příručka k migraci ADAL na MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se Azure Active Directory migrovat aplikaci MSAL Authentication Library (ADAL) Java do knihovny Microsoft Authentication Library ().
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7729a30acb1b191378960887164bb4b32e225c36
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128005"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Příručka k migraci ADAL do MSAL pro Java

Tento článek popisuje změny, které je třeba provést při migraci aplikace, která používá knihovnu Azure Active Directory Authentication Library (ADAL) k použití knihovny Microsoft Authentication Library (MSAL).

K ověřování entit Azure AD a žádosti o tokeny od Azure AD se používají knihovny Microsoft Authentication Library pro Java (MSAL4J) a Azure AD Authentication Library pro Java (ADAL4J). Předtím, než se teď většina vývojářů pracovala s Azure AD pro vývojáře (verze 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že žádá o tokeny pomocí služby Azure AD Authentication Library (ADAL).

MSAL nabízí následující výhody:

- Protože používá novější koncový bod platformy Microsoft identity, můžete ověřit širší sadu identit Microsoftu, jako jsou identity Azure AD, účty Microsoft a sociální a místní účty prostřednictvím Azure AD Business to Consumer (B2C).
- Vaši uživatelé získají nejlepší možnosti jednotného přihlašování.
- Vaše aplikace může povolit postupný souhlas a podpora podmíněného přístupu je jednodušší.

MSAL for Java je knihovna pro ověřování, kterou doporučujeme používat s platformou Microsoft identity. V ADAL4J se neimplementují žádné nové funkce. Veškeré úsilí, které se dodávají dál, se zaměřuje na zlepšení MSAL.

## <a name="differences"></a>Rozdíly

Pokud jste pracovali s koncovým bodem Azure AD for Developers (v 1.0) (a ADAL4J), můžete si přečíst informace [o tom, co se liší od koncového bodu Microsoft Identity Platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL4J získává tokeny pro prostředky, zatímco MSAL pro jazyk Java získává tokeny pro obory. Počet MSAL pro třídy jazyka Java vyžaduje parametr scopes. Tento parametr je seznam řetězců, které deklarují požadovaná oprávnění a požadované prostředky. Ukázkové obory najdete v tématu věnovaném [oborům Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) .

Do prostředku můžete přidat `/.default` příponu oboru, která vám umožní migrovat vaše aplikace z koncového bodu v 1.0 (ADAL) na koncový bod Microsoft Identity Platform (MSAL). Například pro hodnotu `https://graph.microsoft.com`prostředku je `https://graph.microsoft.com/.default`ekvivalentní hodnota oboru.  Pokud prostředek není ve formátu adresy URL, ale ID prostředku ve formuláři `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`, můžete i nadále používat hodnotu oboru jako. `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`

Další podrobnosti o různých typech oborů najdete v článcích věnovaném [oprávněním a souhlasům na platformě identity Microsoftu](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) a v [oborech pro webové rozhraní API Přijímám v 1.0 tokeny](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes) .

## <a name="core-classes"></a>Základní třídy

V ADAL4J `AuthenticationContext` třída představuje připojení ke službě tokenů zabezpečení (STS) nebo autorizačnímu serveru prostřednictvím autority. MSAL for Java je ale navržený kolem klientských aplikací. Poskytuje dvě samostatné třídy: `PublicClientApplication` a `ConfidentialClientApplication` představuje klientské aplikace.  Druhá z nich `ConfidentialClientApplication`představuje aplikaci, která je navržena tak, aby bezpečně udržovala tajný klíč, jako je například identifikátor aplikace pro aplikaci démon.

Následující tabulka ukazuje, jak se funkce ADAL4J mapují na nové MSAL pro funkce Java:

| Metoda ADAL4J| Metoda MSAL4J|
|------|-------|
|acquireToken (prostředek řetězce, přihlašovací údaj ClientCredential, AuthenticationCallback zpětné volání) | acquireToken(ClientCredentialParameters)|
|acquireToken (řetězcové prostředky, ClientAssertion kontrolní výraz, AuthenticationCallback zpětné volání)|acquireToken(ClientCredentialParameters)|
|acquireToken (prostředek řetězce, přihlašovací údaj AsymmetricKeyCredential, AuthenticationCallback zpětné volání)|acquireToken(ClientCredentialParameters)|
|acquireToken (prostředek řetězce, řetězec clientId, uživatelské jméno, heslo řetězce, zpětné volání AuthenticationCallback)| acquireToken(UsernamePasswordParameters)|
|acquireToken (řetězec prostředku, řetězec clientId, uživatelské jméno, heslo řetězce = null, zpětné volání AuthenticationCallback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (řetězcové prostředky, UserAssertion userAssertion, ClientCredential Credential, zpětné volání AuthenticationCallback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () a acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount místo IUser

ADAL4J manipulovat s uživateli. I když uživatel představuje jednoho lidského nebo softwarového agenta, může mít jeden nebo více účtů v systému Microsoft identity. Uživatel může například mít několik osobních účtů Azure AD, Azure AD B2C nebo Microsoft.

MSAL for Java definuje koncept účtu přes `IAccount` rozhraní. Jedná se o zásadní změnu z ADAL4J, ale je to dobrý důvod, protože zachycuje skutečnost, že stejný uživatel může mít několik účtů, a případně i v různých adresářích Azure AD. MSAL for Java poskytuje lepší informace v případech hostů, protože jsou k dispozici informace o domácím účtu.

## <a name="cache-persistence"></a>Trvalost mezipaměti

ADAL4J nepodporoval mezipaměť tokenů.
MSAL for Java přidá [mezipaměť tokenů](msal-acquire-cache-tokens.md) pro zjednodušení správy životností tokenů tím, že automaticky aktualizuje vypršení platnosti tokenů, pokud je to možné, a zabrání zbytečnému uživateli v poskytování přihlašovacích údajů.

## <a name="common-authority"></a>Společná autorita

Pokud v v 1.0 používáte `https://login.microsoftonline.com/common` autoritu, můžou se uživatelé přihlašovat pomocí libovolného účtu Azure Active Directory (AAD) (pro libovolnou organizaci).

Pokud použijete `https://login.microsoftonline.com/common` autoritu v 2.0, uživatelé se mohou přihlašovat pomocí libovolné organizace AAD nebo dokonce i osobní účet společnosti Microsoft (MSA). Pokud chcete omezit přihlášení na libovolný účet AAD, v MSAL pro Java musíte použít `https://login.microsoftonline.com/organizations` autoritu (což je stejné chování jako u ADAL4J). Chcete-li zadat autoritu, `authority` nastavte parametr v metodě [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) při vytváření `PublicClientApplication` třídy.

## <a name="v10-and-v20-tokens"></a>tokeny v 1.0 a v 2.0

Koncový bod v 1.0 (používaný v ADAL) vysílá jenom tokeny v 1.0.

Koncový bod v 2.0 (používaný v MSAL) může generovat tokeny v 1.0 a v 2.0. Vlastnost manifestu aplikace webového rozhraní API umožňuje vývojářům zvolit, která verze tokenu je přijata. Viz `accessTokenAcceptedVersion` v tématu Referenční dokumentace k [manifestu aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) .

Další informace o tokenech v 1.0 a v 2.0 najdete v tématu [Azure Active Directory Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>MSAL migrace ADAL

V ADAL4J byly k dispozici aktualizační tokeny – což povoluje vývojářům jejich ukládání do mezipaměti. Pak by mohli použít `AcquireTokenByRefreshToken()` k povolení řešení, jako je implementace dlouhotrvajících služeb, které aktualizují řídicí panely jménem uživatele, když už uživatel není připojený.

MSAL for Java nezveřejňuje aktualizační tokeny z bezpečnostních důvodů. Místo toho MSAL zpracovává aktualizace tokenů za vás.

MSAL for Java obsahuje rozhraní API, které umožňuje migrovat obnovovací tokeny, které jste získali pomocí ADAL4j, do ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Pomocí této metody můžete zadat dříve použitý obnovovací token spolu s libovolnými obory (prostředky), které si přejete. Obnovovací token se vymění pro nové a v mezipaměti pro použití vaší aplikací.

Následující fragment kódu ukazuje určitý kód migrace v důvěrné klientské aplikaci:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` Vrátí přístupový token a token ID, zatímco váš nový obnovovací token je uložený v mezipaměti.
Aplikace bude nyní obsahovat také IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Chcete-li použít tokeny, které jsou nyní v mezipaměti, zavolejte:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
