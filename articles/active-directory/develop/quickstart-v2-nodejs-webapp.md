---
title: Přidání OIDC přihlašování do webové aplikace Node. js – Microsoft Identity Platform | Azure
description: Naučte se implementovat ověřování ve webové aplikaci Node. js pomocí OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2869333fe0e9103724b9b1075270a5c3301c472
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968013"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Rychlý Start: Přidání přihlašování pomocí OpenID připojení k webové aplikaci Node. js

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak nastavit ověřování OpenID Connect ve webové aplikaci vytvořené pomocí Node. js s Express. Ukázka je navržená tak, aby běžela na jakékoli platformě.

## <a name="prerequisites"></a>Předpoklady

K provedení této ukázky budete potřebovat:

* Instalace Node. js z http://nodejs.org/

* [Účet Microsoft](https://www.outlook.com) nebo [vývojářský Program sady Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registrace vaší aplikace 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD:
    - V nabídce v pravém horním rohu stránky vyberte svůj profil a pak **Přepněte do adresáře**.
    - Změňte svou relaci na tenanta Azure AD, ve kterém chcete vytvořit aplikaci.

1. Pro registraci aplikace přejděte na [Azure Active Directory > Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) .

1. Vyberte **Nová registrace.**

1. Jakmile se zobrazí stránka **Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
    - V části **název** zadejte smysluplný název, který se zobrazí uživatelům aplikace. Příklad: MyWebApp
    - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a v osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)** .

    Pokud existuje více identifikátorů URI přesměrování, budete je muset přidat z karty **ověřování** později po úspěšném vytvoření aplikace.

1. Pokud chcete vytvořit aplikaci, vyberte **zaregistrovat** .

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Tuto hodnotu budete potřebovat ke konfiguraci aplikace později v tomto projektu.

1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
    - V části **identifikátory URI pro přesměrování** vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátor URI pro přesměrování: `http://localhost:3000/auth/openid/return`
    - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `http://localhost:3000`.
    - V části **Upřesnit nastavení > implicitního udělení oprávnění** ověřte **tokeny ID** , protože tato ukázka vyžaduje, aby byl [tok implicitního udělení](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) povolen k přihlášení uživatele.

1. Vyberte **Save** (Uložit).

1. Na stránce **certifikáty & tajné klíče** v části **tajné klíče klienta** vyberte možnost **nový tajný klíč klienta**.
    - Zadejte popis klíče (např. tajný klíč aplikace).
    - Vyberte dobu trvání klíče buď **v 1 roce, 2 roky,** nebo **nikdy nevyprší**.
    - Po kliknutí na tlačítko **Přidat** se zobrazí hodnota klíč. Zkopírujte hodnotu klíče a uložte ji do bezpečného umístění.

    Tento klíč budete potřebovat později ke konfiguraci aplikace. Tato hodnota klíče se znovu nezobrazí ani není dostupná žádným jiným způsobem, takže ji nahrajte hned, jak je vidět z Azure Portal.

## <a name="download-the-sample-application-and-modules"></a>Stažení ukázkové aplikace a modulů

Dále naklonujte ukázkové úložiště a nainstalujte moduly NPM.

Z prostředí nebo příkazového řádku:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

nebo

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Z kořenového adresáře projektu spusťte příkaz:

`$ npm install`  

## <a name="configure-the-application"></a>Konfigurace aplikace

Zadejte parametry v `exports.creds` config. js podle pokynů.

* Aktualizujte `<tenant_name>` v `exports.identityMetadata` s názvem tenanta Azure AD ve formátu \*. onmicrosoft.com.
* Aktualizujte `exports.clientID` s ID aplikace zaznamenanou v registraci aplikace.
* Aktualizujte `exports.clientSecret` s použitím tajného kódu aplikace zjištěného při registraci aplikace.
* Aktualizujte `exports.redirectUrl` s identifikátorem URI přesměrování zaznamenaným z registrace aplikace.

**Volitelná konfigurace pro produkční aplikace:**

* Pokud chcete použít jinou `post_logout_redirect_uri`, aktualizujte `exports.destroySessionUrl` v souboru config. js.

* Nastavte `exports.useMongoDBSessionStore` v souboru config. js na hodnotu true, pokud chcete použít [MongoDB](https://www.mongodb.com) nebo jiná [kompatibilní úložiště relací](https://github.com/expressjs/session#compatible-session-stores).
Výchozí úložiště relací v této ukázce je `express-session`. Výchozí úložiště relací není vhodné pro produkční prostředí.

* Aktualizujte `exports.databaseUri`, pokud chcete použít úložiště relace mongoDB a jiný identifikátor URI databáze.

* Aktualizace `exports.mongoDBSessionMaxAge`. Tady můžete určit, jak dlouho chcete uchovat relaci v mongoDB. Jednotka má sekundu (y).

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Spusťte službu mongoDB. Pokud v této aplikaci používáte úložiště relací mongoDB, musíte [nainstalovat MongoDB](http://www.mongodb.org/) a nejdřív službu spustit. Pokud používáte výchozí úložiště relací, můžete tento krok přeskočit.

Spusťte aplikaci pomocí následujícího příkazu z příkazového řádku.

```
$ node app.js
```

**Je pro vás výstup serveru obtížné?:** K přihlášení v této ukázce používáme `bunyan`. Konzola nebude velmi smyslná, pokud nenainstalujete Bunyan a spustíte server, jak je uvedeno výše, ale přesměrujte ho prostřednictvím binárního souboru Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>A je to hotové!

Na `http://localhost:3000`budete mít úspěšné spuštění serveru.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o scénáři webové aplikace, kterou podporuje platforma Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Webová aplikace, která se podepisuje ve scénáři uživatelů](scenario-web-app-sign-user-overview.md)
