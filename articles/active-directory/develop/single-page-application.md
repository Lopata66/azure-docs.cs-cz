---
title: Jednostránkové aplikace v Azure Active Directory
description: Popisuje, jaké jednostránkové aplikace (SPA) jsou a základní informace o protokolu toku, registrace a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f6f66779bec9ed4e38e5a662c2d3728ba2034b6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545298"
---
# <a name="single-page-applications"></a>Jednostránkové aplikace

Jednostránkové aplikace (SPA) jsou obvykle strukturované jako JavaScript prezentační vrstvy (front-end), který běží v prohlížeči a webového rozhraní API back-end, který běží na serveru a implementuje obchodní logiku aplikace. Další informace o implicitním udělení autorizace a vám pomohou rozhodnout, jestli je vhodná pro váš scénář aplikace najdete v tématu [Principy OAuth2 implicitní tok ve službě Azure Active Directory udělit](v1-oauth2-implicit-grant-flow.md).

V tomto scénáři, když se uživatel přihlásí, JavaScript front-end využívá [Active Directory Authentication Library pro JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) a implicitním udělení autorizace k získání tokenu ID (id_token) ze služby Azure AD. Token se uloží do mezipaměti a klient připojí ho k požadavku jako nosný token při volání do své webové rozhraní API back-end, který je zabezpečený pomocí middlewaru OWIN.

## <a name="diagram"></a>Diagram

![Diagram jednostránkové aplikace](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protokol toku

1. Uživatel přejde na webovou aplikaci.
1. Aplikace vrátí front-endu JavaScriptu (prezentační vrstva) do prohlížeče.
1. Uživatel inicializuje přihlášení, například klepnutím na odkaz přihlášení. Prohlížeč odesílá GET na koncový bod autorizace Azure AD k vyžádání tokenu ID. Tato žádost obsahuje adresu URL pro ID a odpověď aplikace v parametrech dotazu.
1. Azure AD ověří příslušnou odpovědní adresu URL vůči registrovaná adresa URL odpovědi, který byl nakonfigurován na webu Azure Portal.
1. Uživatel se přihlásí na přihlašovací stránku.
1. Pokud je ověření úspěšné, Azure AD vytvoří ID token a vrátí ji do adresy URL odpovědi aplikace jako fragment adresy URL (#). Pro produkční aplikace tato adresa URL odpovědi by mělo být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou vyžadované aplikací k ověření tokenu.
1. Kód jazyka JavaScript klienta, spouštění v prohlížeči extrahuje token z odpovědi používané k zabezpečení volání aplikace webového že rozhraní API zpět ukončit.
1. Prohlížeč volání aplikace webového rozhraní API zpět končit ID tokenu v autorizační hlavičce. Ověřování služby Azure AD vydá token ID, který může sloužit jako nosný token, pokud prostředek je stejné jako ID klienta (v tomto případě to platí je webové rozhraní API back-endu vaší aplikace vlastní).

## <a name="code-samples"></a>Ukázky kódu

Zobrazit [ukázky kódu pro scénáře jednostránková aplikace](sample-v1-code.md#single-page-applications). Nezapomeňte se podívat zpět často, jak často jsou přidávány nové ukázky.

## <a name="app-registration"></a>Registrace aplikace

* Jednoho tenanta – Pokud vytváříte aplikaci pouze pro vaši organizaci, je nutné jej zaregistrovat v adresáři vaší firmy s využitím webu Azure portal.
* Víceklientské – Pokud vytváříte aplikaci, která je možné uživatele mimo vaši organizaci ho musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. K zajištění dostupnosti v jejich adresáři aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, díky kterým souhlas s vaší aplikací. Při registraci pro vaši aplikaci, se zobrazí se dialogové okno zobrazující oprávnění, která aplikace požaduje instalaci a pak možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění může být správcem v druhé organizaci nutné vyjádřit souhlas. Když uživatel nebo správce souhlasí, aplikace je zaregistrovaný v jejich adresáře.

Po registraci aplikace, musí být nakonfigurován pro použití protokolu implicitní grant OAuth 2.0. Ve výchozím nastavení je tento protokol zakázán pro aplikace. Pokud chcete povolit protokol implicitní grant OAuth2 pro vaši aplikaci, úprava manifestu aplikace z portálu Azure portal a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu true. Další informace najdete v tématu [manifest aplikace](reference-app-manifest.md).

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Použití ADAL.js pomáhá s:

* aktualizaci vypršenou platností tokenu
* žádání o přístupový token k volání webového rozhraní API prostředku

Po úspěšném ověření Azure AD zapisuje do souboru cookie v prohlížeči uživatele k vytvoření relace. Všimněte si, že relace existuje mezi uživatelem a Azure AD (není mezi uživatelem a webové aplikace). Když vyprší platnost tokenu, ADAL.js používá tuto relaci tiše získat nový token. ADAL.js používá skrytý element iFrame pro odesílání a přijímání požadavků pomocí protokolu implicitní grant OAuth. ADAL.js můžete také použít tento stejný mechanismus tiše získat přístupové tokeny pro prostředky webového rozhraní API, které aplikace volá tak dlouho, dokud prostředků mezi zdroji (CORS) pro sdílení obsahu podporují tyto prostředky jsou registrovány v adresáři uživatele a všechny požadované souhlasu se zadaný uživatelem při přihlášení.

## <a name="next-steps"></a>Další postup

* Další informace o dalších [typy aplikací a scénářů](app-types.md)
* Další informace o službě Azure AD [základy ověřování](authentication-scenarios.md)
