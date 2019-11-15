---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Boomi | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1044a78d9bf1b52a7b0dd9e47d7db6d98b45299c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081963"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Boomi

V tomto kurzu se dozvíte, jak integrovat Boomi s Azure Active Directory (Azure AD). Když integrujete Boomi s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Boomi.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Boomi svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Boomi odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Boomi podporuje jednotné přihlašování **IDP** .

## <a name="adding-boomi-from-the-gallery"></a>Přidání Boomi z Galerie

Pokud chcete nakonfigurovat integraci Boomi do služby Azure AD, musíte přidat Boomi z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Boomi** .
1. Na panelu výsledků vyberte **Boomi** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Boomi

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Boomi pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Boomi.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Boomi, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte BOOMI SSO](#configure-boomi-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Boomi Test User](#create-boomi-test-user)** -to, abyste měli protějšek B. Simon v Boomi, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Boomi** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://platform.boomi.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Boomi](https://boomi.com/company/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Boomi aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Boomi několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název |  Zdrojový atribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Boomi** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Boomi.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Boomi**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-boomi-sso"></a>Konfigurace jednotného přihlašování Boomi

1. V jiném okně webového prohlížeče se přihlaste k webu Boomi společnosti jako správce.

1. Přejděte na **název společnosti** a přejděte na **Nastavení**.

1. Klikněte na kartu **Možnosti jednotného přihlašování** a proveďte následující kroky.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zaškrtněte políčko **Povolit jednotné přihlašování SAML** .

    b. Kliknutím na **importovat** nahrajte stažený certifikát z Azure AD do **certifikátu poskytovatele identity**.

    c. V textovém poli **Adresa URL pro přihlášení zprostředkovatele identity** zadejte hodnotu **přihlašovací adresa URL** z okna konfigurace aplikace služby Azure AD.

    d. Jako **umístění ID federace**vyberte **id federace v FEDERATION_ID** přepínací tlačítko elementu atributu.

    e. Klikněte na tlačítko **Uložit** tlačítko.

### <a name="create-boomi-test-user"></a>Vytvořit testovacího uživatele Boomi

Aby se uživatelé Azure AD mohli přihlašovat k Boomi, musí se zřídit v Boomi. V případě Boomi je zřizování ručním úkolem.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu Boomi společnosti jako správce.

1. Po přihlášení přejděte na **Správa uživatelů** a přejděte na **Uživatelé**.

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_001.png "Uživatelé")

1. Klikněte na ikonu **+** a otevře se dialogové okno **Přidat/udržovat role uživatele** .

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_002.png "Uživatelé")

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_003.png "Uživatelé")

    a. Do textového pole **e-mailová adresa uživatele** zadejte e-mailové adresy uživatele, jako B.Simon@contoso.com.

    b. Do textového pole **název** zadejte jméno uživatele jako B.

    c. Do textového pole **příjmení** zadejte jméno uživatele jako Simon.

    d. Zadejte **ID federace**uživatele. Každý uživatel musí mít ID federace, které jedinečně identifikuje uživatele v rámci účtu.

    e. Přiřaďte uživateli **standardní roli uživatele** . Nepřiřazujte roli správce, protože by jim poskytoval normální přístup do atmosféry i přístup s jednotným přihlašováním.

    f. Klikněte na **OK**.

    > [!NOTE]
    > Uživatel neobdrží uvítací oznamovací e-mail obsahující heslo, které se dá použít k přihlášení k účtu AtomSphere, protože jeho heslo je spravované prostřednictvím poskytovatele identity. K zřizování uživatelských účtů AAD můžete použít jiné nástroje pro vytváření uživatelských účtů Boomi nebo rozhraní API poskytovaná Boomi.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Boomi, měli byste se automaticky přihlásit k Boomi, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Boomi s Azure AD](https://aad.portal.azure.com/)