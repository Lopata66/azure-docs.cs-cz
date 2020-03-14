---
title: Kurz – vytvoření uživatelských toků – Azure Active Directory B2C
description: Naučte se vytvářet toky uživatelů v Azure Portal, abyste mohli povolit registraci, přihlašování a úpravy uživatelských profilů pro vaše aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264242"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Kurz: vytvoření toků uživatelů v Azure Active Directory B2C

Ve vašich aplikacích můžete mít [uživatelské toky](user-flow-overview.md) , které uživatelům umožňují registraci, přihlašování a správu svého profilu. V tenantovi Azure Active Directory B2C (Azure AD B2C) můžete vytvořit více uživatelských toků různých typů a podle potřeby je ve svých aplikacích používat. Toky uživatelů se dají znovu použít napříč aplikacemi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Vytvoření toku uživatele upravujícího profil
> * Vytvoření toku uživatele pro resetování hesla

V tomto kurzu se dozvíte, jak vytvořit několik doporučených uživatelských toků pomocí Azure Portal. Pokud hledáte informace o tom, jak ve své aplikaci nastavit tok přihlašovacích údajů vlastníka prostředku (ROPC), přečtěte si téma [Konfigurace toku přihlašovacích údajů pro heslo vlastníka prostředku v Azure AD B2C](configure-ropc.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Zaregistrujte své aplikace](tutorial-register-applications.md) , které jsou součástí uživatelských toků, které chcete vytvořit.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření uživatelského toku pro registraci a přihlašování

Tok uživatelů registrace a přihlašování zpracovává jak registraci, tak i přihlašovací prostředí s jedinou konfigurací. Uživatelé vaší aplikace jsou ve správném umístění v závislosti na kontextu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

    ![B2C, okno klienta, adresáře a předplatného, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady**vyberte **toky uživatelů (zásady)** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na kartě **Doporučené** vyberte tok **registrace a přihlášení** uživatele.

    ![Výběr stránky toku uživatelů pomocí registrace a zvýrazněného toku pro přihlášení](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V případě **zprostředkovatelů identity**vyberte **registrace e-mailu**.

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. V případě **atributů a deklarací uživatelů**vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více**a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno**a **poštovní směrovací číslo**. Klikněte na tlačítko **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace**vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. **Adresa URL odpovědi** by měla ukazovat `https://jwt.ms`.
1. Klikněte na **Spustit tok uživatele**a pak vyberte **zaregistrovat se hned**.

    ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zadejte platnou e-mailovou adresu, klikněte na **Odeslat ověřovací kód**, zadejte ověřovací kód, který jste obdrželi, a pak vyberte **ověřit kód**.
1. Zadejte nové heslo a potvrďte ho.
1. Vyberte zemi a oblast, zadejte název, který chcete zobrazit, zadejte poštovní kód a potom klikněte na **vytvořit**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
1. Nyní můžete tok uživatele spustit znovu a měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace identity, které jste vybrali v poli Země/oblast, jméno a PSČ.

## <a name="create-a-profile-editing-user-flow"></a>Vytvoření toku uživatele upravujícího profil

Pokud chcete uživatelům umožnit úpravy svého profilu v aplikaci, použijte uživatelský tok upravující profil.

1. V nabídce na stránce Přehled klienta Azure AD B2C vyberte **toky uživatelů (zásady)** a pak vyberte **Nový tok uživatele**.
1. Na kartě **Doporučené** vyberte tok uživatele **upravující profilování** .
1. Zadejte **název** toku uživatele. Například *profileediting1*.
1. V případě **zprostředkovatelů identity**vyberte **přihlášení k místnímu účtu**.
1. V případě **atributů uživatele**vyberte atributy, které má zákazník ve svém profilu upravovat. Vyberte například **Zobrazit více**a potom zvolte atributy i deklarace identity pro **zobrazované jméno** a **název úlohy**. Klikněte na tlačítko **OK**.
1. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace**vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. **Adresa URL odpovědi** by měla ukazovat `https://jwt.ms`.
1. Klikněte na **Spustit tok uživatele**a pak se přihlaste pomocí účtu, který jste vytvořili dříve.
1. Teď máte příležitost změnit zobrazované jméno a název úlohy pro uživatele. Klikněte na **Pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

## <a name="create-a-password-reset-user-flow"></a>Vytvoření toku uživatele pro resetování hesla

Pokud chcete uživatelům vaší aplikace povolit resetování hesla, použijte uživatelský tok pro resetování hesla.

1. V nabídce přehled Azure AD B2C tenanta vyberte **toky uživatelů (zásady)** a pak vyberte **Nový tok uživatele**.
1. Na kartě **Doporučené** vyberte tok uživatele **resetování hesla** .
1. Zadejte **název** toku uživatele. Například *passwordreset1*.
1. U **zprostředkovatelů identity**povolte možnost **resetovat heslo pomocí e-mailové adresy**.
1. V části deklarace identity aplikace klikněte na **Zobrazit další** a vyberte deklarace identity, které chcete vrátit do vaší aplikace, do své aplikace. Vyberte například **ID objektu uživatele**.
1. Klikněte na tlačítko **OK**.
1. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace**vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. **Adresa URL odpovědi** by měla ukazovat `https://jwt.ms`.
1. Klikněte na **Spustit tok uživatele**, ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a vyberte **pokračovat**.
1. Teď máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Vytvoření toku uživatele upravujícího profil
> * Vytvoření toku uživatele pro resetování hesla

V dalším kroku se dozvíte, jak přidat zprostředkovatele identity k vašim aplikacím a povolit tak přihlašování uživatelů pomocí poskytovatelů, jako je Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft nebo Twitter.

> [!div class="nextstepaction"]
> [Přidejte do svých aplikací zprostředkovatele identity >](tutorial-add-identity-providers.md)