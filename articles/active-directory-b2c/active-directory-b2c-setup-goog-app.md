---
title: Nastavení registrace a přihlášení pomocí účtu Google – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Google ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508507"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Google pomocí Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace služby Google

Pro použití jako účtu Google [zprostředkovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory (Azure AD) B2C, budete potřebovat k vytvoření aplikace ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Google můžete získat na [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Přihlaste se k [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů účtu Google.
2. V levém horním rohu stránky vyberte seznam projektů a pak vyberte **nový projekt**.
3. Zadejte **název projektu**, klikněte na tlačítko **vytvořit**a zkontrolujte, že používáte nový projekt.
4. Vyberte **pověření** v nabídce vlevo a pak vyberte **Vytvořte přihlašovací údaje** > **ID klienta Oauth**.
5. V části **typ aplikace**vyberte **webovou aplikaci**.
6. Zadejte **název** pro vaši aplikaci, zadejte `https://your-tenant-name.b2clogin.com` v **oprávnění JavaScript zdroje**, a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **identifikátory URI pro přesměrování autorizovaní**. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C.
7. Klikněte na možnost **Vytvořit**.
8. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Budete potřebovat oba poskytovatelé konfigurace Google jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurace Google účtu jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Google*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Google**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace Google, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Google.

