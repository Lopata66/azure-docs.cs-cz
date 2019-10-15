---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s EZOfficeInventory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EZOfficeInventory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ebfe5f75d5d8546e0f5e8ad6f8c5d0063e5bda2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EZOfficeInventory

V tomto kurzu se dozvíte, jak integrovat EZOfficeInventory s Azure Active Directory (Azure AD). Když integrujete EZOfficeInventory s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k EZOfficeInventory.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k EZOfficeInventory svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* EZOfficeInventory odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EZOfficeInventory podporuje jednotné přihlašování iniciované v **SP**

* EZOfficeInventory podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Přidání EZOfficeInventory z Galerie

Pokud chcete nakonfigurovat integraci EZOfficeInventory do služby Azure AD, musíte přidat EZOfficeInventory z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **EZOfficeInventory** .
1. Na panelu výsledků vyberte **EZOfficeInventory** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EZOfficeInventory

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EZOfficeInventory pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v EZOfficeInventory.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EZOfficeInventory, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EZOFFICEINVENTORY SSO](#configure-ezofficeinventory-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte EZOfficeInventory Test User](#create-ezofficeinventory-test-user)** -to, abyste měli protějšek B. Simon v EZOfficeInventory, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **EZOfficeInventory** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`.

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta EZOfficeInventory](mailto:support@ezofficeinventory.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. EZOfficeInventory aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace EZOfficeInventory několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) | Zdrojový atribut|
    | ---------------| --------------- |
    | Křestní_jméno | User. křestní jméno |
    | Příjmení | User. příjmení |
    | E-mail | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení EZOfficeInventory** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k EZOfficeInventory.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **EZOfficeInventory**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ezofficeinventory-sso"></a>Konfigurace jednotného přihlašování EZOfficeInventory

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu EZOfficeInventory společnosti jako správce.

2. V pravém horním rohu stránky klikněte na **profil**@no__t – 1and a potom přejděte na **Nastavení** > **Doplňky**.

    ![Konfigurace EZOfficeInventory](./media/ezofficeinventory-tutorial/configure01.png)

3. Přejděte dolů do části **integrace SAML** a proveďte následující kroky:

    ![Konfigurace EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Ověřte možnost **Enabled** .

    b. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete v programu Poznámkový blok certifikát kódovaný jako base64, zkopírujte jeho obsah a vložte ho do textového pole **certifikát poskytovatele identity** .

    d. Do textového pole **Text tlačítka pro přihlášení** zadejte text tlačítka pro přihlášení.

    e. Do textového pole **první jméno** zadejte **křestní_jméno**.

    f. Do textového pole **poslední jméno** zadejte **příjmení**.

    g. Do textového pole **e-mail** zadejte **e-mail**.

    h. Jako **výchozí možnost role EZOfficeInventory** vyberte svou roli podle vašeho požadavku.

    i. Klikněte na **Aktualizovat**.

### <a name="create-ezofficeinventory-test-user"></a>Vytvořit testovacího uživatele EZOfficeInventory

V této části se v EZOfficeInventory vytvoří uživatel s názvem Britta Simon. EZOfficeInventory podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v EZOfficeInventory neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici EZOfficeInventory, měli byste se automaticky přihlásit k EZOfficeInventory, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si EZOfficeInventory s Azure AD](https://aad.portal.azure.com/)

