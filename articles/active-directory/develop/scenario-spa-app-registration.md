---
title: Registrovat jednostránkové aplikace (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet jednostránkové aplikace (registrace aplikací).
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9dc5b446e2ab26ca43c2a300e1af1237353325a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83682392"
---
# <a name="single-page-application-app-registration"></a>Jednostránkové aplikace: registrace aplikace

K registraci jednostránkové aplikace (SPA) na platformě Microsoft Identity proveďte následující kroky. Registrační postup se liší od MSAL.js 1,0, který podporuje tok implicitního udělení a MSAL.js 2,0, který podporuje tok autorizačního kódu s PKCE.

## <a name="create-the-app-registration"></a>Vytvoření registrace aplikace

U aplikací založených na MSAL.js 1,0 a 2,0 začněte provedením následujících kroků a vytvořte prvotní registraci aplikace.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Pokud má váš účet přístup k více klientům, v horní nabídce vyberte filtr **adresář + předplatné** a pak vyberte tenanta, který by měl obsahovat registraci aplikace, kterou chcete vytvořit.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**, zadejte **název** aplikace a zvolte **podporované typy účtů** pro aplikaci. Nezadávejte **identifikátor URI přesměrování**. **NOT** Popis různých typů účtů najdete v tématu [Registrace nové aplikace pomocí Azure Portal](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).
1. Kliknutím na **Registrovat** vytvořte registraci aplikace.

V dalším kroku nakonfigurujte registraci aplikace pomocí **identifikátoru URI přesměrování** , abyste určili, kde má platforma Microsoft identity by měla přesměrovat klienta spolu s případnými tokeny zabezpečení. Použijte postup, který je vhodný pro verzi MSAL.js, kterou používáte ve vaší aplikaci:

- [MSAL.js 2,0 s tokem kódu ověřování](#redirect-uri-msaljs-20-with-auth-code-flow) (doporučeno)
- [MSAL.js 1,0 s implicitním tokem](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Identifikátor URI pro přesměrování: MSAL.js 2,0 s tokem kódu ověřování

Pomocí těchto kroků přidejte identifikátor URI přesměrování pro aplikaci, která používá MSAL.js 2,0 nebo novější. MSAL.js 2.0 + podporuje tok autorizačního kódu s PKCE a CORS v reakci na [omezení souborů cookie třetích stran v prohlížeči](reference-third-party-cookies-spas.md). V MSAL.js 2.0 + není podporován tok implicitního udělení.

1. V Azure Portal vyberte registraci aplikace, kterou jste vytvořili dříve v části [Vytvoření registrace aplikace](#create-the-app-registration).
1. V části **Spravovat**vyberte **ověřování**a pak vyberte **Přidat platformu**.
1. V části **webové aplikace**vyberte dlaždici **aplikace s jednou stránkou** .
1. V části **identifikátory URI pro přesměrování**zadejte [identifikátor URI pro přesměrování](reply-url.md). Nevybírejte **buď** CheckBox v rámci **implicitního udělení**.
1. Vyberte **Konfigurovat** a dokončete přidávání identifikátoru URI přesměrování.

Právě jste dokončili registraci jednostránkové aplikace (SPA) a nakonfigurovali identifikátor URI pro přesměrování, ke kterému bude klient přesměrován, a budou odeslány všechny tokeny zabezpečení. Když nakonfigurujete identifikátor URI pro přesměrování pomocí dlaždice **jednostránkové aplikace** v podokně **Přidat platformu** , registrace vaší aplikace je nakonfigurovaná tak, aby podporovala tok autorizačního kódu s PKCE a CORS.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Identifikátor URI pro přesměrování: MSAL.js 1,0 s implicitním tokem

Pomocí těchto kroků přidejte identifikátor URI pro přesměrování pro jednostránkovou aplikaci, která používá MSAL.js 1,3 nebo starší a implicitně udělený tok. Aplikace, které používají MSAL.js 1,3 nebo starší, nepodporují tok kódu ověřování.

1. V Azure Portal vyberte registraci aplikace, kterou jste vytvořili dříve v části [Vytvoření registrace aplikace](#create-the-app-registration).
1. V části **Spravovat**vyberte **ověřování**a pak vyberte **Přidat platformu**.
1. V části **webové aplikace**vyberte dlaždici **aplikace s jednou stránkou** .
1. V části **identifikátory URI pro přesměrování**zadejte [identifikátor URI pro přesměrování](reply-url.md).
1. Povolit **implicitní tok**:
    - Pokud se vaše aplikace přihlásí uživatelům, vyberte **tokeny ID**.
    - Pokud vaše aplikace také potřebuje volat chráněné webové rozhraní API, vyberte **přístupové tokeny**. Další informace o těchto typech tokenů najdete v tématu [tokeny ID](id-tokens.md) a [přístupové tokeny](access-tokens.md).
1. Vyberte **Konfigurovat** a dokončete přidávání identifikátoru URI přesměrování.

Právě jste dokončili registraci jednostránkové aplikace (SPA) a nakonfigurovali identifikátor URI pro přesměrování, ke kterému bude klient přesměrován, a budou odeslány všechny tokeny zabezpečení. Výběrem jednoho nebo obou **tokenů ID** a **přístupových tokenů**jste povolili postup implicitního udělení.

## <a name="note-about-authorization-flows"></a>Poznámka o autorizačních tocích

Ve výchozím nastavení je registrace aplikace vytvořená pomocí stránky konfigurace platformy s jednou stránkou umožňuje tok autorizačního kódu. Aby bylo možné využít tento tok, musí vaše aplikace používat MSAL.js 2,0 nebo novější.

Jak už bylo uvedeno dříve, jednostránkové aplikace používající MSAL.js 1,3 jsou omezeny na implicitní tok udělení. Aktuální [osvědčené postupy OAuth 2,0](v2-oauth2-auth-code-flow.md) doporučují použití toku autorizačního kódu místo implicitního toku pro jednostránkové. Použití obnovovacích tokenů s omezeným počtem dob také pomůže přizpůsobit aplikaci na [moderní omezení ochrany osobních údajů souborů cookie v prohlížeči](reference-third-party-cookies-spas.md), jako je Safari ITP.

Když všechny vaše produkční aplikace s jednou stránkou, které jsou reprezentované registrací aplikace, používají MSAL.js 2,0 a tok autorizačního kódu, zrušte v Azure Portal podokno **ověřování** registrace aplikace. Aplikace používající MSAL.js 1. x a implicitní tok mohou i nadále fungovat, ale pokud ponecháte povolený implicitní tok (zaškrtnuto).

## <a name="next-steps"></a>Další kroky

Dále nakonfigurujte kód vaší aplikace tak, aby používal registraci aplikace, kterou jste vytvořili v předchozích krocích:.

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-spa-app-configuration.md)
