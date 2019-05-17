---
title: Zabezpečené jednostránkové aplikace pomocí Microsoft identity platform implicitní tok | Azure
description: Vytváření webových aplikací pomocí Microsoft identity platform implementace implicitní tok pro jednostránkové aplikace.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
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
ms.openlocfilehash: 6f73756373a145375aa2b3d0bcb1c8fa0ede5cdb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823487"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Platforma identit Microsoft a implicitní tok poskytování

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

S Microsoft identity platform koncový bod se můžete přihlásit uživatele své jednostránkové aplikace pomocí jak osobní i pracovní nebo školní účty od Microsoftu. Jednostránková a další JavaScript aplikace, na kterých běží především v prohlížeči tváře několik zajímavé vyzve při rozhodování o ověřování:

* Vlastnosti zabezpečení těchto aplikací se značně liší od tradiční serverových webových aplikací.
* Mnoho serverů autorizace a zprostředkovatelů identity požadavků CORS nepodporují.
* Od aplikace stát zejména invazivní uživatelské prostředí pro přesměrování prohlížeče celou stránku.

V případě těchto aplikací (AngularJS, Ember.js, React.js a tak dále) podporuje platforma identit Microsoft flow implicitní Grant OAuth 2.0. Implicitní tok je popsána v [specifikaci OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Jeho primární výhodou je, že umožňuje aplikaci získat tokeny z Microsoft identity platform bez provedení back-end serveru výměnou přihlašovacích údajů. To umožňuje aplikaci pro uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API vše v rámci klienta kódu jazyka JavaScript. Existuje několik důležité informace o zabezpečení vzít v úvahu při použití implicitní tok konkrétně přibližně [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) a [zosobnění uživatele](https://tools.ietf.org/html/rfc6749#section-10.3).

Pokud chcete použít implicitní tok a platforma identit Microsoft k přidání ověřování do aplikace jazyka JavaScript, doporučujeme použít knihovnu JavaScript open source [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Ale pokud nechcete použít knihovnu v jednostránkové aplikaci a pošlete sami sobě zprávy protokolu, postupujte podle obecných kroků.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory (Azure AD) a funkce jsou podporovány bodem Microsoft identity platform. Chcete-li zjistit, zda by měl používat Microsoft identity platform endpoint, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram znázorňuje vypadá celý implicitní přihlášení tok a následující části popisují každý krok podrobněji.

![OpenID Connect plaveckých drah](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pro počáteční přihlášení uživatele do vaší aplikace, můžete odeslat [OpenID Connect](v2-protocols-oidc.md) žádosti o ověření a získat `id_token` z koncového bodu Microsoft identity platform.

> [!IMPORTANT]
> Úspěšně požádat o token ID registrace aplikace v [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránka musí mít povolené správně, tak, že vyberete tok implicitní grant **přístupové tokeny** a **Tokeny typu ID** pod **implicitní grant** oddílu. Pokud není povolen, `unsupported_response` se vrátí Chyba: **Zadaná hodnota pro 'typ odpovědi' vstupní parametr není povolený pro tohoto klienta. Očekávaná hodnota je "kód"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Pokud chcete otestovat, přihlášení pomocí implicitního toku, klikněte na tlačítko <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Po přihlášení by měl prohlížeč přesměrován na `https://localhost/myapp/` s `id_token` do adresního řádku.
>

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` | povinné |`{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints). |
| `client_id` | povinné | ID aplikace (klient), který [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky přiřazené vaší aplikaci. |
| `response_type` | povinné |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat typ odpovědi `token`. Pomocí `token` tady vám umožní vaši aplikaci pro příjem přístupový token z koncového bodu authorize okamžitě bez nutnosti provádět druhou žádost do koncového bodu authorize. Pokud používáte `token` typ odpovědi, `scope` parametr musí obsahovat obor určující, který prostředek se má token vydat. |
| `redirect_uri` | Doporučené |Redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Musí odpovídat přesně jeden z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, musí být kódování url. |
| `scope` | povinné |Místo oddělený seznam [obory](v2-permissions-and-consent.md). Pro OpenID Connect, musí zahrnovat obor `openid`, který se přeloží na "Přihlášení" oprávnění v souhlasu uživatelského rozhraní. Volitelně můžete také chtít zahrnout `email` nebo `profile` obory pro získání přístupu k datům uživatele. V této žádosti pro vyžádání souhlasu k různým prostředkům mohou zahrnovat také další obory. |
| `response_mode` | nepovinné |Určuje metodu, která se má použít k odeslání výsledný token zpátky do vaší aplikace. Výchozí hodnota je dotaz na přístupový token, ale fragment, pokud požadavek obsahuje tokentu id_token. |
| `state` | Doporučené |Hodnota v požadavku, která se také vrátit v odpovědi tokenu. Může být řetězec jakéhokoli obsahu, který chcete. Náhodně generované jedinečná hodnota se obvykle používá pro [prevence útoků proti padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| `nonce` | povinné |Hodnota v požadavku, generovaný aplikací, který bude obsahovat výsledný id_token jako deklarace identity. Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. Povinné pouze v případě, že je požadováno tokentu id_token. |
| `prompt` | nepovinné |Určuje typ interakce s uživatelem, který je požadován. Jedinými platnými hodnotami v tuto chvíli jsou "přihlášení", "žádný", "select_account" a "souhlas". `prompt=login` Vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku negace jednotného přihlašování. `prompt=none` je opakem – zajistí, že uživatel se nezobrazí se žádné interaktivní výzvu jakýmkoli způsobem. Žádost nejde dokončit tiše prostřednictvím jednotného přihlašování, koncový bod Microsoft identity platform vrátí chybu. `prompt=select_account` odešle uživateli nástroj pro výběr účtu ve kterém se zobrazí všechny účty zapamatovaných v relaci. `prompt=consent` Dialogové okno souhlasu OAuth se aktivuje, až se uživatel přihlásí, s výzvou uživateli udělit oprávnění k aplikaci. |
| `login_hint`  |nepovinné |Umožňuje předem vyplnit pole uživatelské jméno nebo e-mailová adresa přihlašovací stránka pro uživatele, pokud znáte svoje uživatelské jméno předem. Často aplikace bude používat tento parametr během opětovné ověření uživatelského jména s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity.|
| `domain_hint` | nepovinné |Může být jedna z `consumers` nebo `organizations`. Pokud zahrnutý, budou přeskočeny procesu zjišťování na základě e-mailu uživatele prochází na přihlašovací stránku, což vede k poněkud jednodušší činnost koncového uživatele. Často aplikace bude používat tento parametr během opětovné ověření extrahováním `tid` deklarace identity z požadavku id_token. Pokud `tid` deklarace identity, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft tenanta příjemce), měli byste použít `domain_hint=consumers`. V opačném případě můžete použít `domain_hint=organizations` během opětovné ověření. |

V tomto okamžiku uživatele vyzve k zadání přihlašovacích údajů a bylo možné ověření dokončit. Koncový bod Microsoft identity platform také pomohou zajistit, aby uživatel vyjádřil souhlas se oprávnění uvedená v `scope` parametr dotazu. Pokud uživatel vyjádřil souhlas **žádný** těchto oprávnění budete dotázáni, uživateli požadovaná oprávnění vyjádřit souhlas. Další informace najdete v tématu [aplikace s více tenanty, oprávnění a souhlas](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí svůj souhlas, koncový bod Microsoft identity platform vrátí odpověď na vaši aplikaci na označený `redirect_uri`, pomocí metody popsané v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi pomocí `response_mode=fragment` a `response_type=id_token+token` vypadá podobně jako následující (pomocí konců řádků pro čitelnost):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Pokud zahrnutý `response_type` zahrnuje `token`. Přístupový token, který aplikace žádá, v tomto případě pro Microsoft Graph. Přístupový token by neměly být dekódovat nebo jinak zkontroloval, by měly být považovány za neprůhledný řetězec. |
| `token_type` |Pokud zahrnutý `response_type` zahrnuje `token`. Bude vždy `Bearer`. |
| `expires_in`|Pokud zahrnutý `response_type` zahrnuje `token`. Určuje počet sekund, po které je token platný, pro účely ukládání do mezipaměti. |
| `scope` |Pokud zahrnutý `response_type` zahrnuje `token`. Označuje počet rozsahů:, pro které bude platit access_token. Nemusí obsahovat všechny obory požadováno, pokud nebyla pro uživatele (v případě Azure AD jen pro obory žádá při osobní účet se používá k přihlášení). |
| `id_token` | Podepsané JSON Web Token (JWT). Aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. Další informace o id_tokens, najdete v článku [ `id_token reference` ](id-tokens.md). <br> **Poznámka:** Pouze zadaný if `openid` byl vyžádán oboru. |
| `state` |Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

#### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může také být odeslán `redirect_uri` tak aplikaci můžete odpovídajícím způsobem zpracovat:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

## <a name="validate-the-idtoken"></a>Ověření požadavku id_token

Pouhého získání tokentu id_token není dostatečná k ověření uživatele. musí také ověřit podpis požadavku id_token a ověřte, deklarace identity v tokenu na základě požadavků vaší aplikace. Koncový bod Microsoft identity platform používá [webové tokeny JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii využívající veřejného klíče pro podepisování tokenů a ověřte, že jsou platné.

Můžete také ověřit `id_token` v klientovi kód, ale běžnou praxí je odeslat `id_token` back-end server a provést ověření existuje. Jakmile ověříte podpisu požadavku id_token, existují několik deklarací identity, které bude nutné ověřit. Najdete v článku [ `id_token` odkaz](id-tokens.md) Další informace, včetně [ověřování tokenů](id-tokens.md#validating-an-id_token) a [důležité informace o podepisování výměny klíčů](active-directory-signing-key-rollover.md). Doporučujeme, abyste využívající knihovnu k analýze a ověřování tokenů: k dispozici aspoň jeden k dispozici pro většinu jazyky a platformy.

Také můžete chtít ověřit další deklarace identity v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění uživatele nebo organizaci zaregistroval k aplikaci.
* Zajistit, že uživatel má správnou autorizaci/oprávnění.
* Zajištění sílu ověřování došlo, jako je ověřování službou Multi-Factor Authentication.

Jakmile ověříte požadavku id_token, můžete zahájit relaci s uživatelem a používat deklarace identity v požadavku id_token k získání informací o uživateli ve vaší aplikaci. Tyto informace můžete použít pro zobrazení, záznamy, individuální nastavení a další.

## <a name="get-access-tokens"></a>Získání přístupových tokenů

Teď, když uživatel přihlásíte jednostránkovou aplikaci, můžete získat přístupové tokeny pro volání webových rozhraní API zabezpečené pomocí platforma identit Microsoft, jako [Microsoft Graphu](https://developer.microsoft.com/graph). I v případě, že jste už dostali tokenu pomocí `token` typ odpovědi, tuto metodu můžete použít k získání tokenů na další zdroje bez nutnosti přesměrovat uživatele se znovu přihlásit.

V normálním toku OpenID Connect a OAuth, provedli byste to tak, že požadavek na platformě Microsoft identity `/token` koncového bodu. Koncového bodu Microsoft identity platform však nepodporuje požadavků CORS, tak volání jazyka AJAX k získání a obnovovacích tokenů je mimo otázku. Místo můžete použít implicitní tok v skryté iframe získat nové tokeny pro jiné webové rozhraní API: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Podrobnosti o parametrech dotazu v adrese URL, najdete v části [odeslat znaménko v žádosti o](#send-the-sign-in-request).

> [!TIP]
> Zkuste kopírování a vkládání nižší než požadavek na kartě prohlížeče! (Nezapomeňte nahradit `login_hint` hodnoty správné hodnoty pro vaše uživatele)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

K `prompt=none` parametr, tento požadavek buď úspěšné nebo selže okamžitě a vraťte se do vaší aplikace. Úspěšné odpovědi se pošle do vaší aplikace na adrese označený `redirect_uri`, pomocí metody popsané v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi pomocí `response_mode=fragment` vypadá jako:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Pokud zahrnutý `response_type` zahrnuje `token`. Přístupový token, který aplikace žádá, v tomto případě pro Microsoft Graph. Přístupový token by neměly být dekódovat nebo jinak zkontroloval, by měly být považovány za neprůhledný řetězec. |
| `token_type` | Bude vždy `Bearer`. |
| `expires_in` | Určuje počet sekund, po které je token platný, pro účely ukládání do mezipaměti. |
| `scope` | Označuje počet rozsahů:, pro které bude platit access_token. Nemusí obsahovat všechny obory požadováno, pokud nebyla pro uživatele (v případě Azure AD jen pro obory žádá při osobní účet se používá k přihlášení). |
| `id_token` | Podepsané JSON Web Token (JWT). Pokud zahrnutý `response_type` zahrnuje `id_token`. Aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. Další informace o id_tokens, najdete v článku [ `id_token` odkaz](id-tokens.md). <br> **Poznámka:** Pouze zadaný if `openid` byl vyžádán oboru. |
| `state` |Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

#### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může také být odeslán `redirect_uri` tak aplikaci můžete odpovídajícím způsobem zpracovat. V případě třídy `prompt=none`, budou mít očekávanou chybou:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

Pokud tato chyba se zobrazí v žádosti o prvku iframe, uživatel musí interaktivně Přihlaste se znovu získat nový token. Můžete zpracovávat tento případ v dle svého dává smysl pro vaši aplikaci.

## <a name="validating-access-tokens"></a>Ověřování tokenů přístupu

Jakmile obdržíte access_token, ujistěte se, že k ověření podpisu tokenu, jakož i následující deklarace identity. Můžete také ověřit další deklarace identity založené na váš scénář.

* **Cílová skupina** deklarace, ujistěte se, že token, který měla předávat do aplikace
* **Vystavitel** deklarace identity, chcete-li ověřit, že byl token vydán bodem platforma identit Microsoft do vaší aplikace
* **Ne dříve než** a **čas vypršení platnosti** deklarací, chcete-li ověřit, že nevypršela platnost tokenu

Další informace o deklarace identity v tokenu přístupu k dispozici, najdete v článku [přístup referenční informace o tokenech](access-tokens.md)

## <a name="refreshing-tokens"></a>Aktualizace tokeny

Implicitní grant neposkytuje obnovovací tokeny. Obě `id_token`s a `access_token`s vyprší po krátké době čas, takže vaše aplikace musí být připravené k aktualizaci těchto tokenů pravidelně. Pokud chcete aktualizovat buď typ tokenu, můžete provést stejný požadavek skryté iframe ze pomocí `prompt=none` parametr můžete řídit chování platforma identit. Pokud chcete dostávat nové `id_token`, nezapomeňte použít `response_type=id_token` a `scope=openid`, a také `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Odeslat odhlášení žádosti

OpenID Connect `end_session_endpoint` umožňuje vaší aplikaci odesílat požadavek na Microsoft identity platform koncový bod k ukončení relace uživatele a vymažte soubory cookie nastavením koncového bodu Microsoft identity platform. Plně podepsat uživatele z webové aplikace, aplikace by měla ukončit svou vlastní relaci s uživatelem (obvykle zaškrtnutím nebo zrušením tokenu mezipaměti odstranit soubory cookie) a pak přesměrovat prohlížeč, aby:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` |povinné |`{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, která uživatel má být vrácen do po dokončení odhlášení. Tato hodnota musí odpovídat jedné z přesměrování, na které registrovaný kód URIs pro aplikaci. Jestliže není uveden, uživateli se zobrazí obecná zpráva koncovým bodem Microsoft identity platform. |

## <a name="next-steps"></a>Další postup

* Přejděte přes [MSAL JS ukázky](sample-v2-code.md#single-page-applications-spa) začít kódování.
