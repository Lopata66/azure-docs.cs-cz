---
title: 'Kurz: Azure Active Directory integrace se zálohováním koncového bodu Carbonite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zálohováním koncového bodu Carbonite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f77f00870af7b9d1788d96391264bdf9b4ee73c
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85607663"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Kurz: integrace zálohování koncového bodu Carbonite s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat zálohování koncového bodu Carbonite pomocí Azure Active Directory (Azure AD). Při integraci zálohování koncového bodu Carbonite s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k zálohování koncového bodu Carbonite.
* Umožněte uživatelům, aby se k zálohování koncových bodů Carbonite pomocí svých účtů Azure AD automaticky přihlásili.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné služby Carbonite Endpoint Backup pro jednotné přihlašování (SSO) je povolené.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zálohování koncového bodu Carbonite podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Přidání zálohování koncového bodu Carbonite z Galerie

Pokud chcete nakonfigurovat integraci zálohování koncového bodu Carbonite do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zálohu Carbonite Endpoint z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Carbonite zálohování koncového bodu** .
1. Na panelu výsledků vyberte **zálohování koncového bodu Carbonite** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí zálohování koncového bodu Carbonite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v zálohování koncového bodu Carbonite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zálohování koncového bodu Carbonite, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Konfigurace jednotného přihlašování koncového bodu Carbonite](#configure-carbonite-endpoint-backup-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele koncového bodu Carbonite koncového bodu](#create-carbonite-endpoint-backup-test-user)** – Chcete-li mít protějšek B. Simon v zálohování koncového bodu Carbonite, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **koncového bodu Carbonite** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte jednu z následujících adres URL:

        ```https
        https://red-us.mysecuredatavault.com
        https://red-apac.mysecuredatavault.com
        https://red-fr.mysecuredatavault.com
        https://red-emea.mysecuredatavault.com
        https://kamino.mysecuredatavault.com
        ```

    b. Do textového pole **Adresa URL odpovědi** zadejte jednu z těchto adres URL:

        ```https
        https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
        https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
        https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
        https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
        ```

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte jednu z těchto adres URL:

    ```https
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení zálohování koncového bodu Carbonite** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurace jednotného přihlašování koncového bodu Carbonite

1. Pokud chcete automatizovat konfiguraci v rámci zálohování koncového bodu Carbonite, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavení zálohování koncového bodu Carbonite** , které vás nasměruje do zálohovací aplikace koncového bodu Carbonite. Odtud zadejte přihlašovací údaje správce, které se přihlásí k zálohování koncového bodu Carbonite. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit zálohování koncového bodu Carbonite ručně, otevřete nové okno webového prohlížeče a přihlaste se k firemnímu webu Carbonite pro zálohování jako správce a proveďte následující kroky:

4. V levém podokně klikněte na **Společnost** .

    ![Konfigurace zálohování koncového bodu Carbonite ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klikněte na **jednotné přihlašování**.

    ![Konfigurace zálohování koncového bodu Carbonite ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klikněte na **Povolit** a pak pro konfiguraci klikněte na **Upravit nastavení** .

    ![Konfigurace zálohování koncového bodu Carbonite ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na stránce nastavení **jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace zálohování koncového bodu Carbonite ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Do textového pole **název zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Klikněte na **zvolit soubor** a nahrajte stažený soubor **certifikátu (Base64)** z Azure Portal.

    1. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k záloze koncových bodů Carbonite.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **zálohování koncového bodu Carbonite**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-carbonite-endpoint-backup-test-user"></a>Vytvořit testovacího uživatele pro zálohování koncového bodu Carbonite

1. V jiném okně webového prohlížeče se přihlaste ke svému webu Carbonite Endpoint Backup jako správce.

1. V levém podokně klikněte na **uživatele** a pak klikněte na **Přidat uživatele**.

    ![Přidat uživatele v zálohování koncového bodu Carbonite](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele v zálohování koncového bodu Carbonite](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Zadejte **e-mail**, **jméno**, příjmení a **jméno** uživatele a poskytněte uživateli požadovaná oprávnění podle požadavků organizace.

    1. Klikněte na **Přidat uživatele**.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici zálohování koncového bodu Carbonite na přístupovém panelu, měli byste se automaticky přihlásit k zálohování koncového bodu Carbonite, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)