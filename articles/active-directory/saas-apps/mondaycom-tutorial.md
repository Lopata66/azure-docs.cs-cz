---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s monday.com | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c0353bdcce6bb4917d13de9b8f254ee77de1a4c
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s monday.com

V tomto kurzu se dozvíte, jak integrovat monday.com s Azure Active Directory (Azure AD). Když integrujete monday.com s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k monday.com.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k monday.com svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* monday.com odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* monday.com podporuje jednotné přihlašování (SSO) **a IDP** .
* monday.com podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-mondaycom-from-the-gallery"></a>Přidání monday.com z Galerie

Pokud chcete nakonfigurovat integraci monday.com do služby Azure AD, musíte přidat monday.com z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Monday.com** .
1. Na panelu výsledků vyberte **Monday.com** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>Konfigurace a testování jednotného přihlašování Azure AD pro monday.com

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí monday.com pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v monday.com.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí monday.com, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Monday.com SSO](#configure-mondaycom-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Monday.com test User](#create-mondaycom-test-user)** -to, abyste měli protějšek B. Simon v Monday.com, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Monday.com** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud v **základním oddílu konfigurace SAML** máte **soubor metadat poskytovatele služeb** a chcete ho nakonfigurovat v režimu iniciované **IDP** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** pro **identifikátor** a odpověď získají automaticky v základní části Konfigurace SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud se hodnoty **adresy URL** **identifikátoru** a odpovědi nevrátí automaticky, zadejte hodnoty ručně. **Identifikátor** a **Adresa URL odpovědi** jsou stejné a hodnota je v následujícím vzoru:`https://<your-domain>.monday.com/saml/saml_callback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Monday.com](https://monday.com/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. monday.com aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace monday.com několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut |
    |--|--|
    | E-mailu | uživatel. pošta |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení Monday.com** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k monday.com.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Monday.com**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-mondaycom-sso"></a>Konfigurace jednotného přihlašování monday.com

1. Pokud chcete automatizovat konfiguraci v rámci monday.com, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavení Monday.com** , které vás přesměruje na aplikaci Monday.com. Odtud zadejte přihlašovací údaje správce, které se přihlásí k monday.com. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit monday.com ručně, otevřete nové okno webového prohlížeče a přihlaste se k monday.com jako správce a proveďte následující kroky:

1. Přejděte do **profilu** v pravém horním rohu stránky a klikněte na **správce**.

    ![Konfigurace monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Vyberte **zabezpečení** a ujistěte se, že kliknete na **otevřít** vedle SAML.

    ![Konfigurace monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Vyplňte následující podrobnosti z IDP.

    ![Konfigurace monday.com](./media/mondaycom-tutorial/configuration03.png)

    > [!NOTE]
    > Další podrobnosti najdete v [tomto](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) článku.

### <a name="create-mondaycom-test-user"></a>Vytvořit testovacího uživatele monday.com

V této části se v monday.com vytvoří uživatel s názvem B. Simon. monday.com podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v monday.com neexistuje, vytvoří se nový, když se pokusíte o přístup k monday.com.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici monday.com, měli byste se automaticky přihlásit k monday.com, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si monday.com s Azure AD](https://aad.portal.azure.com/)
