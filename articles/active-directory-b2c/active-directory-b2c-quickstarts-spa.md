---
title: Rychlý start – Nastavení přihlášení pro jednostránkovou aplikaci pomocí služby Azure Active Directory B2C | Microsoft Docs
description: Spusťte ukázkovou jednostránkovou aplikaci využívající Azure Active Directory B2C k zajištění přihlášení k účtu.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2e5f16fa42e86442f6600e375df343f3b6369986
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190391"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý start: Nastavení přihlášení pro jednostránkové aplikace pomocí Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C poskytuje správu cloudových identit pro zajištění ochrany vašich aplikací, firmy a zákazníků. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.
- Instalovat [Node.js](https://nodejs.org/en/download/)
- Účet na sociální síti Facebook, Google, Microsoft nebo Twitter.
- [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. Spuštěním následujících příkazů na příkazovém řádku Node.js spusťte server: 

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js vrátí číslo portu, na kterém naslouchá na místním hostiteli.

    ```
    Listening on port 6420...
    ```

2. Přejděte na adresu URL aplikace. Například, `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Login** (Přihlášení) spusťte pracovní postup.

    ![Ukázková aplikace v prohlížeči](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    Ukázka podporuje několik možností registrace, včetně použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. Pro účely tohoto rychlého startu použijte účet zprostředkovatele sociální identity Facebook, Google, Microsoft nebo Twitter. 

2. Azure AD B2C zobrazí v ukázkové webové aplikaci vlastní přihlašovací stránku pro fiktivní značku Wingtip Toys. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Zprostředkovatel registrace nebo přihlašování](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Ověřování (přihlášení) se pomocí přihlašovacích údajů vašeho účtu na sociální síti a autorizujete aplikaci ke čtení informací z vašeho účtu na sociální síti. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město. 

3. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Klikněte na **Call Web API** (Zavolat webové rozhraní API), aby se ze zavolání webového rozhraní API vrátilo vaše zobrazované jméno jako objekt JSON. 

![Odpověď rozhraní API](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Ukázková jednostránková aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu použijete ukázkovou jednostránkovou aplikaci pro:

* Přihlaste se pomocí vlastní přihlašovací stránky
* Přihlaste se pomocí zprostředkovatele sociální identity
* Vytvoření účtu služby Azure AD B2C
* Volání webového rozhraní API chráněného službou Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
