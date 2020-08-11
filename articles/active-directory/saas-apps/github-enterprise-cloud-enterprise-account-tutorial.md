---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s účtem GitHub Enterprise Cloud-Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GitHub Enterprise cloudu – podnikový účet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 544b1e8c-f633-4d9b-92fe-077d4c51885a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c29de2d8150bce590d19f3af474dda87d17a47
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88084414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí účtu GitHub Enterprise Cloud – Enterprise

V tomto kurzu se dozvíte, jak integrovat účet GitHub Enterprise Cloud-Enterprise s Azure Active Directory (Azure AD). Když integrujete účet GitHub Enterprise Cloud – Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k účtu GitHub Enterprise Cloud – Enterprise.
* Umožněte uživatelům, aby se automaticky přihlásili k účtu GitHub Enterprise Cloud – Enterprise pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* GitHub Enterprise Cloud – podnikový účet s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* GitHub Enterprise Cloud – účet Enterprise podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.
* GitHub Enterprise Cloud – účet Enterprise podporuje **při zřizování uživatelem jenom čas**
* Jakmile nakonfigurujete účet GitHub Enterprise Cloud – Enterprise, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Přidání účtu GitHub Enterprise Cloud – Enterprise z Galerie

Pokud chcete nakonfigurovat integraci účtu GitHub Enterprise Cloud-Enterprise do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat účet GitHub Enterprise Cloud-Enterprise z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **GitHub Enterprise Cloud-Enterprise Account** .
1. Vyberte **GitHub Enterprise Cloud-Enterprise účet** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurace a testování jednotného přihlašování Azure AD pro GitHub Enterprise Cloud – podnikový účet

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí účtu GitHub Enterprise Cloud – Enterprise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci účtu GitHub Enterprise Cloud-Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí účtu GitHub Enterprise Cloud-Enterprise, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování pro GitHub Enterprise Cloud – podnikový účet](#configure-github-enterprise-cloud-enterprise-account-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvoření webu GitHub Enterprise Cloud – podnikový účet testu uživatele](#create-github-enterprise-cloud-enterprise-account-test-user)** – Pokud chcete mít protějšek B. Simon v GitHub Enterprise Cloud-Enterprise, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **GitHub Enterprise Cloud-Enterprise** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

     Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a identifikátoru. Kontaktujte [GitHub Enterprise Cloud – tým podpory klientů podnikového účtu](mailto:support@github.com) pro získání těchto hodnot. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateBase64.png)

1. V části **Nastavení účtu GitHub Enterprise Cloud-Enterprise** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k účtu GitHub Enterprise Cloud-Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **GitHub Enterprise Cloud – podnikový účet**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Konfigurace GitHubu Enterprise cloudu – podnikový účet jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně **účtu GitHub Enterprise Cloud-Enterprise** , je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do webu [GitHub Enterprise Cloud Enterprise Support Team](mailto:support@github.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Vytvoření cloudového cloudu GitHub – podnikový účet testu uživatele

V této části se v rámci účtu GitHub Enterprise Cloud-Enterprise vytvoří uživatel s názvem B. Simon. GitHub Enterprise Cloud – účet Enterprise podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v rámci účtu GitHub Enterprise Cloud-Enterprise, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici účet GitHub Enterprise Cloud-Enterprise, měli byste být automaticky přihlášeni k účtu GitHub Enterprise Cloud-Enterprise, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušení webu GitHub Enterprise Cloud – podnikový účet s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit účet GitHub Enterprise Cloud-Enterprise s pokročilou viditelností a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)