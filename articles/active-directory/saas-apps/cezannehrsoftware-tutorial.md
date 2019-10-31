---
title: 'Kurz: Azure Active Directory integraci se softwarem Cezanne HR | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a software Cezanne HR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158695"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Azure Active Directory integraci se softwarem Cezanne HR

V tomto kurzu se naučíte integrovat software Cezanne HR s Azure Active Directory (Azure AD).
Integrace softwaru Cezanne HR s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Cezanne softwaru pro správu lidských zdrojů.
* Uživatelům můžete povolit, aby se automaticky přihlásili k softwaru Cezanne HR (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se softwarem Cezanne HR budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Cezanne HR software pro jednotné přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Software Cezannee pro **personál podporuje jednotné** přihlašování.

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání softwaru Cezanne HR z Galerie

Pokud chcete nakonfigurovat integraci softwaru Cezanne HR do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat software Cezanne HR z galerie.

**Pokud chcete přidat software Cezanne HR z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **CEZANNE HR software**, vyberte **Cezanne HR software** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Cezanne software HR v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Cezanne HR založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v softwaru Cezanne HR.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí softwaru Cezannee HR, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro CEZANNE HR software](#configure-cezanne-hr-software-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si uživatele Cezannee software Tester](#create-cezanne-hr-software-test-user)** , abyste měli protějšek Britta Simon v softwaru Cezanne HR, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru Cezannee HR, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **softwarových aplikací Cezanne HR** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování Cezanne HR software a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. K získání těchto hodnot se obraťte na [tým podpory CEZANNE HR software Client](https://cezannehr.com/services/support/) .

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit software Cezannee HR** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurace jednotného přihlašování pro Cezanne HR software

1. V jiném okně webového prohlížeče se přihlaste k vašemu tenantovi softwaru Cezanne HR jako správce.

2. V levém navigačním podokně klikněte na **nastavení systému**. Přejít na **nastavení zabezpečení** Pak přejděte do **Konfigurace jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. V okně **Povolte uživatelům přihlášení pomocí následujícího panelu služby jednotného přihlašování (SSO)** zaškrtněte políčko **SAML 2,0** a vyberte možnost **Pokročilá konfigurace** .

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klikněte na tlačítko **Přidat nové** .

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. V části **Zprostředkovatelé identit SAML 2,0** proveďte následující kroky.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Jako **Zobrazovaný název**zadejte název poskytovatele identity.

    b. Do textového pole **identifikátor entity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    c. Změňte **vazbu SAML** na post.

    d. Do textového pole **koncového bodu služby tokenu zabezpečení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole název atributu ID uživatele zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Kliknutím na **nahrát** ikonu nahrajte stažený certifikát z Azure Portal.

    g. Klikněte na tlačítko **OK**.

6. Klikněte na tlačítko **Uložit** .

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k softwaru Cezanne HR.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **software Cezanne HR**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cezanne software HR**.

    ![Odkaz na software Cezanne HR v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-cezanne-hr-software-test-user"></a>Vytvořit uživatele Cezanne HR software test

Aby se uživatelé Azure AD mohli přihlásit k Cezanne softwaru pro personál, musí se zřídit v softwaru Cezanne HR. V případě softwaru Cezanne HR je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Cezanne HR software společnosti jako správce.

2. V levém navigačním podokně klikněte na **nastavení systému**. Přejít na **Správa uživatelů** Pak přejděte k **Přidat nového uživatele**.

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nový uživatel")

3. V části **Podrobnosti o osobě** proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nový uživatel")

    a. Nastavte **interního uživatele** jako vypnutý.

    b. Do textového pole **jméno v prvním** poli zadejte jméno uživatele jako **Britta**.  

    c. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    d. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele, jako Brittasimon@contoso.com.

4. V části **informace o účtu** proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nový uživatel")

    a. Do textového pole **uživatelské jméno** zadejte e-mail uživatele, jako Brittasimon@contoso.com.

    b. Do textového pole **heslo** zadejte heslo uživatele.

    c. Jako **roli zabezpečení**vyberte **HR Professional** .

    d. Klikněte na **OK**.

5. Přejděte na kartu **jednotné přihlašování** a v oblasti **identifikátory SAML 2,0** vyberte **Přidat nový** .

    ![Uživatelský](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Uživatel")

6. Vyberte poskytovatele identity pro **poskytovatele identity** a v textovém poli **identifikátoru uživatele**zadejte e-mailovou adresu účtu Britta Simon.

    ![Uživatelský](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Uživatel")

7. Klikněte na tlačítko **Uložit** .

    ![Uživatelský](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Uživatel")

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici software Cezanne HR na přístupovém panelu, měli byste se automaticky přihlásit k softwaru Cezanne HR, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
