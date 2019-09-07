---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s NetDocuments | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf25ce318415a8aa36bca8d4cd7380e4e8e67ca
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743462"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s NetDocuments

V tomto kurzu se dozvíte, jak integrovat NetDocuments s Azure Active Directory (Azure AD). Když integrujete NetDocuments s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k NetDocuments.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k NetDocuments svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* NetDocuments odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NetDocuments podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Přidání NetDocuments z Galerie

Pokud chcete nakonfigurovat integraci NetDocuments do služby Azure AD, musíte přidat NetDocuments z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **NetDocuments** .
1. Na panelu výsledků vyberte **NetDocuments** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Konfigurace a testování jednotného přihlašování Azure AD pro NetDocuments

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí NetDocuments pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NetDocuments.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NetDocuments, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte NETDOCUMENTS SSO](#configure-netdocuments-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte NetDocuments Test User](#create-netdocuments-test-user)** -to, abyste měli protějšek B. Simon v NetDocuments, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **NetDocuments** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. ID úložiště je hodnota začínající na **CA** a za 8 kódem znaku, který je přidružený k vašemu úložišti NetDocuments. Další informace najdete v [dokumentu podpory federovaných identit v NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Případně můžete kontaktovat [tým podpory klienta NetDocuments](https://support.netdocuments.com/hc/) a získat tyto hodnoty, pokud máte potíže s konfigurací výše uvedených informací. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. NetDocuments aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Aplikace NetDocuments očekává, že **NameIdentifier** budou mapovány pomocí **ČísloZaměstnance** nebo jakékoli jiné deklarace identity, která je pro vaši organizaci platná jako **NameIdentifier**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** . a změňte mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení NetDocuments** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k NetDocuments.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **NetDocuments**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-netdocuments-sso"></a>Konfigurace jednotného přihlašování NetDocuments

1. V jiném okně webového prohlížeče se přihlaste k webu NetDocuments společnosti jako správce.

2. Přejít na **správce**.

3. Klikněte na **Přidat a odebrat uživatele a skupiny**.
   
    ![Úložiště](./media/netdocuments-tutorial/ic795047.png "Úložiště")

4. Klikněte na **Konfigurovat rozšířené možnosti ověřování**.
    
    ![Konfigurace rozšířených možností ověřování](./media/netdocuments-tutorial/ic795048.png "Konfigurace rozšířených možností ověřování")

5. V dialogu **federované identity** proveďte následující kroky:
   
    ![Federované identity](./media/netdocuments-tutorial/ic795049.png "Federované identity")
   
    a. Jako **Typ serveru federované identity**vyberte **Active Directory Federation Services (AD FS)** .
   
    b. Klikněte na **zvolit soubor**a nahrajte stažený soubor metadat, který jste stáhli z Azure Portal.
   
    c. Klikněte na **OK**.

### <a name="create-netdocuments-test-user"></a>Vytvořit testovacího uživatele NetDocuments

Aby se uživatelé Azure AD mohli přihlašovat k NetDocuments, musí se zřídit v NetDocuments.  
V případě NetDocuments je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **NetDocuments** společnosti jako správce.

2. V nabídce v horní části klikněte na **správce**.
   
    ![Správce](./media/netdocuments-tutorial/ic795051.png "Správce")

3. Klikněte na **Přidat a odebrat uživatele a skupiny**.
   
    ![Úložiště](./media/netdocuments-tutorial/ic795047.png "Úložiště")

4. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu platného Azure Active Directory účtu, který chcete zřídit, a pak klikněte na **Přidat uživatele**.
   
    ![E-mailová adresa](./media/netdocuments-tutorial/ic795053.png "E-mailová adresa")
   
    >[!NOTE]
    >Držitel účtu Azure Active Directory získá e-mail s odkazem na potvrzení účtu, než se aktivuje. K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů NetDocuments nebo rozhraní API poskytovaná NetDocuments.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici NetDocuments, měli byste se automaticky přihlásit k NetDocuments, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si NetDocuments s Azure AD](https://aad.portal.azure.com/)

