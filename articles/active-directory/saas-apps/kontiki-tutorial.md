---
title: 'Kurz: Azure Active Directory integrace s Kontiki | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67098490"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Kurz: Azure Active Directory integrace s Kontiki

V tomto kurzu se dozvíte, jak integrovat Kontiki s Azure Active Directory (Azure AD).

Integrace Kontiki s Azure AD přináší následující výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Kontiki.
* Uživatelé můžou být automaticky přihlášení k Kontiki pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Kontiki potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné Kontiki s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete Kontiki se službou Azure AD.

Kontiki podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Zřizování uživatelů za běhu**

## <a name="add-kontiki-in-the-azure-portal"></a>Přidat Kontiki do Azure Portal

Pokud chcete integrovat Kontiki s Azure AD, musíte přidat Kontiki do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Kontiki**. Ve výsledcích hledání vyberte **Kontiki**a pak vyberte **Přidat**.

    ![Kontiki v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Kontiki na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kontiki.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Kontiki, musíte dokončit tyto stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování Kontiki](#configure-kontiki-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvořit testovacího uživatele v Kontiki](#create-a-kontiki-test-user)** | Vytvoří protějšek Britta Simon v Kontiki, která je propojená se zastoupením uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Kontiki v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikace **Kontiki** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML** nebo **SAML/WS-nakrmený** režim pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednotné přihlašování pomocí SAML** vyberte **Upravit** (ikona tužky) a otevřete **základní podokno konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** v textovém poli **Adresa URL pro přihlášení** zadejte adresu URL, která má následující vzor:`https://<companyname>.mc.eval.kontiki.com`

    ![Informace o jednotném přihlašování v doméně Kontiki a adresách URL](common/sp-signonurl.png)

    > [!NOTE]
    > Pokud chcete získat správnou hodnotu, obraťte se na [tým podpory klienta Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. V podokně **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** vedle **federačních metadat XML**. Vyberte možnost stažení podle vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu XML federačních metadat](common/metadataxml.png)

1. V části **Nastavení Kontiki** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Odhlašovací adresa URL

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurace jednotného přihlašování Kontiki

Ke konfiguraci jednotného přihlašování na straně Kontiki odešlete stažený soubor XML federačních metadat a příslušné adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Tým podpory Kontiki používá informace, které odesíláte, aby se zajistilo, že připojení jednotného přihlašování pomocí protokolu SAML je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon\@\<Your a Company-Domain>.\<>rozšíření **. Například **brittasimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

    ![Podokno uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Kontiki, aby mohli používat jednotné přihlašování Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace** > **všechny aplikace** > **Kontiki**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Kontiki**.

    ![Kontiki v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** . Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-kontiki-test-user"></a>Vytvořit testovacího uživatele v Kontiki

Pro konfiguraci zřizování uživatelů v Kontiki není k dispozici žádná položka akce. Když se přiřazený uživatel pokusí přihlásit k Kontiki pomocí portálu moje aplikace, Kontiki zkontroluje, jestli uživatel existuje. Pokud se nenajde žádný uživatelský účet, Kontiki automaticky vytvoří uživatelský účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Po nastavení jednotného přihlašování se při výběru **Kontiki** na portálu moje aplikace automaticky přihlásíte k Kontiki. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si tyto články:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
