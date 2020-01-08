---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s hostovaným MyCirqa SSO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hostovaným MyCirqa SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4337e343-180d-475d-83ac-6f5b5f133dfe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc6cd36190ca3847fdc9e3f6935aed5c78e0942
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75662336"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s hostovaným MyCirqa SSO

V tomto kurzu se dozvíte, jak integrovat hostované MyCirqa SSO pomocí Azure Active Directory (Azure AD). Když integrujete hostované MyCirqa SSO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k hostovanému jednotnému přihlašování MyCirqa
* Umožněte, aby se vaši uživatelé automaticky přihlásili k hostovanému MyCirqa SSO pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Hostované předplatné MyCirqa SSO jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Hostované MyCirqa SSO podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Přidání hostovaného jednotného přihlašování MyCirqa z Galerie

Pokud chcete nakonfigurovat integraci hostovaného MyCirqa jednotného přihlašování do služby Azure AD, musíte přidat hostované MyCirqa SSO z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **hostované MyCirqa SSO** .
1. Vyberte **hostované MYCIRQA SSO** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD pro hostované MyCirqa jednotné přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí hostovaného MyCirqa jednotného přihlašování pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v hostovaném MyCirqa SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí hostovaného jednotného přihlašování MyCirqa, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte hostované MYCIRQA SSO](#configure-hosted-mycirqa-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte hostovaného testovacího uživatele MYCIRQA jednotného přihlašování](#create-hosted-mycirqa-sso-test-user)** , abyste měli protějšek B. Simon v hostovaném MyCirqa SSO, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce hostované integrace aplikací **jednotného přihlašování MyCirqa** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot kontaktujte [tým podpory hostovaného MYCIRQA jednotného přihlašování](not sure) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k hostovanému MyCirqa SSO.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **hostované MYCIRQA jednotné přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-hosted-mycirqa-sso"></a>Konfigurace hostovaného jednotného přihlašování MyCirqa

Pokud chcete nakonfigurovat jednotné přihlašování na **hostované straně jednotného** přihlašování MyCirqa, musíte odeslat **adresu URL federačních metadat aplikace** do [hostovaného týmu podpory pro jednotné přihlašování MyCirqa](mailto:support@isoxford.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Vytvořit hostovaného testovacího uživatele MyCirqa SSO

V této části vytvoříte uživatele s názvem Britta Simon v hostovaném MyCirqa SSO. Pracujte s [hostovaným týmem podpory MYCIRQA SSO](mailto:support@isoxford.com) a přidejte uživatele na hostované platformě jednotného přihlašování MyCirqa. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici hostované MyCirqa SSO na přístupovém panelu byste se měli automaticky přihlášeni k hostovanému MyCirqa jednotného přihlašování, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si hostované MyCirqa SSO pomocí Azure AD](https://aad.portal.azure.com/)