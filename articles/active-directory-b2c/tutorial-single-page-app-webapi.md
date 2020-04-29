---
title: 'Kurz: ochrana webového rozhraní API Node. js pomocí Azure AD B2C a udělení přístupu k jednostránkové aplikaci (SPA)'
titleSuffix: Azure AD B2C
description: V tomto kurzu se naučíte, jak použít Active Directory B2C k ochraně webového rozhraní API Node. js a jeho volání z jednostránkové aplikace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875663"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Kurz: Chraňte a udělte přístup k webovému rozhraní API Node. js z jednostránkové aplikace s Azure AD B2C

V tomto kurzu se dozvíte, jak volat webové rozhraní API Node. js chráněného Azure Active Directory B2C (Azure AD B2C) z jednostránkové aplikace.

V tomto kurzu druhý v řadě dvou částí:

> [!div class="checklist"]
> * Vytvoření registrace aplikace webového rozhraní API ve vašem tenantovi Azure AD B2C
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Úprava ukázky kódu webového rozhraní API pro spolupráci s vaším klientem

V [prvním kurzu](tutorial-single-page-app.md) této série jste stáhli ukázku kódu a změnili ji tak, aby se uživatelé přihlásili pomocí toku uživatele v Azure AD B2C tenantovi.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky a požadavky v [kurzu: povolení ověřování v aplikaci s jednou stránkou s Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) nebo jiný Editor kódu
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu definujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Poznamenejte si `demo.read` hodnotu **rozsah,** který se má použít v pozdějším kroku při konfiguraci jednostránkové aplikace. Úplná hodnota oboru je podobná `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z jiné aplikace, je třeba udělit oprávnění aplikace webovému rozhraní API.

V kurzu požadavků jste vytvořili webovou aplikaci s názvem *WebApp1*. V tomto kurzu nakonfigurujete tuto aplikaci tak, aby volala webové rozhraní API, které jste vytvořili v předchozí části, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Vaše webová aplikace s jednou stránkou má teď udělená oprávnění k chráněnému webovému rozhraní API pro zadané obory. Uživatel se ověřuje pomocí Azure AD B2C pro použití jednostránkové aplikace. Jednostránkové aplikace používá tok udělení autorizace pro přístup k chráněnému webovému rozhraní API s přístupovým tokenem vráceným Azure AD B2C.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a Vy jste definovali obory, nakonfigurujte kód webového rozhraní API tak, aby fungoval s vaším klientem Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API Node. js, které stáhnete z GitHubu.

[Stáhněte si \*archiv. zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkový projekt webového rozhraní API z GitHubu. Můžete také přejít přímo na projekt [Azure-Samples/Active-Directory-B2C-JavaScript-NodeJS-WebApi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) na GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor *config. js* v editoru kódu.
1. Upravte hodnoty proměnných tak, aby odrážely ty, které jste vytvořili dříve. Aktualizujte `policyName` také tok uživatele, který jste vytvořili jako součást požadavků. Například *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>Povolení CORS

Chcete-li povolit, aby vaše jednostránková aplikace volala webové rozhraní API Node. js, je nutné povolit [CORS](https://expressjs.com/en/resources/middleware/cors.html) ve webovém rozhraní API. V produkční aplikaci byste měli mít pozor na to, která doména požadavek vydává, ale v tomto kurzu umožníte žádostem z libovolné domény.

Pokud chcete povolit CORS, použijte následující middleware. V ukázce kódu webového rozhraní API Node. js v tomto kurzu už je to přidané do souboru *index. js* .

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránkové aplikace (SPA) z [předchozího kurzu](tutorial-single-page-app.md) v řadě používá Azure AD B2C pro registraci a přihlašování uživatelů a ve výchozím nastavení volá webové rozhraní API Node. js chráněné *fabrikamb2c* ukázkovým klientem.

V této části provedete aktualizaci jednostránkové webové aplikace tak, aby volala webové rozhraní API Node. js chráněné *vaším* Azure AD B2C tenant (a který spustíte v místním počítači).

Změna nastavení v zabezpečeném hesla:

1. V projektu [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] , který jste stáhli nebo naklonoval v předchozím kurzu, otevřete soubor *apiConfig. js* ve složce *JavaScriptSPA* .
1. Nakonfigurujte ukázku s identifikátorem URI pro obor *demo. Read* , který jste vytvořili dříve, a adresu URL webového rozhraní API.
    1. V `apiConfig` definici `b2cScopes` nahraďte hodnotu úplným identifikátorem URI pro *demo. Read* Scope (hodnota **oboru** , kterou jste si poznamenali dříve).
    1. Změňte doménu v `webApi` hodnotě na identifikátor URI přesměrování, který jste přidali při registraci aplikace webového rozhraní API v předchozím kroku.

    Vzhledem k tomu, že rozhraní API `/hello` je dostupné na koncovém bodu, ponechte */Hello* v identifikátoru URI.

    `apiConfig` Definice by měla vypadat podobně jako následující blok kódu, ale s názvem vašeho tenanta B2C na místě `<your-tenant-name>`:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Spuštění ZABEZPEČENÉho a webového rozhraní API

Nyní jste připraveni otestovat přístup k rozhraní API s oborem aplikace na jednu stránku. Spusťte jak webové rozhraní API Node. js, tak ukázkovou jednostránkovou aplikaci JavaScriptu na místním počítači. Pak se přihlaste k jednostránkové aplikaci a výběrem tlačítka **rozhraní API volání** zahajte požadavek na chráněné rozhraní API.

I když jsou obě aplikace spuštěné v tomto kurzu místně, jste je nakonfigurovali tak, aby používaly Azure AD B2C pro zabezpečené registrace, přihlašování a udělení přístupu k chráněnému webovému rozhraní API.

### <a name="run-the-nodejs-web-api"></a>Spuštění webového rozhraní API Node. js

1. Otevřete okno konzoly a přejděte do adresáře obsahujícího ukázku webového rozhraní API Node. js. Příklad:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    node index.js
    ```

    V okně konzoly se zobrazí číslo portu, na kterém je aplikace hostovaná.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Spuštění jednostránkové aplikace

1. Otevřete jiné okno konzoly a přejděte do adresáře, který obsahuje ukázku kódu JavaScript SPA. Příklad:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    npm start
    ```

    V okně konzoly se zobrazí číslo portu, ve kterém je aplikace hostovaná.

    ```console
    Listening on port 6420...
    ```

1. Přejděte do `http://localhost:6420` prohlížeče, aby se zobrazila aplikace.

    ![Ukázková aplikace s jednoduchou stránkou zobrazená v prohlížeči](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [předchozím kurzu](tutorial-single-page-app.md). Po úspěšném přihlášení by se měla zobrazit `User 'Your Username' logged-in` zpráva.
1. Vyberte tlačítko **rozhraní API pro volání** . SPA získá udělení autorizace z Azure AD B2C a pak přístup k chráněnému webovému rozhraní API, aby zobrazilo jméno přihlášeného uživatele:

    ![Jednostránkové aplikace v prohlížeči ukazující výsledek uživatelského jména JSON vrácený rozhraním API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Ve vašem tenantovi Azure AD B2C se vytvořila registrace aplikace webového rozhraní API.
> * Nakonfigurované obory pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Upravení ukázky kódu webového rozhraní API pro spolupráci s vaším klientem

Teď, když jste se seznámili s požadavkem na prostředek z chráněného webového rozhraní API, získáte lepší přehled o tom, jak tyto typy aplikací vzájemně komunikují a s Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikací, které lze použít v Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
