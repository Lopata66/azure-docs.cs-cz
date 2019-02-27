---
title: 'Kurz: Integrace Azure Active Directory s Halogen softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Halogen softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfcf637810a3cbbc70216af39918847e703ca32f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869730"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Kurz: Integrace Azure Active Directory s Halogen softwaru

V tomto kurzu se dozvíte, jak integrovat Halogen Software s Azure Active Directory (Azure AD).
Integrace softwaru Halogen s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k softwaru Halogen.
* Uživatelům se automaticky přihlášeni k softwaru Halogen (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Halogen softwaru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Halogen softwaru jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Halogen Software podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-halogen-software-from-the-gallery"></a>Přidání Halogen softwaru z Galerie

Konfigurace integrace Halogen softwaru do služby Azure AD, budete muset přidat Halogen Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Halogen softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Halogen softwaru**vyberte **Halogen softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Software halogen v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Halogen Software založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Halogen softwaru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Halogen softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Halogen softwaru Single Sign-On](#configure-halogen-software-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele softwaru Halogen](#create-halogen-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Halogen Software, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Halogen softwaru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Halogen softwaru** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Halogen softwaru domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://global.hgncloud.com/<companyname>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Halogen softwarového klienta](https://support.halogensoftware.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení softwaru Halogen** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-halogen-software-single-sign-on"></a>Konfigurace Halogen softwaru jednotného přihlašování

1. V jiném okně prohlížeče, přihlašování k vaší **Halogen softwaru** aplikaci jako správce.

2. Klikněte na tlačítko **možnosti** kartu.
  
    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. V levém navigačním podokně klikněte na tlačítko **konfigurace SAML**.
  
    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. Na **konfigurace SAML** stránce, proveďte následující kroky:

    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. Jako **jedinečný identifikátor**vyberte **NameID**.

    b. Jako **mapy jedinečný identifikátor pro**vyberte **uživatelské jméno**.
  
    c. Nahrát soubor stažený metadat, klikněte na tlačítko **Procházet** a vyberte soubor a potom **nahrát soubor**.

    d. Chcete-li otestovat konfiguraci, klikněte na tlačítko **spustit Test**.

    > [!NOTE]
    > Budete muset čekat na zprávu "*SAML test je dokončen. Zavřete prosím toto okno*". Zavřete okno otevřené. **Povolit SAML** zaškrtávací políčko je povoleno pouze v případě test byl dokončen.

    e. Vyberte **povolit SAML**.

    f. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k softwaru Halogen.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Halogen softwaru**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Halogen softwaru**.

    ![Odkaz Halogen Software v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-halogen-software-test-user"></a>Vytvořit Halogen Software testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon Halogen softwaru.

**Vytvořte uživatele v softwaru Halogen jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k vaší **Halogen softwaru** aplikaci jako správce.

2. Klikněte na tlačítko **uživatele Center** kartu a potom klikněte na tlačítko **Create User**.

    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. Na **nového uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Co je služba Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

    c. V **uživatelské jméno** textové pole, typ **Britta Simon**, uživatelské jméno jako na webu Azure portal.

    d. V **heslo** textového pole zadejte heslo pro Britta.

    e. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Halogen softwaru na přístupovém panelu, vám by měl být automaticky přihlášeni Halogen software, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)