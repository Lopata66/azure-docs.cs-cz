---
title: Platforma identit Microsoft a OAuth 2.0 On-Behalf-Of toku | Azure
description: Tento článek popisuje, jak používat zprávy HTTP k implementaci služeb ověřování pomocí OAuth 2.0 On-Behalf-Of toku.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0c7c29bf3094c3d5fc99b9906ee4469a6643317
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501591"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma identit Microsoft a tok OAuth 2.0 On-Behalf-Of

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Tok OAuth 2.0 On-Behalf-Of (OBO) slouží případ použití kde aplikace vyvolá služby/webové rozhraní API, která zase potřebuje k volání jiné služby nebo webové rozhraní API. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. Pro služby střední vrstvy, aby ověřených požadavků pro příjem dat služby je potřeba zabezpečit přístupového tokenu z platforma identit Microsoft jménem uživatele.

> [!NOTE]
>
> - Koncový bod Microsoft identity platform nepodporuje všechny scénáře a funkce. Pokud chcete zjistit, zda by měl použít koncový bod Microsoft identity platform, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md). Konkrétně známé klientských aplikací se nepodporují pro aplikace s účtem Microsoft (MSA) a cílové skupiny Azure AD. Běžným vzorem souhlasu pro OBO proto nebude fungovat pro klienty, kteří přihlášení i osobní i pracovní nebo školní účty. Další informace o tom, jak zpracovat tento krok toku, najdete v článku [získat souhlas pro aplikace střední vrstvy](#gaining-consent-for-the-middle-tier-application).
> - Od května 2018, se některé implicitní tok odvozené `id_token` nelze použít pro OBO toku. Jednostránkové aplikace (SPA) by měla předat **přístup** token do střední vrstvy důvěrnému klientovi provádět OBO toky místo. Další informace o tom, které můžou klienti provádí volání OBO najdete v tématu [omezení](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokolu

Předpokládejme, že uživatel byl ověřen na aplikace s využitím [toku udělení autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md). V tomto okamžiku má aplikace přístupový token *pro rozhraní API A* (token A) s deklarací identity uživatele a vyjádření souhlasu pro střední vrstvě přístup k webovému rozhraní API (A rozhraní API). Nyní rozhraní API A potřebuje provést ověřeného požadavku na podřízené webové rozhraní API (API B).

Jaké kroky musí provést tvoří tok OBO a jsou vysvětleny díky pomoci následující diagram.

![OAuth 2.0 On-Behalf-Of toku](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace odešle požadavek rozhraní API A s tokenem A (pomocí `aud` deklarace identity a rozhraní API).
1. Rozhraní API A ověřuje u konkrétního koncového bodu Microsoft identity platform vystavování tokenů a žádá token pro přístup k rozhraní API služby serveru B.
1. Microsoft identity platform vystavování tokenů endpoint ověří přihlašovací údaje A rozhraní API pomocí tokenu A a vystaví přístupový token pro rozhraní API B (token B).
1. Token B je nastavena v hlavičce autorizace požadavku na rozhraní API B.
1. Vrátí data z zabezpečené prostředku rozhraní API služby serveru B.

> [!NOTE]
> V tomto scénáři má střední vrstvy služby bez zásahu uživatele získat souhlas uživatele pro přístup k rozhraní API pro příjem. Možnost udělit přístup k rozhraní API pro příjem dat, proto se zobrazí předem jako část souhlasu krok během ověřování. Zjistěte, jak to vytvořit aplikaci, najdete v článku [získat souhlas pro aplikace střední vrstvy](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Žádost o Service to service přístupový token

Požádat o přístupový token, ujistěte se, HTTP POST specifickým pro tenanta Microsoft identity platform koncovému bodu tokenu s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existují dva možné případy v závislosti na tom, jestli klientská aplikace rozhodne bylo zabezpečené protokolem sdílený tajný klíč nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Prvním případě: Žádost o přístupový token s sdílený tajný klíč

Pokud používáte sdílený tajný klíč, žádosti o token přístupu service to service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ žádosti o token. Pro žádost o pomocí token JWT, hodnota musí být `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Požaduje se | ID aplikace (klient), které [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránka má přiřazené vaší aplikaci. |
| `client_secret` | Požaduje se | Tajný kód klienta, který jste vygenerovali pro vaši aplikaci na webu Azure Portal – stránka registrace aplikace. |
| `assertion` | Požaduje se | Hodnota tokenu používaného v požadavku. |
| `scope` | Požaduje se | Mezerou oddělený seznam oborů pro žádosti o token. Další informace najdete v tématu [obory](v2-permissions-and-consent.md). |
| `requested_token_use` | Požaduje se | Určuje, jak by měl být požadavek zpracovat. Ve službě OBO flow musí být hodnota nastavena na `on_behalf_of`. |

#### <a name="example"></a>Příklad:

Následující HTTP POST vyžádá přístupový a aktualizační token s `user.read` obor pro https://graph.microsoft.com webové rozhraní API.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

Žádosti o token service to service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Požaduje se |  ID aplikace (klient), které [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránka má přiřazené vaší aplikaci. |
| `client_assertion_type` | Požaduje se | Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Požaduje se | Kontrolní výraz (JSON web token), které potřebujete k vytvoření a podepsání certifikátem zaregistrujete jako přihlašovací údaje pro vaši aplikaci. Informace o postupu registrace vašeho certifikátu a formát kontrolního výrazu, najdete v článku [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md). |
| `assertion` | Požaduje se | Hodnota tokenu používaného v požadavku. |
| `requested_token_use` | Požaduje se | Určuje, jak by měl být požadavek zpracovat. Ve službě OBO flow musí být hodnota nastavena na `on_behalf_of`. |
| `scope` | Požaduje se | Seznam oborů pro žádosti o token oddělených mezerami. Další informace najdete v tématu [obory](v2-permissions-and-consent.md).|

Všimněte si, že parametry jsou téměř stejné jako v případě žádosti sdílený tajný klíč, s výjimkou, že `client_secret` parametr nahrazuje dva parametry: `client_assertion_type` a `client_assertion`.

#### <a name="example"></a>Příklad:

Následující HTTP POST vyžádá přístupový token s `user.read` obor pro https://graph.microsoft.com webové rozhraní API s certifikátem.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Služby k odpovědi tokenu služby access

Úspěšná odpověď je odpověď JSON OAuth 2.0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| `token_type` | Určuje hodnotu pro typ tokenu. Jediný typ, který Microsoft identity platform podporuje je `Bearer`. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: Použití tokenu nosiče (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Obor přístupu v tokenu. |
| `expires_in` | Časový interval, během několika sekund, které je přístupový token platný. |
| `access_token` | Požadovaný přístupový token. Volání služby můžete použít tento token k ověření přijímající služby. |
| `refresh_token` | Obnovovací token pro požadované přístupový token. Volání služby můžete tento token po vypršení platnosti přístupového tokenu aktuální vyžádat dalšího přístupového tokenu. Token obnovení je k dispozici pouze pokud `offline_access` byl vyžádán oboru. |

### <a name="success-response-example"></a>Příklad odpovědi úspěch

Následující příklad ukazuje úspěšná odpověď na žádost o přístupový token pro https://graph.microsoft.com webové rozhraní API.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Výše uvedené přístupový token je token ve formátu verze 1.0. Je to proto, není k dispozici token podle prostředků, ke kterému přistupujete. Microsoft Graph požaduje tokeny verze 1.0, takže platforma identit Microsoft vytváří v1.0 přístupové tokeny, když klient požádá o tokeny pro Microsoft Graph. Jenom aplikace by měla vypadat na přístupové tokeny. Klienti by neměl muset zkontrolujte je.

### <a name="error-response-example"></a>Ukázka chyby odpovědi

Koncový bod tokenu vrátí reakce na chybu při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud má zásady podmíněného přístupu (jako je ověřování službou Multi-Factor Authentication) u něho nastavený příjem dat rozhraní API. Střední vrstvy služby by měl plochu tuto chybu do klientské aplikace tak, aby klientská aplikace může poskytnout interakce uživatele splňovat zásady podmíněného přístupu.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Použití přístupového tokenu pro přístup k zabezpečeným prostředkům

Nyní služby střední vrstvy můžete použít token získali výše aby ověřených požadavků pro podřízené webové rozhraní API, tak, že nastavíte tokenu `Authorization` záhlaví.

### <a name="example"></a>Příklad:

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Získání souhlas pro aplikace střední vrstvy

V závislosti na cílové skupiny pro vaši aplikaci můžete použít různé strategie pro zajištění, že tok OBO je úspěšné. Ve všech případech konečným cílem je zajistit správné souhlas. Jak k tomu dojde, ale závisí na uživatele, které vaše aplikace podporuje.

### <a name="consent-for-azure-ad-only-applications"></a>Souhlas pro Azure AD – pouze aplikace

#### <a name="default-and-combined-consent"></a>/.default a kombinované souhlas

Pro aplikace, které je potřeba pouze přihlášení pracovní nebo školní účty stačí tradičního přístupu jinam "Označuje klientské aplikace". Aplikace střední vrstvy přidá do seznamu známých klientských aplikací v jeho manifestu klienta a pak klienta můžete aktivovat tok vyjádření souhlasu. kombinované sebe sama a aplikace střední vrstvy. Na koncovém bodu Microsoft identity platform to se provádí pomocí [ `/.default` oboru](v2-permissions-and-consent.md#the-default-scope). Při aktivaci souhlasu obrazovky pomocí známých klientské aplikace a `/.default`, obrazovkami pro vyjádření souhlasu zobrazit oprávnění pro klienta pro aplikaci API střední vrstvy a také požádat o oprávnění jsou vyžadované rozhraní API střední vrstvy. Uživatel souhlasí pro obě aplikace a pak OBO tok funguje.

V tuto chvíli osobní systémem účtů Microsoft nepodporuje kombinované souhlasu a proto se tento přístup nefunguje pro aplikace, které chcete konkrétně přihlašovat osobní účty. Osobní účty Microsoft, který používáte jako účty hostů v tenantovi se určují pomocí šablon v systému Azure AD a můžete projít kombinované souhlas.

#### <a name="pre-authorized-applications"></a>Předběžně autorizované aplikace

Funkce portálu aplikaci je "předběžně autorizovaných aplikacích". Tímto způsobem můžete určit zdroj, vždy určité aplikace má oprávnění k příjmu určité obory. To je užitečné především k navázání připojení mezi klientem front-endu a back-end zdroj snadnější. Prostředek může deklarovat více předběžně autorizovaných aplikacích – tyto aplikace můžete požádat o tato oprávnění v OBO tok a přijímat je bez poskytnutí souhlasu uživatele.

#### <a name="admin-consent"></a>Souhlas správce

Správce tenanta může zaručit, že aplikace mají oprávnění k volání jejich požadovaných rozhraní API poskytnout souhlas správce služby pro aplikace střední vrstvy. K tomuto účelu správce najít aplikace střední vrstvy v rámci jejich tenanta, otevřete stránku požadovaná oprávnění a rozhodnout dát oprávnění pro aplikaci. Další informace o souhlas správce, najdete v článku [oprávnění a vyjádření souhlasu dokumentaci](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Souhlas pro službu Azure AD a aplikace účtu Microsoft

Požadavky na vyjádření souhlasu pro osobní účty jsou kvůli omezením ve model oprávnění pro osobní účty a nedostatečné celopodnikové tenanta, trochu liší od služby Azure AD. Neexistuje žádná tenanta k poskytování celého tenanta souhlas pro ani je existuje možnost provádět kombinované souhlas. Proto další strategie zaměřené na k dispozici sami – mějte na paměti, že se, jestli funguje pro aplikace, které potřebují pouze podporují také účty Azure AD.

#### <a name="use-of-a-single-application"></a>Použití jedné aplikace

V některých případech může stačí párování střední vrstvy a front-endu klienta. V tomto scénáři možná bude snazší vytvořit jednu aplikaci zcela negace potřebu aplikace střední vrstvy. K ověřování mezi front-endu a webové rozhraní API, můžete použít soubory cookie, tokentu id_token nebo přístupového tokenu pro vlastní aplikace požaduje. Potom požádat o souhlas z této jedné aplikace na prostředek back-end.

## <a name="client-limitations"></a>Omezení klienta

Pokud klient použije implicitní tok zobrazíte tokentu id_token, a tento klient má také zástupné znaky v adrese URL odpovědi, nelze použít požadavku id_token pro tok, který OBO.  Ale přístupové tokeny, které získali prostřednictvím implicitního udělení toku může stále uplatnit důvěrnému klientovi i v případě inicializační klienta nemá adresu URL odpovědi zástupný znak zaregistrovaný.

## <a name="next-steps"></a>Další postup

Další informace o protokolu OAuth 2.0 a další způsob, jak provádět ověřování služba-služba pomocí přihlašovacích údajů klienta.

* [Přihlašovací údaje pro klienta OAuth 2.0 udělovaných jako platforma identit Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Tok OAuth 2.0 kódu v platforma identit Microsoft](v2-oauth2-auth-code-flow.md)
* [Použití `/.default` oboru](v2-permissions-and-consent.md#the-default-scope)
