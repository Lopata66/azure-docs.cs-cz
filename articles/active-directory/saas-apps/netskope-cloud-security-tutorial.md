---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s konzolou správce Netskope | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konzolou pro správu Netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c38900d4ded3d2ee08245674bda90d96226c1eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80396558"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s konzolou správce Netskope

V tomto kurzu se dozvíte, jak integrovat konzolu pro správu Netskope s Azure Active Directory (Azure AD). Když integrujete konzolu pro správu Netskope s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke konzole správce Netskope.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Netskope konzole pro správu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Netskope správce konzoly pro správu jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konzola správce Netskope podporuje jednotné přihlašování (SSO) **IDP** .

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Přidání konzoly správce Netskope z Galerie

Pokud chcete nakonfigurovat integraci konzoly pro správu Netskope do služby Azure AD, musíte přidat konzolu správce Netskope z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Netskope konzolu pro správu** .
1. Z panelu výsledků vyberte **Netskope konzolu pro správu** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Konfigurace a testování jednotného přihlašování Azure AD pro konzolu pro správu Netskope

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí konzoly pro správu Netskope s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v konzole pro správu Netskope vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konzoly pro správu Netskope, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování konzoly pro správu Netskope](#configure-netskope-administrator-console-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte testovacího uživatele v konzole pro správu Netskope](#create-netskope-administrator-console-test-user)** , abyste měli protějšek B. Simon v konzole pro správu Netskope, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **konzolových aplikací Správce Netskope** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`<OrgKey>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Tyto hodnoty se zobrazí později v tomto kurzu.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<tenantname>.goskope.com`

    > [!NOTE]
    > Hodnoty přihlašovacích adres URL nejsou reálné. Aktualizujte hodnotu URL pro přihlášení pomocí skutečné přihlašovací adresy URL. Pokud chcete získat hodnotu URL pro přihlášení, kontaktujte [tým podpory Netskope správce konzoly pro správu](mailto:support@netskope.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace konzoly správce Netskope očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace konzoly správce Netskope, že se v odpovědi SAML vrátí další atributy, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název |  Zdrojový atribut|
    | ---------| --------- |
    | role správce | User. assignedroles |

    > [!NOTE]
    > Kliknutím [sem](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) zobrazíte informace o tom, jak ve službě Azure AD vytvářet role.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení konzoly pro správu Netskope** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke konzole pro správu Netskope.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **konzolu pro správu Netskope**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurace jednotného přihlašování konzoly pro správu Netskope

1. V prohlížeči otevřete novou kartu a přihlaste se k webu konzoly správce Netskope jako správce.

1. V levém navigačním podokně klikněte na kartu **Nastavení** .

    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klikněte na kartu **Správa** .

    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klikněte na kartu **jednotné přihlašování** .

    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-sso.png)

1. V části **nastavení sítě** proveďte následující kroky:
    
    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Zkopírujte hodnotu **adresy URL služby assertion Consumer Service** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu **ID entity poskytovatele služby** a vložte ji do textového pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal.

1. V části **nastavení jednotného přihlašování/slo** klikněte na **Upravit nastavení** .

    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. V místním okně **Nastavení** proveďte následující kroky:

    ![Konfigurace konzoly správce Netskope](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Vyberte možnost **Povolit jednotné přihlašování**.

    b. Do textového pole **Adresa URL IDP** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený certifikát kódovaný v kódování Base64 v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **IDP certifikát** .

    e. Vyberte možnost **Povolit jednotné přihlašování**.

    f. Do textového pole **Adresa URL IDP slo** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    g. Klikněte na **Odeslat**.

### <a name="create-netskope-administrator-console-test-user"></a>Vytvořit testovacího uživatele konzoly pro správu Netskope

1. V prohlížeči otevřete novou kartu a přihlaste se k webu konzoly správce Netskope jako správce.

1. V levém navigačním podokně klikněte na kartu **Nastavení** .

    ![Vytvoření uživatele konzoly správce Netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klikněte na kartu **Aktivní platforma** .

    ![Vytvoření uživatele konzoly správce Netskope](./media/netskope-cloud-security-tutorial/user1.png)

1. Klikněte na kartu **Uživatelé** .

    ![Vytvoření uživatele konzoly správce Netskope](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klikněte na **Přidat uživatele**.

    ![Vytvoření uživatele konzoly správce Netskope](./media/netskope-cloud-security-tutorial/user-add.png)

1. Zadejte e-mailovou adresu uživatele, kterého chcete přidat, a klikněte na **Přidat**.

    ![Vytvoření uživatele konzoly správce Netskope](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici konzoly pro správu Netskope na přístupovém panelu byste měli být automaticky přihlášení do konzoly pro správu Netskope, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si konzolu pro správu Netskope s Azure AD](https://aad.portal.azure.com/)
