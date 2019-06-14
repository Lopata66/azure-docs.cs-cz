---
title: Platforma identit Microsoft používá pro přístup k zabezpečeným prostředkům bez zásahu uživatele | Azure
description: Vytváření webových aplikací pomocí Microsoft identity platform implementace ověřovacího protokolu OAuth 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 485f79f79c52067e89fa0a606e76a533c312fb84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545097"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Platforma identit Microsoft a tok přihlašovacích údajů klienta OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Můžete použít [udělení přihlašovacích údajů klienta OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) podle RFC 6749, říká se jim *OAuth s rameny dvě*, přístup k prostředkům hostované webové pomocí identity aplikace. Tento typ udělení se běžně používá pro interakce – servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací se často označují jako *procesy démon* nebo *účtů služeb*.

Přihlašovací údaje pro klienta OAuth 2.0 udělení povolení toku webové službě (důvěrnému klientovi) ověření při volání jiné webové služby pomocí vlastních přihlašovacích údajů, místo zosobňování uživatele. V tomto scénáři klient je obvykle střední vrstvy webové služby, služba démona nebo webová stránka. Pro vyšší úroveň záruky platforma identit Microsoft také umožňuje volání služby k používání certifikátu (ne sdílený tajný klíč) jako pověření.

> [!NOTE]
> Koncový bod Microsoft identity platform nepodporuje všechny funkce a scénáře služby Azure AD. Pokud chcete zjistit, zda by měl použít koncový bod Microsoft identity platform, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

Ve více standardních *OAuth s rameny tři*, klientské aplikace má přiděleno oprávnění k přístupu k prostředkům jménem konkrétního uživatele. Oprávnění se deleguje od uživatele k aplikaci, obvykle během [souhlas](v2-permissions-and-consent.md) procesu. Ale přihlašovací údaje klienta (*OAuth s rameny dvě*) tok, jsou udělena oprávnění přímo do vlastní aplikace. Pokud aplikace zobrazí token pro prostředek, prostředek vynutí, že aplikace nemá autorizaci k provedení akce a ne uživatele.

## <a name="protocol-diagram"></a>Diagram protokolu

Tok přihlašovacích údajů klienta celý vypadá podobně jako v následujícím diagramu. Každý z kroků dále v tomto článku popisujeme.

![Tok přihlašovacích údajů klienta](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Získat přímé autorizace

Aplikace obvykle obdrží přímé autorizaci pro přístup k prostředku v jednom ze dvou způsobů:

* [Prostřednictvím seznam řízení přístupu (ACL) ve zdroji](#access-control-lists)
* [Pomocí oprávnění přiřazení aplikací ve službě Azure AD](#application-permissions)

Tyto dvě metody jsou nejčastěji používaných ve službě Azure AD a doporučujeme, abyste je pro klienty a prostředky, které provádějí klienta tok přihlašovacích údajů. Prostředek můžete také k autorizaci klientů jinými způsoby. Každý prostředek serveru můžete zvolit metodu, která je nejvhodnější pro svou aplikaci.

### <a name="access-control-lists"></a>Seznamy řízení přístupu

Poskytovatel prostředků může vynutit kontrolu autorizace na základě seznamu aplikace (klient) ID, která ví a udělí konkrétní úroveň přístupu ke službě. Pokud prostředek obdrží token z koncového bodu Microsoft identity platform, může dekódovat token a extrahovat ID klienta aplikace z `appid` a `iss` deklarací identity. Pak porovná aplikace proti seznam řízení přístupu (ACL), který udržuje. Členitost seznamu řízení přístupu a metoda mezi prostředky výrazně lišit.

Běžným případem použití je použití seznamu ACL portu ke spuštění testů pro webovou aplikaci nebo webové rozhraní API. Webové rozhraní API může udělit jenom určité podmnožiny úplná oprávnění pro konkrétního klienta. Ke spuštění testů začátku do konce pomocí rozhraní API, vytvoření testovací klient, který získá tokeny z koncového bodu Microsoft identity platform a potom je odešle do rozhraní API. Rozhraní API pak zkontroluje seznam ACL pro ID aplikace testovacího klienta získáte plný přístup k celé funkce rozhraní API. Pokud použijete tento typ seznamu ACL, nezapomeňte ověřit nejen volajícího `appid` hodnotu, ale také ověřit, zda `iss` hodnota tokenu je důvěryhodný.

Tento typ ověřování je běžné, že procesy démon a účty služeb, které potřebují přistup k datům ve vlastnictví uživatelů, kteří mají osobní účty Microsoft. Pro data ve vlastnictví organizace doporučujeme, že vám přinese potřebné autorizace prostřednictvím oprávnění k aplikaci.

### <a name="application-permissions"></a>Oprávnění aplikace

Namísto použití seznamů ACL, můžete použít rozhraní API k vystavení sady oprávnění aplikace. Aplikace oprávnění aplikaci udělují správce ve vaší organizaci a lze použít pouze pro přístup k datům ve vlastnictví organizace a její zaměstnanci. Například Microsoft Graph poskytuje několik oprávnění aplikace provést následující kroky:

* Čtení pošty ve všech poštovních schránkách
* Čtení a zápis pošty ve všech poštovních schránkách
* Odesílání pošty jako jakýkoli uživatel
* Čtení dat z adresáře

Další informace o oprávněních aplikace, přejděte na [Microsoft Graphu](https://developer.microsoft.com/graph).

Oprávnění aplikace ve vaší aplikaci, postupujte podle kroků popsaných v následujících částech.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Požádat o oprávnění v portálu pro registraci aplikace

1. Zaregistrujte a vytvořte aplikaci prostřednictvím nového [prostředí registrace aplikací (Preview)](quickstart-register-app.md).
2. Přejděte do vaší aplikace v prostředí aplikace registrace (Preview). Přejděte **certifikáty a tajné kódy** a přidejte **nový tajný kód klienta**, protože je budete potřebovat alespoň jeden tajný kód klienta k vyžádání tokenu.
3. Vyhledejte **oprávnění k rozhraní API** a pak přidejte **oprávnění aplikace** , která vaše aplikace vyžaduje.
4. **Uložit** registraci aplikace.

#### <a name="recommended-sign-the-user-into-your-app"></a>Doporučené: Přihlášení uživatele do vaší aplikace

Obvykle když vytvoříte aplikaci, která používá oprávnění aplikace, aplikace vyžaduje stránku nebo zobrazení schválí její oprávnění správce. Na této stránce můžou být součástí aplikace přihlásit flow součástí nastavení aplikace, nebo může být vyhrazený tok "připojení". V mnoha případech je vhodné pro aplikace, aby to předvedli "připojení" Zobrazit pouze poté, co uživatel má přihlášení pomocí pracovního nebo školního účtu Microsoft.

Pokud se uživatel do vaší aplikace, můžete určit organizaci, do které uživatel patří před požádat uživatele ke schválení oprávnění aplikace. Ačkoli to není nezbytně nutné, pomůže vám vytvořit intuitivnější prostředí pro vaše uživatele. Chcete-li přihlásit uživatele, postupujte podle našich [Microsoft identity platform protokol kurzy](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Požádat o oprávnění od správce adresáře

Jakmile budete připraveni k žádosti o oprávnění od správce organizace, můžete přesměrovat uživatele na platformě Microsoft identity *koncový bod souhlas správce*.

> [!TIP]
> Pokuste se spustit tuto žádost do Postman! (Použít ID aplikace pro dosažení co nejlepších výsledků – kurz aplikace nebude vyžadovat užitečné oprávnění.) [![Spustit v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Tenantu Active directory, kterou chcete požádat o oprávnění. To může být ve formátu popisný název nebo identifikátor GUID. Pokud neznáte kterého tenanta uživatel patří do a chcete, aby mohly přihlásit pomocí žádného tenanta, použijte `common`. |
| `client_id` | Požaduje se | **ID aplikace (klient)** , který [webu Azure portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené vaší aplikaci. |
| `redirect_uri` | Požaduje se | Identifikátor URI pro přesměrování místo, kam chcete odpověď k odeslání pro vaši aplikaci ke zpracování. Musí přesně odpovídat jeden z identifikátorů URI, které jste zaregistrovali na portálu pro přesměrování s tím rozdílem, že ho musí mít kódování URL a může mít další segmenty. |
| `state` | Doporučené | Hodnota, která je zahrnutá v požadavku, který je také vrácen v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |

V tomto okamžiku vynucuje Azure AD, který pouze správce tenanta se můžete přihlásit do dokončení požadavku. Správce bude požádán o schválení všechna oprávnění aplikace s přímým přístupem, které jste si vyžádali pro vaši aplikaci v portálu pro registraci aplikace.

##### <a name="successful-response"></a>Úspěšné odpovědi

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšné odpovědi vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Tenantu Active directory, která oprávnění aplikace, které požadovány ve formátu GUID. |
| `state` | Hodnota, která je zahrnutá v požadavku, který je vrácený v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| `admin_consent` | Nastavte na **True**. |

##### <a name="error-response"></a>Odpověď na chybu

Pokud správce neschvaluje oprávnění pro vaši aplikaci, neúspěšnou odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, které lze použít ke klasifikaci typy chyb, a které vám umožní reagovat na chyby. |
| `error_description` | Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby. |

Po přijetí úspěšné odpovědi z koncového bodu aplikace zřizování aplikace získalo přímo použít oprávnění, která je požadovaná. Teď můžete požádat o token pro prostředek, který chcete.

## <a name="get-a-token"></a>Získání tokenu

Poté, co pro vaši aplikaci, které jste získali nezbytné autorizace, pokračujte získání přístupových tokenů pro rozhraní API. K získání tokenu pomocí klienta udělování přihlašovacích údajů, odešlete požadavek POST do `/token` Microsoft identity platform koncový bod:

> [!TIP]
> Pokuste se spustit tuto žádost do Postman! (Použít ID aplikace pro dosažení co nejlepších výsledků – kurz aplikace nebude vyžadovat užitečné oprávnění.) [![Spustit v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Prvním případě: Žádost o přístupový token s sdílený tajný klíč

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Tenantu Active directory aplikace v plánu fungovat proti ve formátu název domény nebo identifikátor GUID. |
| `client_id` | Požaduje se | ID aplikace, který je přiřazen do vaší aplikace. Tyto informace můžete najít na portálu, kde jste nezaregistrovali vaši aplikaci. |
| `scope` | Požaduje se | Hodnota předaná `scope` parametr v této žádosti by měl být identifikátor prostředku (identifikátor URI ID aplikace) požadovaný prostředek, označeny `.default` příponu. Například Microsoft Graphu, je hodnota `https://graph.microsoft.com/.default`. <br/>Tato hodnota informuje koncového bodu Microsoft identity platform, že všechny aplikace s přímým přístupem oprávnění, které jste nakonfigurovali pro vaše aplikace, koncový bod by měl vydání tokenu pro ty, které jsou přidružené k prostředku, který chcete použít. Další informace o `/.default` oboru, najdete v článku [souhlas dokumentaci](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Požaduje se | Tajný kód klienta, který jste vygenerovali pro vaši aplikaci v portálu pro registraci aplikace. Tajný kód klienta musí být kódovaná adresou URL před odesláním. |
| `grant_type` | Požaduje se | Musí být nastaveno na `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Tenantu Active directory aplikace v plánu fungovat proti ve formátu název domény nebo identifikátor GUID. |
| `client_id` | Požaduje se |ID aplikace (klient), který je přiřazen do vaší aplikace. |
| `scope` | Požaduje se | Hodnota předaná `scope` parametr v této žádosti by měl být identifikátor prostředku (identifikátor URI ID aplikace) požadovaný prostředek, označeny `.default` příponu. Například Microsoft Graphu, je hodnota `https://graph.microsoft.com/.default`. <br/>Tato hodnota informuje koncového bodu Microsoft identity platform, že všechny aplikace s přímým přístupem oprávnění, které jste nakonfigurovali pro vaše aplikace, ji by měl vystavit token pro ty, které jsou přidružené k prostředku, který chcete použít. Další informace o `/.default` oboru, najdete v článku [souhlas dokumentaci](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Požaduje se | Hodnota musí být nastavena na `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Požaduje se | Kontrolní výraz (JSON web token), které potřebujete k vytvoření a podepsání certifikátem zaregistrujete jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) informace o registraci vašeho certifikátu a formát kontrolního výrazu.|
| `grant_type` | Požaduje se | Musí být nastaveno na `client_credentials`. |

Všimněte si, že parametry jsou téměř stejné jako v případě žádosti sdílený tajný klíč, s tím rozdílem, že parametr hodnota client_secret nahrazuje dva parametry: client_assertion_type a client_assertion.

### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšná odpověď vypadá takto:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Popis |
| --- | --- |
| `access_token` | Požadovaný přístupový token. Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako například webové rozhraní API. |
| `token_type` | Určuje hodnotu pro typ tokenu. Jediný typ, který Microsoft identity platform podporuje je `bearer`. |
| `expires_in` | Množství času, který je přístupový token platný (v sekundách). |

### <a name="error-response"></a>Odpověď na chybu

Chybová odpověď vypadá takto:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází a reagovat na chyby. |
| `error_description` | Určité chybové zprávě, která vám můžou pomoct identifikovat hlavní příčinu chyby ověřování. |
| `error_codes` | Seznam tokenů zabezpečení specifické chybové kódy, které mohly pomoct s diagnostikou. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor pro žádost o pomoct s diagnostikou. |
| `correlation_id` | Jedinečný identifikátor pro žádost o pomoct s diagnostikou komponentami. |

> [!NOTE]
> Aby se vaše aplikace bude moct přijímat v2 token můžete aktualizovat soubor manifestu aplikace z webu azure portal. Můžete přidat atribut `accessTokenAcceptedVersion` a nastavte hodnotu na 2 jako `"accessTokenAcceptedVersion": 2`. Zkontrolujte článek [manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-reference) bližší informace o stejné. Ve výchozím nastavení aplikace aktuálně recieves v1 token. Pokud to není definován v manifestu aplikace/webové rozhraní API, je výchozí hodnota pro tento atribut v manifestu je 1, a proto bude aplikace přijímat po v1 token.  


## <a name="use-a-token"></a>Použít token

Teď, když jste získali token, pomocí tokenu prostředku provádět požadavky. Když vyprší platnost tokenu, opakovat žádost o `/token` koncový bod získat nový přístupový token.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Ukázky kódu a další dokumentace

Čtení [údajů klienta, které přehled dokumentace](https://aka.ms/msal-net-client-credentials) z knihovna Microsoft Authentication Library

| Ukázka | Platforma |Popis |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 Console | Jednoduchou aplikaci .NET Core, který se zobrazí uživatelům tenanta dotazování Microsoft Graphu pomocí identity aplikace, nikoli jménem uživatele. Ukázka znázorňuje také variace použitím certifikátů pro ověřování. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Webová aplikace, synchronizuje data z Microsoft Graphu pomocí identity aplikace, nikoli jménem uživatele. |
