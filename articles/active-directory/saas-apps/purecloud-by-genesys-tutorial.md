---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PureCloud podle Genesys | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PureCloud pomocí Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0433b7287fc00da09fcf303fbcb8d74bf4e3b196
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85800107"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PureCloud pomocí Genesys

V tomto kurzu se dozvíte, jak integrovat PureCloud pomocí Genesys s Azure Active Directory (Azure AD). Až to uděláte, můžete:

* Pomocí Azure AD můžete řídit, kteří uživatelé budou mít přístup k PureCloud pomocí Genesys.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k PureCloud prostřednictvím Genesys s účty Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ho nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* PureCloud pomocí předplatného jednotného přihlašování Genesys (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PureCloud by Genesys podporovaly **SP a IDP**– bylo zahájeno jednotné přihlašování.

> [!NOTE]
> Vzhledem k tomu, že ID této aplikace je hodnota s pevným řetězcem, může být v jednom klientovi nakonfigurovaná jenom jedna instance.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Přidání PureCloud pomocí Genesys z Galerie

Pokud chcete nakonfigurovat integraci PureCloud by Genesys do služby Azure AD, musíte přidat PureCloud od galerie Genesys z Galerie do svého seznamu spravovaných aplikací SaaS. Postupujte přitom takto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PureCloud by Genesys** .
1. Z panelu výsledků vyberte **PureCloud by Genesys** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Konfigurace a testování jednotného přihlašování Azure AD pro PureCloud pomocí Genesys

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí PureCloud pomocí Genesys pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PureCloud pomocí Genesys.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí PureCloud od Genesys, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Nakonfigurujte PureCloud pomocí jednotného přihlašování Genesys](#configure-purecloud-by-genesys-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte PureCloud pomocí Genesys testovacího uživatele](#create-purecloud-by-genesys-test-user)** , aby měl protějšek B. Simon v PureCloud by Genesys, který je propojený s reprezentací uživatele Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, postupujte následovně:

1. V [Azure Portal](https://portal.azure.com/)na stránce **PureCloud podle Genesys** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP**, zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do pole **identifikátor** zadejte adresu URL, která odpovídá vaší oblasti:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která odpovídá vaší oblasti:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do pole **přihlašovací adresa URL** zadejte adresu URL, která odpovídá vaší oblasti:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. PureCloud by aplikace Genesys očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů:

    ![image](common/default-attributes.png)

1. Kromě toho PureCloud by aplikace Genesys očekává, že se v odpovědi SAML vrátí několik dalších atributů, jak je znázorněno v následující tabulce. Tyto atributy jsou také předem vyplněné, ale můžete je podle potřeby zkontrolovat.

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | E-mail | User. userPrincipalName |
    | OrganizationName | `Your organization name` |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení PureCloud podle Genesys** zkopírujte příslušnou adresu URL (nebo adresy URL) na základě vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon v Azure Portal:

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte uživatelské jméno v následujícím formátu: username@companydomain.extension . Například: `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části nastavíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k PureCloud pomocí Genesys.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **PureCloud podle Genesys**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurace PureCloud pomocí jednotného přihlašování Genesys

1. V jiném okně webového prohlížeče se přihlaste k PureCloud pomocí Genesys jako správce.

1. V horní části vyberte **správce** a potom v části **integrace**klikněte na **jednotné přihlašování** .

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Přepněte na kartu **ADFS/Azure AD (Premium)** a pak postupujte podle těchto kroků:

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Vyberte **Procházet** a nahrajte certifikát s kódováním base-64, který jste stáhli z Azure Portal do **certifikátu ADFS**.

    b. Do pole **identifikátor URI vystavitele ADFS** vložte hodnotu **identifikátoru Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do pole **cílový identifikátor URI** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Pro hodnotu **identifikátoru předávající strany** klikněte na Azure Portal a pak na stránce Application Integration **PureCloud podle Genesys** vyberte kartu **vlastnosti** a zkopírujte hodnotu **ID aplikace** . Vložte ho do pole **identifikátor předávající strany** .

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Vyberte **Uložit**.

### <a name="create-purecloud-by-genesys-test-user"></a>Vytvořit PureCloud podle Genesys testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k PureCloud prostřednictvím Genesys, musí se zřídit v PureCloud pomocí Genesys. V PureCloud podle Genesys je zřizování ručním úkolem.

**Pokud chcete zřídit uživatelský účet, postupujte podle těchto kroků:**

1. Přihlaste se k PureCloud pomocí Genesys jako správce.

1. V horní části vyberte **správce** a v části **lidé & oprávnění**přejít na **lidi** .

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na stránce **lidé** vyberte **Přidat osobu**.

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure04.png)

1. V dialogovém okně **Přidat lidi do organizace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Do pole **jméno a příjmení zadejte** jméno uživatele. Například: **B. Simon**.

    b. Do pole **e-mail** zadejte e-mail uživatele. Například: **b. simon \@ contoso.com**.

    c. Vyberte **Vytvořit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici **PureCloud by Genesys** , měli byste se k PureCloud automaticky přihlásit pomocí účtu Genesys, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí služby Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si PureCloud pomocí Genesys se službou Azure AD](https://aad.portal.azure.com/)
