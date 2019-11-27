---
title: 'Kurz: Integrace Azure Active Directory s využitím bonusu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a bonusem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232061"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Kurz: Integrace Azure Active Directory s využitím bonusu

V tomto kurzu se naučíte, jak pomocí Azure Active Directory (Azure AD) integrovat bonus.
Služba Azure AD integruje bonus s následujícími výhodami:

* Můžete kontrolovat v Azure AD, kteří mají přístup za bonus.
* Uživatelům můžete povolit, aby se automaticky přihlásili, aby se k nim mohli přihlašovat pomocí účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím bonusu potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Bonusové předplatné s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bonusně podporuje jednotné přihlašování (SSO) iniciované **IDP**

## <a name="adding-bonusly-from-the-gallery"></a>Přidávání bonusů z Galerie

Pokud chcete nastavovat integraci Bonusně do Azure AD, je potřeba přidat bonus z Galerie do vašeho seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat bonus z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **bonus**, z panelu výsledků vyberte **bonus** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Bonusně v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s bonusem na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkazový vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v bonusovém prostředí.

Pokud chcete jednotné přihlašování pomocí služby Azure AD nakonfigurovat a testovat s bonusem, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednorázově jednotné přihlašování](#configure-bonusly-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte bonusně testovacího uživatele](#create-bonusly-test-user)** – abyste měli protiBrittaho Simonu, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete jednotné přihlašování pomocí služby Azure AD nakonfigurovat Bonusně, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **bonusová** integrace aplikací vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Vybonusit informace o jednotném přihlašování v doméně a adresách URL](common/idp-reply.png)

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Chcete-li získat hodnotu, obraťte se na [tým podpory pro mimořádné klienty](https://bonus.ly/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **nastavit bonus** dle potřeby zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-bonusly-single-sign-on"></a>Konfigurace bonusového jednotného přihlašování

1. V jiném okně prohlížeče se přihlaste k vašemu **bonusovém** tenantovi.

1. Na panelu nástrojů v horní části klikněte na **Nastavení** a pak vyberte **integrace a aplikace**.

    ![Bonusová část pro sociální sítě](./media/bonus-tutorial/ic773686.png "Bonusly")
1. V části **jednotné přihlašování**vyberte **SAML**.

1. Na stránce dialogu **SAML** proveďte následující kroky:

    ![Vybonus – stránka dialogového okna SAML](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Do textového pole **Adresa URL cíle jednotného přihlašování IDP** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení IDP** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **vystavitele IDP** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.
    
    d. Vložte hodnotu **kryptografického otisku** zkopírovanou z Azure Portal do textového pole **otisku certifikátu** .
    
    e. Klikněte na možnost **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k bonusům.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **bonus**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **bonusně**.

    ![Bonusový odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-bonusly-test-user"></a>Vytvořit bonusový test uživatele

Aby se uživatelé Azure AD mohli přihlašovat k bonusu, musí se zřídit Bonusně. V případě bonusu je zřizování ručním úkolem.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné mimořádné nástroje pro vytváření uživatelských účtů nebo rozhraní API, které nabízí Bonusně. 

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. V okně webového prohlížeče se přihlaste k vašemu bonusovém tenantovi.

1. Klikněte na **Nastavení**.

    ![Nastavení](./media/bonus-tutorial/ic781041.png "Nastavení")

1. Klikněte na kartu **Uživatelé a bonusy** .

    ![Uživatelé a bonusy](./media/bonus-tutorial/ic781042.png "Uživatelé a bonusy")

1. Klikněte na **Spravovat uživatele**.

    ![Správa uživatelů](./media/bonus-tutorial/ic781043.png "Správa uživatelů")

1. Klikněte na **Přidat uživatele**.

    ![Přidat uživatele](./media/bonus-tutorial/ic781044.png "Přidání uživatele")

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele](./media/bonus-tutorial/ic781045.png "Přidání uživatele")  

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    c. Do textového pole **e-mail** zadejte e-maily uživatele, jako je `brittasimon\@contoso.com`.

    d. Klikněte na možnost **Uložit**.

    > [!NOTE]
    > Držitel účtu Azure AD obdrží e-mail s odkazem na potvrzení účtu předtím, než se aktivuje.  

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici bonus na přístupovém panelu byste se měli automaticky přihlášeni k bonusu, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
