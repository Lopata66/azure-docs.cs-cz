---
title: Referenční materiály pro přístupové tokeny Microsoft Identity Platform | Azure
description: Seznamte se s přístupovými tokeny vygenerovanými koncovými body Azure AD v 1.0 a Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: bacac67ddd7f379d679a149fe9574676ae0c7567
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834414"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny přístupu Microsoft Identity Platform

Přístupové tokeny umožňují klientům bezpečně volat rozhraní API chráněná Azure. Tokeny přístupu Microsoft Identity Platform jsou [JWTs](https://tools.ietf.org/html/rfc7519), kódované objekty JSON ve formátu base64, které jsou podepsané Azure. Klienti by měli zacházet s přístupovými tokeny jako neprůhledné řetězce, protože obsah tokenu je určený jenom pro prostředek. Pro účely ověřování a ladění mohou vývojáři dekódovat JWTs pomocí webu, jako je [JWT.MS](https://jwt.ms). Váš klient může získat přístupový token z koncového bodu verze 1.0 nebo z koncového bodu v 2.0 s použitím nejrůznějších protokolů.

Když si klient vyžádá přístupový token, Azure AD také vrátí metadata o přístupovém tokenu pro spotřebu vaší aplikace. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a rozsahy, pro které je platná. Tato data umožňují vaší aplikaci inteligentní ukládání přístupových tokenů do mezipaměti bez nutnosti analyzovat samotný přístupový token.

Pokud je vaše aplikace prostředek (webové rozhraní API), který můžou klienti požádat o přístup, poskytují přístup k tokenům užitečné informace pro použití při ověřování a autorizaci, jako je uživatel, klient, Vystavitel, oprávnění a další.

V následujících částech se dozvíte, jak může prostředek ověřit a použít deklarace v rámci přístupového tokenu.

> [!IMPORTANT]
> Přístupové tokeny se vytvářejí na základě *cílové skupiny* tokenu, což znamená aplikaci, která vlastní obory v tokenu.  Toto je způsob, jakým `accessTokenAcceptedVersion` nastavení prostředku v [manifestu aplikace](reference-app-manifest.md#manifest-reference) `2` umožňuje klientovi voláním koncového bodu v 1.0 získat přístupový token verze 2.0.  Podobně to znamená, že změna [volitelných deklarací](active-directory-optional-claims.md) přístupového tokenu pro vašeho klienta nemění přístupový token přijatý při vyžádání tokenu pro `user.read`, který vlastní prostředek MS graphu.  
> Ze stejného důvodu při testování klientské aplikace pomocí osobního účtu (například hotmail.com nebo outlook.com) můžete zjistit, že přístupový token přijatý vaším klientem je neprůhledný řetězec. Důvodem je to, že prostředek, který je k dispozici, požadoval starší lístky MSA (účet Microsoft), které jsou zašifrované a nelze je porozumět klientovi.

## <a name="sample-tokens"></a>Ukázkové tokeny

tokeny v 1.0 a v 2.0 vypadají podobně a obsahují mnoho stejných deklarací. Zde je uveden příklad každé z nich.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Zobrazit tento token v 1.0 v [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Zobrazit tento token v 2.0 v [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)

## <a name="claims-in-access-tokens"></a>Deklarace identity v přístupových tokenech

JWTs jsou rozdělené na tři části:

* **Hlavička** – poskytuje informace o tom, jak [ověřit token](#validating-tokens) , včetně informací o typu tokenu a způsobu jeho podpisu.
* **Datová část** – obsahuje všechna důležitá data o uživateli nebo aplikaci, která se pokouší zavolat vaši službu.
* **Signature** – je nezpracovaný materiál použitý k ověření tokenu.

Jednotlivé součásti jsou oddělené tečkou (`.`) a samostatně kódovaným znakem base64.

Deklarace identity jsou přítomny pouze v případě, že existuje hodnota pro její vyplnění. Proto by vaše aplikace neměla mít závislost na přítomné deklaraci. Mezi příklady patří `pwd_exp` (ne každý tenant vyžaduje vypršení platnosti hesla) nebo `family_name` (toky[přihlašovacích údajů klienta](v1-oauth2-client-creds-grant-flow.md) jsou v zastoupení aplikací, které nemají názvy). Deklarace identity použité pro ověření přístupového tokenu budou vždy k dispozici.

> [!NOTE]
> Některé deklarace identity se používají k usnadnění zabezpečených tokenů Azure AD v případě opakovaného použití. Ty jsou označené jako nepoužívané pro veřejnou spotřebu v popisu jako "neprůhledné". Tyto deklarace identity můžou nebo nemusí být v tokenu, a nové můžou být přidané bez předchozího upozornění.

### <a name="header-claims"></a>Deklarace hlaviček

|Deklarovat | Formát | Popis |
|--------|--------|-------------|
| `typ` | Řetězec – vždycky "JWT" | Označuje, že token je JWT.|
| `nonce` | Řetězec | Jedinečný identifikátor, který slouží k ochraně před útoky na opakované přehrání tokenu. Prostředek může tuto hodnotu zaznamenat k ochraně před přehráním. |
| `alg` | Řetězec | Určuje algoritmus, který se použil k podepsání tokenu, například "RS256". |
| `kid` | Řetězec | Určuje kryptografický otisk pro veřejný klíč, který se používá k podepsání tohoto tokenu. Emitováno v tokenech přístupu v 1.0 i v 2.0. |
| `x5t` | Řetězec | Funguje stejně jako `kid`. `x5t` je starší deklarace identity vygenerovaná pouze v přístupových tokenech verze 1.0 pro účely kompatibility. |

### <a name="payload-claims"></a>Deklarace datové části

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `aud` | Řetězec – identifikátor URI ID aplikace | Identifikuje zamýšleného příjemce tokenu. V tokenech ID jsou cílovou skupinou ID aplikace vaší aplikace, které je přiřazeno vaší aplikaci v Azure Portal. Vaše aplikace by měla tuto hodnotu ověřit a zamítnout token, pokud se hodnota neshoduje. |
| `iss` | Řetězec, identifikátor URI služby STS | Identifikuje službu tokenů zabezpečení (STS), která vytvoří a vrátí token, a tenanta Azure AD, ve kterém byl uživatel ověřený. Pokud je vydaný token tokenem v 2.0 (viz `ver` deklarace), identifikátor URI skončí `/v2.0`. Identifikátor GUID, který označuje, že uživatel je uživatelem uživatele z účet Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad`. Vaše aplikace by měla použít část s identifikátorem GUID k omezení sady klientů, kteří se mohou k aplikaci přihlásit, pokud jsou k dispozici. |
|`idp`| Řetězec, obvykle identifikátor URI služby STS | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. Tato hodnota je shodná s hodnotou deklarace vystavitele, pokud uživatelský účet, který není ve stejném tenantovi jako host pro vystavitele, například. Pokud tato deklarace identity není k dispozici, znamená to, že se místo toho dá použít hodnota `iss`.  Pro osobní účty používané v organizačním kontextu (například osobní účet, který je pozván na tenanta Azure AD) může být deklarace `idp` live.com nebo identifikátor URI STS obsahující účet Microsoft tenanta `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, časové razítko systému UNIX | "Vydáno v" označuje, kdy došlo k ověření pro tento token. |
| `nbf` | int, časové razítko systému UNIX | Deklarace "NBF" (ne dřív) určuje dobu, po jejímž uplynutí nesmí být požadavek JWT přijat ke zpracování. |
| `exp` | int, časové razítko systému UNIX | Deklarace "EXP" (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí může být požadavek JWT přijat ke zpracování. Je důležité si uvědomit, že před tímto časem může prostředek odmítat token, například když se vyžaduje změna v ověřování nebo se zjistilo odvolání tokenu. |
| `aio` | Neprůhledný řetězec | Interní deklarace, kterou používá služba Azure AD k záznamu dat pro opakované použití tokenu Prostředky by neměly tuto deklaraci identity používat. |
| `acr` | String, "0" nebo "1" | K dispozici pouze v tokenech v 1.0. Deklarace identity Class kontextu ověřování. Hodnota 0 označuje, že ověřování koncového uživatele nesplňovalo požadavky normy ISO/IEC 29115. |
| `amr` | Pole JSON řetězců | K dispozici pouze v tokenech v 1.0. Určuje způsob ověření předmětu tokenu. Další podrobnosti najdete [v části deklarace identity AMR](#the-amr-claim) . |
| `appid` | Řetězec, identifikátor GUID | K dispozici pouze v tokenech v 1.0. ID aplikace klienta, který používá token. Aplikace může fungovat samostatně nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat instanční objekt služby ve službě Azure AD. |
| `appidacr` | "0", "1" nebo "2" | K dispozici pouze v tokenech v 1.0. Označuje způsob ověření klienta. U veřejného klienta je hodnota "0". Pokud se použije ID klienta a tajný klíč klienta, hodnota je 1. Pokud byl klientský certifikát použit k ověření, hodnota je "2". |
| `azp` | Řetězec, identifikátor GUID | Je k dispozici pouze v tokenech v 2.0, což je náhrada za `appid`. ID aplikace klienta, který používá token. Aplikace může fungovat samostatně nebo jménem uživatele. ID aplikace obvykle představuje objekt aplikace, ale může také představovat instanční objekt služby ve službě Azure AD. |
| `azpacr` | "0", "1" nebo "2" | Je k dispozici pouze v tokenech v 2.0, což je náhrada za `appidacr`. Označuje způsob ověření klienta. U veřejného klienta je hodnota "0". Pokud se použije ID klienta a tajný klíč klienta, hodnota je 1. Pokud byl klientský certifikát použit k ověření, hodnota je "2". |
| `preferred_username` | Řetězec | Primární uživatelské jméno, které představuje uživatele. Může to být e-mailová adresa, telefonní číslo nebo obecné uživatelské jméno bez zadaného formátu. Jeho hodnota je proměnlivá a může se v průběhu času měnit. Protože je proměnlivá, nesmí se tato hodnota použít k rozhodování o autorizaci.  Dá se použít i v pomocných parametrech uživatelského jména. Aby bylo možné získat tuto deklaraci, je vyžadován obor `profile`. |
| `name` | Řetězec | Poskytuje uživatelsky čitelné hodnoty, které identifikují předmět tokenu. Hodnota není zaručena jako jedinečná, je proměnlivá a je navržena tak, aby se používala pouze pro účely zobrazení. Aby bylo možné získat tuto deklaraci, je vyžadován obor `profile`. |
| `scp` | Řetězec, seznam oborů oddělených mezerami | Sada oborů vystavené vaší aplikací, pro které klientská aplikace požádala o souhlas. Vaše aplikace by měla ověřit, že tyto obory jsou platné, které jsou vystavené vaší aplikací, a učinit rozhodnutí o autorizaci na základě hodnoty těchto oborů. Je zahrnutá jenom pro [tokeny uživatelů](#user-and-application-tokens). |
| `roles` | Pole řetězců, seznam oprávnění | Sada oprávnění vystavená vaší aplikací, ke které žádající aplikace nebo uživatel udělil oprávnění k volání. Pro [tokeny aplikací](#user-and-application-tokens)se tato služba používá v [rámci toku uživatelů na místě](v1-oauth2-client-creds-grant-flow.md) .  Pro [tokeny uživatele](#user-and-application-tokens) se naplní role, ke kterým se uživatel přiřadil v cílové aplikaci. |
| `wids` | Pole [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) identifikátorů GUID | Označuje role v rámci tenanta přiřazené tomuto uživateli z oddílu rolí, které jsou k dispozici na [stránce role správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Tato deklarace identity je nakonfigurovaná na základě jednotlivých aplikací prostřednictvím vlastnosti `groupMembershipClaims` [manifestu aplikace](reference-app-manifest.md).  Nastaví se na All nebo DirectoryRole se vyžaduje.  Nemusí být k dispozici v tokenech získaných prostřednictvím implicitního toku, protože se týkají délky tokenu. |
| `groups` | Pole JSON identifikátorů GUID | Poskytuje ID objektů, které představují členství ve skupině daného subjektu. Tyto hodnoty jsou jedinečné (viz ID objektu) a lze je bezpečně použít ke správě přístupu, jako je vynucení autorizace pro přístup k prostředku. Skupiny zahrnuté v deklaraci skupin jsou nakonfigurovány na základě jednotlivých aplikací prostřednictvím vlastnosti `groupMembershipClaims` [manifestu aplikace](reference-app-manifest.md). Hodnota null bude vyloučit všechny skupiny, hodnota "Security Group" bude zahrnovat pouze členství ve skupině zabezpečení služby Active Directory a hodnota "vše" bude zahrnovat skupiny zabezpečení a distribuční seznamy Office 365. <br><br>Podrobnosti o použití deklarace identity `groups` s implicitním udělením najdete v níže uvedené deklaraci `hasgroups`. <br>U ostatních toků platí, že pokud počet skupin, ve kterých se uživatel nachází v rámci limitu (150 pro SAML, 200 pro JWT), se do zdrojů deklarací identity, které odkazují na koncový bod grafu AAD, přidá seznam skupin pro uživatele. |
| `hasgroups` | Logická hodnota | V případě, že je k dispozici vždy `true`, označuje uživatele alespoň jednu skupinu. Používá se místo deklarace `groups` v JWTs v části implicitní udělení toků, pokud by deklarace identity celé skupiny rozšířila fragment identifikátoru URI za omezení délky adresy URL (aktuálně 6 nebo více skupin). Indikuje, že klient by měl pomocí grafu určit skupiny uživatelů (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objekt JSON | V případě požadavků na tokeny, které nejsou omezené (viz `hasgroups` výše), ale u tokenu jsou pořád příliš velké, budou zahrnuty odkazy na seznam úplných skupin pro uživatele. Pro JWTs jako distribuovanou deklaraci SAML jako novou deklaraci identity `groups`. <br><br>**Ukázková hodnota JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Řetězec, identifikátor GUID | Objekt zabezpečení, o kterém token vyhodnotí informace, jako je například uživatel aplikace Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k bezpečnému provádění kontrol autorizace, například když se token používá pro přístup k prostředku a dá se použít jako klíč v databázových tabulkách. Vzhledem k tomu, že předmět je vždy přítomen v tokenech, které služba Azure AD vystavuje, doporučujeme použít tuto hodnotu v systému autorizace pro obecné účely. Subjekt je však párový identifikátor, který je jedinečný pro konkrétní ID aplikace. Proto pokud se jeden uživatel přihlásí ke dvěma různým aplikacím pomocí dvou různých ID klientů, obdrží tyto aplikace dvě různé hodnoty pro deklaraci předmětu. To může nebo nemusí být žádoucí v závislosti na vaší architektuře a požadavcích na ochranu osobních údajů. Viz také deklarace identity `oid` (která zůstává v aplikacích v rámci tenanta stejná). |
| `oid` | Řetězec, identifikátor GUID | Neproměnlivý identifikátor pro objekt na platformě Microsoft identity, v tomto případě uživatelský účet. Dá se taky použít k bezpečnému provádění kontrol autorizace a jako klíče v databázových tabulkách. Toto ID jednoznačně identifikuje uživatele napříč aplikacemi – dvě různé aplikace přihlášené ke stejnému uživateli získají stejnou hodnotu v `oid` deklaraci identity. Proto lze `oid` použít při zadávání dotazů do Microsoft online služby, jako je například Microsoft Graph. Microsoft Graph bude toto ID vracet jako vlastnost `id` pro daný [uživatelský účet](/graph/api/resources/user). Vzhledem k tomu, že `oid` umožňuje korelaci uživatelů více aplikacemi, je pro příjem této deklarace vyžadován rozsah `profile`. Všimněte si, že pokud jeden uživatel existuje ve více klientech, bude uživatel v každém tenantovi obsahovat jiné ID objektu – považují se za jiné účty, i když se uživatel do každého účtu přihlašuje pomocí stejných přihlašovacích údajů. |
| `tid` | Řetězec, identifikátor GUID | Představuje tenanta Azure AD, ze kterého uživatel pochází. V případě pracovních a školních účtů je identifikátor GUID neměnné ID klienta organizace, do které uživatel patří. U osobních účtů je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`. Aby bylo možné získat tuto deklaraci, je vyžadován obor `profile`. |
| `unique_name` | Řetězec | K dispozici pouze v tokenech v 1.0. Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. Tato hodnota není zaručena jako jedinečná v rámci tenanta a měla by být použita pouze pro účely zobrazení. |
| `uti` | Neprůhledný řetězec | Interní deklarace identity, kterou Azure používá k opětovnému ověření tokenů. Prostředky by tuto deklaraci neměli používat. |
| `rh` | Neprůhledný řetězec | Interní deklarace identity, kterou Azure používá k opětovnému ověření tokenů. Prostředky by neměly tuto deklaraci identity používat. |
| `ver` | Řetězec, buď `1.0`, nebo `2.0` | Určuje verzi přístupového tokenu. |


> [!NOTE]
> **Nadlimitní deklarace skupin**
>
> Aby se zajistilo, že velikost tokenu nepřekračuje limity velikosti hlavičky HTTP, Azure AD omezí počet ID objektů, které obsahuje, do deklarace skupiny. Pokud je uživatel členem více skupin, než je limit překročení (150 pro tokeny SAML, 200 pro tokeny JWT), služba Azure AD negeneruje v tokenu deklaraci identity skupin. Místo toho zahrnuje nadlimitní deklaraci identity v tokenu, která označuje aplikaci pro dotaz na Graph API k načtení členství uživatele ve skupině.
  ```csharp
  {
    ...
    "_claim_names": {
     "groups": "src1"
      },
      {
    "_claim_sources": {
      "src1": {
          "endpoint":"[Graph Url to get this user's group membership from]"
          }
         }
       }
    ...
   }
   ```
> Můžete použít `BulkCreateGroups.ps1` poskytované ve složce [Scripts pro vytváření aplikací](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) , které vám pomůžou vyzkoušet scénáře překročení limitu.

#### <a name="v10-basic-claims"></a>deklarace identity Basic v 1.0

Pokud je to možné, budou v tokenech v 1.0 zahrnuté tyto deklarace, ale ve výchozím nastavení nejsou zahrnuté v tokenech verze 2.0. Pokud používáte v 2.0 a potřebujete jednu z těchto deklarací, požádejte o ně použití [volitelných deklarací identity](active-directory-optional-claims.md).

| Deklarovat | Formát | Popis |
|-----|--------|-------------|
| `ipaddr`| Řetězec | IP adresa, ze které uživatel ověřil. |
| `onprem_sid`| Řetězec ve [formátu SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | V případech, kdy má uživatel místní ověřování, tato deklarace identity poskytuje identifikátor SID. `onprem_sid` můžete použít pro autorizaci ve starších verzích aplikací.|
| `pwd_exp`| int, časové razítko systému UNIX | Určuje, kdy vyprší platnost hesla uživatele. |
| `pwd_url`| Řetězec | Adresa URL, kam lze odeslat uživatele pro resetování hesla. |
| `in_corp`| Boolean | Signalizuje, že se klient přihlašuje z podnikové sítě. Pokud nejsou, deklarace identity není zahrnutá. |
| `nickname`| Řetězec | Další název uživatele, který je oddělený od prvního nebo posledního jména.|
| `family_name` | Řetězec | Poskytuje příjmení, příjmení nebo rodinné jméno uživatele, jak je definováno v objektu User. |
| `given_name` | Řetězec | Poskytuje první nebo křestní jméno uživatele, jak je nastaveno u objektu User. |
| `upn` | Řetězec | Uživatelské jméno uživatele Může se jednat o telefonní číslo, e-mailovou adresu nebo neformátovaný řetězec. By se měla použít jenom pro účely zobrazení a poskytování pomocných parametrů uživatelského jména ve scénářích pro opakované ověření. |

#### <a name="the-amr-claim"></a>Deklarace `amr`

Identity Microsoftu se můžou ověřovat různými způsoby, které můžou být relevantní pro vaši aplikaci. Deklarace `amr` je pole, které může obsahovat více položek, jako je například `["mfa", "rsa", "pwd"]`, pro ověřování, které používalo heslo i ověřovací aplikaci.

| Hodnota | Popis |
|-----|-------------|
| `pwd` | Ověřování hesla: buď heslo Microsoftu, nebo tajný klíč klienta aplikace. |
| `rsa` | Ověřování bylo založeno na ověření klíče RSA, například u [aplikace Microsoft Authenticator](https://aka.ms/AA2kvvu). To zahrnuje případ, kdy ověřování proběhlo pomocí tokenu JWT podepsaného svým držitelem s certifikátem x509 vlastněné služby. |
| `otp` | Jednorázové heslo pomocí e-mailu nebo textové zprávy. |
| `fed` | Byl použit kontrolní výraz federovaného ověřování (například JWT nebo SAML). |
| `wia` | Integrované ověřování systému Windows |
| `mfa` | Bylo použito Multi-Factor Authentication. V takovém případě budou zahrnuty i další metody ověřování. |
| `ngcmfa` | Ekvivalent `mfa`, který se používá pro zřizování určitých pokročilých typů přihlašovacích údajů. |
| `wiaormfa`| Uživatel použil k ověření přihlašovací údaje systému Windows nebo MFA. |
| `none` | Neudělalo se žádné ověřování. |

## <a name="validating-tokens"></a>Ověřování tokenů

Pokud chcete ověřit id_token nebo access_token, vaše aplikace by měla ověřit signaturu tokenu i deklarace identity. K ověření přístupových tokenů by vaše aplikace měla také ověřit vystavitele, cílovou skupinu a tokeny podepisování. Tyto hodnoty je nutné ověřit proti hodnotám v dokumentu zjišťování OpenID. Například verze dokumentu nezávislá na klientovi je umístěna na adrese [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Middleware Azure AD obsahuje integrované funkce pro ověřování přístupových tokenů a můžete procházet pomocí našich [ukázek](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) a vyhledat je v jazyce podle vašeho výběru. Další informace o tom, jak explicitně ověřit token JWT, najdete v [ukázce ručního ověření JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Poskytujeme knihovny a ukázky kódu, které ukazují, jak snadno zpracovat ověření tokenu. Níže uvedené informace jsou k dispozici pro uživatele, kteří chtějí pochopit původní proces. K dispozici je také několik Open-Source knihoven třetích stran, které jsou k dispozici pro ověření JWT – k dispozici je alespoň jedna možnost pro skoro každou platformu a jazyk. Další informace o knihovnách ověřování Azure AD a ukázkách kódu najdete v tématu knihovny [ověřování v 1.0](active-directory-authentication-libraries.md) a [knihovny ověřování v 2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Ověřování podpisu

Token JWT obsahuje tři segmenty, které jsou odděleny znakem `.`. První segment je označován jako **záhlaví**, druhý jako **tělo**a třetí jako **podpis**. Segment podpisu lze použít k ověření pravosti tokenu, aby mohl být vaší aplikací důvěryhodný.

Tokeny vydané službou Azure AD jsou podepsané pomocí standardních asymetrických šifrovacích algoritmů, jako je například RSA 256. Záhlaví tokenu JWT obsahuje informace o klíči a metodě šifrování použité k podepsání tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Deklarace `alg` označuje algoritmus, který se použil k podepsání tokenu, zatímco deklarace `kid` označuje konkrétní veřejný klíč, který se použil k ověření tokenu.

V jakémkoli daném časovém okamžiku může Azure AD podepsat id_token pomocí některé z určitých dvojic veřejných a privátních klíčů. Azure AD v pravidelných intervalech otáčí možné sady klíčů, takže by měla být vaše aplikace vytvořená tak, aby se tyto klíčové změny automaticky zpracovaly. Přiměřenou frekvencí pro kontrolu aktualizací veřejných klíčů používaných službou Azure AD je každých 24 hodin.

Data podpisového klíče, která jsou nutná k ověření podpisu, můžete získat pomocí [dokumentu metadat OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) v umístění:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Zkuste tuto [adresu URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) v prohlížeči.

Tento dokument metadat:

* Je objekt JSON obsahující několik užitečných informací, jako je například umístění různých koncových bodů vyžadovaných pro ověřování OpenID Connect.
* Obsahuje `jwks_uri`, který poskytuje umístění sady veřejných klíčů použitých k podepisování tokenů. Webový klíč JSON (JWK) umístěný v `jwks_uri` obsahuje všechny informace o veřejném klíči, které se používají v daném časovém okamžiku.  Formát JWK je popsaný v [dokumentu RFC 7517](https://tools.ietf.org/html/rfc7517).  Vaše aplikace může pomocí deklarace identity `kid` v hlavičce JWT vybrat, který veřejný klíč v tomto dokumentu se použil k podepsání konkrétního tokenu. Pak může ověřit podpis pomocí správného veřejného klíče a uvedeného algoritmu.

> [!NOTE]
> Koncový bod v 1.0 vrací deklarace `x5t` i `kid`, zatímco koncový bod v 2.0 odpoví pouze deklarací `kid`. Až dál, doporučujeme k ověření tokenu použít deklaraci `kid`.

Ověřování podpisu je mimo rámec tohoto dokumentu – k dispozici je mnoho Open Source knihoven, které vám pomohou v případě potřeby.  Microsoft Identity Platform ale má jedno rozšíření pro podepisování tokenů pro vlastní podpisové klíče.  

Pokud má vaše aplikace vlastní podpisové klíče v důsledku použití funkce [mapování deklarací identity](active-directory-claims-mapping.md) , musíte připojit parametr `appid` dotazu obsahujícího ID aplikace a získat tak `jwks_uri` odkaz na informace o podpisovém klíči vaší aplikace, které by se měly použít k ověření. Například: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorizace na základě deklarací identity

Obchodní logika vaší aplikace bude diktovat tento krok, některé běžné autorizační metody jsou uvedeny níže.

* Zkontrolujte deklaraci identity `scp` nebo `roles`, abyste ověřili, že všechny tyto obory odpovídají stavům vystaveným rozhraním API, a umožní klientovi, aby procházel požadovanou akci.
* Zajistěte, aby volající klient povolil volání rozhraní API pomocí deklarace `appid`.
* Ověří stav ověřování volajícího klienta pomocí `appidacr` – neměla by být 0, pokud veřejné klienty nemůžou volat vaše rozhraní API.
* Zkontrolujte seznam předchozích `nonce` deklarací identity, abyste ověřili, že se token nebude znovu přehrávat.
* Ověřte, že `tid` odpovídá klientovi, který má povoleno volat vaše rozhraní API.
* Pomocí deklarace identity `acr` ověřte, že uživatel provedl MFA. Tato podmínka by se měla vyhovět pomocí [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Pokud jste si vyžádali `roles` nebo `groups` deklarací identity v přístupovém tokenu, ověřte, jestli je uživatel ve skupině, na kterou se tato akce povoluje.
  * Pro tokeny načtené pomocí implicitního toku bude pravděpodobně nutné zadat dotaz na [Microsoft Graph](https://developer.microsoft.com/graph/) pro tato data, protože je často příliš velká, aby se vešla do tokenu.

## <a name="user-and-application-tokens"></a>Tokeny uživatelů a aplikací

Vaše aplikace může přijímat tokeny jménem uživatele (běžný tok) nebo přímo z aplikace (prostřednictvím [toku přihlašovacích údajů klienta](v1-oauth2-client-creds-grant-flow.md)). Tyto tokeny pouze pro aplikace označují, že toto volání přichází z aplikace a nemá uživatele, který ho zavedl. Tyto tokeny jsou zpracovávány hlavně stejně, s několika rozdíly:

* Tokeny jenom pro aplikace nebudou mít `scp` deklaraci identity a můžou mít `roles` deklaraci identity. V takovém případě se budou zaznamenávat oprávnění aplikace (na rozdíl od delegovaných oprávnění). Další informace o oprávněních delegovaných a aplikací najdete v tématu oprávnění a souhlas v v [1.0](v1-permissions-and-consent.md) a [v 2.0](v2-permissions-and-consent.md).
* V takovém případě bude k dispozici mnoho deklarací specifických pro člověka, například `name` nebo `upn`.
* Deklarace `sub` a `oid` budou stejné. 

## <a name="token-revocation"></a>Odvolání tokenu

Aktualizace tokenů se u různých důvodů může odhlásit nebo odvolat kdykoli. Patří do dvou hlavních kategorií: vypršení časových limitů a odvolaných certifikátů.

### <a name="token-timeouts"></a>Vypršení časového limitu tokenů

* MaxInactiveTime: Pokud se obnovovací token nepoužil v čase, který určí MaxInactiveTime, obnovovací token už nebude platný.
* MaxSessionAge: Pokud jsou MaxAgeSessionMultiFactor nebo MaxAgeSessionSingleFactor nastavené na jinou hodnotu než výchozí (před odvoláním), pak se opětovné ověření bude vyžadovat po uplynutí doby nastavené v MaxAgeSession *.
* Příklady:
  * Tenant má MaxInactiveTime po dobu pěti dnů a uživatel přešel na dovolenou za týden, takže Azure AD neviděl od uživatele novou žádost o token během 7 dnů. Když si uživatel příště požádá o nový token, uvidí svůj obnovovací token, který pak musí znovu zadat přihlašovací údaje.
  * Citlivá aplikace má MaxAgeSessionSingleFactor jeden den. Pokud se uživatel přihlásí v pondělí a v úterý (po 25 hodinách uplynul), bude se muset znovu ověřit.

### <a name="revocation"></a>Odvolání

|   | Soubor cookie založený na hesle | Token založený na hesle | Soubory cookie nezaložené na heslech | Token založený na jiných heslech | Důvěrný token klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Platnost hesla vyprší | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Heslo změnilo uživatel. | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Uživatel provede SSPR | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Správce resetuje heslo | Odvoláno | Odvoláno | Zůstane aktivní | Zůstane aktivní | Zůstane aktivní |
| Uživatel Odvolá své aktualizační tokeny [prostřednictvím PowerShellu](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) . | Odvoláno | Odvoláno | Odvoláno | Odvoláno | Odvoláno |
| Správce odvolá všechny aktualizační tokeny pro tenanta [přes PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) . | Odvoláno | Odvoláno |Odvoláno | Odvoláno | Odvoláno |
| [Jednotné přihlašování](v1-protocols-openid-connect-code.md#single-sign-out) na webu | Odvoláno | Zůstane aktivní | Odvoláno | Zůstane aktivní | Zůstane aktivní |

> [!NOTE]
> Přihlášení "nezaložené na heslech" je jedno, kde uživatel nezadal heslo pro jeho získání. Například použití vaší obličeje s Windows Hello, klíčem FIDO2 nebo PIN kódem.
>
> Primární obnovovací tokeny (PRT) ve Windows 10 jsou oddělené na základě přihlašovacích údajů. Například Windows Hello a heslo mají své odpovídající PRTs, které jsou izolované od sebe. Když se uživatel přihlásí pomocí přihlašovacích údajů Hello (PIN nebo biometrika) a pak změní heslo, bude odvolaný PRT založený na hesle, který jste dříve získali. Při opětovném přihlášení pomocí hesla se zruší platnost starého PRT a vyžádá se nový.
>
> Při použití k načtení nového přístupového tokenu a obnovení tokenu se tokeny pro aktualizaci neověřují nebo odvolají.  

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [`id_tokens` ve službě Azure AD](id-tokens.md).
* Přečtěte si o oprávnění a souhlasu v v [1.0](v1-permissions-and-consent.md) a v [2.0](v2-permissions-and-consent.md).
