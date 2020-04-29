---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s NetSuite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760044"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Kurz: Integrace jednotného přihlašování (SSO) Azure AD pomocí NetSuite

V tomto kurzu se dozvíte, jak integrovat NetSuite s Azure Active Directory (Azure AD). Když integrujete NetSuite s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k NetSuite.
* Umožněte uživatelům, aby se automaticky přihlásili k NetSuite pomocí svých účtů Azure AD.
* Spravujte své účty na jednom centrálním místě, Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným NetSuitem jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

NetSuite podporuje:

* Jednotné přihlašování iniciované IDP
* Zřizování uživatele JIT (just-in-time).
* [Automatizované zřizování uživatelů](NetSuite-provisioning-tutorial.md).
* Po nakonfigurování NetSuite můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Vzhledem k tomu, že identifikátor této aplikace je pevná řetězcová hodnota, může být v jednom klientovi nakonfigurovaná jenom jedna instance.

## <a name="add-netsuite-from-the-gallery"></a>Přidání NetSuite z Galerie

Pokud chcete nakonfigurovat integraci NetSuite do služby Azure AD, přidejte NetSuite z Galerie do svého seznamu spravovaných aplikací pro SaaS pomocí následujícího postupu:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **NetSuite** .
1. V podokně výsledků vyberte **NetSuite**a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro NetSuite

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí NetSuite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NetSuite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NetSuite, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    * [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD s uživatelem B. Simon.  
    * [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , abyste povolili uživateli B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování [NETSUITE SSO](#configure-netsuite-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * [Vytvořte testovacího uživatele NetSuite](#create-the-netsuite-test-user) , který bude mít protějšek uživatele B. Simon v NetSuite, který je propojený s Předprezentací Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, udělejte toto:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **NetSuite** vyhledejte část **Správa** a pak vyberte **jednotné přihlašování**.
1. V podokně **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. V podokně **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu **Upravit** (tužka) vedle **základní konfigurace SAML**.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte do textového pole **Adresa URL odpovědi** adresu URL v jednom z následujících formátů:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * V části Konfigurace NetSuite se zobrazí **hodnota, která je vysvětlena dále v kurzu v kroku 8 v části Konfigurace NetSuite. < `Account ID` ** V tomto případě najdete konkrétní doménu (například system.na0.netsuite.com).

        ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Hodnoty v předchozích adresách URL nejsou reálné. Aktualizujte je skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Můžete se také podívat na formáty zobrazené v části **základní konfigurace SAML** v Azure Portal.

1. NetSuite aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace NetSuite několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > Hodnota atributu Account není reálné číslo. Tuto hodnotu aktualizujete, jak je vysvětleno dále v tomto kurzu.

1. Na stránce nastavit jednotné přihlašování pomocí SAML v části podpisový certifikát SAML Najděte XML metadata federace a vyberte stáhnout a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení NetSuite** zkopírujte odpovídající adresu URL nebo adresy URL v závislosti na vašem požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.

1. V horní části obrazovky vyberte **Nový uživatel** .

1. V podokně vlastnosti **uživatele** proveďte následující kroky:

   a. Do pole **název** zadejte **B. Simon**.  
   b. Do pole **uživatelské jméno** zadejte username@companydomain.extension (například B.Simon@contoso.com).  
   c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .  
   d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k NetSuite.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **NetSuite**.
1. V podokně Přehled vyhledejte část **Správa** a potom vyberte odkaz **Uživatelé a skupiny** .

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a potom v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Tlačítko Přidat uživatele](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** v rozevíracím seznamu **Uživatelé** vyberte **B. Simon**a pak vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, udělejte toto:

   a. V podokně **Vybrat roli** vyberte v rozevíracím seznamu příslušnou roli pro uživatele.  
   b. V dolní části obrazovky vyberte tlačítko **Vybrat** .
1. V podokně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-netsuite-sso"></a>Konfigurace jednotného přihlašování NetSuite

1. V prohlížeči otevřete novou kartu a přihlaste se k webu NetSuite společnosti jako správce.

2. V horním navigačním panelu vyberte možnost **Nastavení**a potom vyberte možnost **Company** > **Povolit funkce**společnosti.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na panelu nástrojů uprostřed stránky vyberte možnost **SuiteCloud**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-suitecloud.png)

4. V části **spravovat ověřování**zaškrtněte políčko **jednotné přihlašování SAML** , chcete-li povolit možnost jednotného přihlašování SAML v NetSuite.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-ticksaml.png)

5. V horním navigačním panelu vyberte možnost **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

6. V seznamu **úlohy instalace** vyberte možnost **integrace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-integration.png)

7. V části **spravovat ověřování**vyberte **jednotné přihlašování SAML**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml.png)

8. V podokně **Nastavení SAML** v části **Konfigurace NetSuite**postupujte takto:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Zaškrtněte políčko **primární metoda ověřování** .

    b. V části **metadata poskytovatele identity SAMLV2**vyberte **nahrát soubor metadat IDP**a pak vyberte **Procházet** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    c. Vyberte **Odeslat**.

9. V horním navigačním panelu NetSuite vyberte **Nastavení**a pak vyberte**informace o**společnosti **společnosti** > .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-account-id.png)

    b. V podokně **informace o společnosti** v pravém sloupci ZKOPÍRUJTE hodnotu **ID účtu** .

    c. Vložte **ID účtu** , které jste zkopírovali z účtu NetSuite do pole **hodnota atributu** ve službě Azure AD.

10. Předtím, než mohou uživatelé provádět jednotné přihlašování do NetSuite, musí být nejprve přiřazena příslušná oprávnění v NetSuite. Chcete-li přiřadit tato oprávnění, postupujte takto:

    a. V horním navigačním panelu vyberte možnost **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    b. V levém podokně vyberte **Uživatelé/role**a pak vyberte **Spravovat role**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Vyberte **Nová role**.

    d. Zadejte **název** nové role.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-new-role.png)

    e. Vyberte **Uložit**.

    f. V horním navigačním panelu vyberte **oprávnění**. Pak vyberte **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-sso.png)

    g. Vyberte **jednotné přihlašování SAML**a pak vyberte **Přidat**.

    h. Vyberte **Uložit**.

    i. V horním navigačním panelu vyberte **Nastavení**a pak vyberte **Správce instalace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    j. V levém podokně vyberte **Uživatelé/role**a pak vyberte **Spravovat uživatele**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Vyberte testovacího uživatele, vyberte **Upravit**a pak vyberte kartu **přístup** .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-edit-user.png)

    l. V podokně **role** přiřaďte příslušnou roli, kterou jste vytvořili.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-role.png)

    m. Vyberte **Uložit**.

### <a name="create-the-netsuite-test-user"></a>Vytvořit testovacího uživatele NetSuite

V této části se v NetSuite vytvoří uživatel s názvem B. Simon. NetSuite podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V tomto oddílu není žádná položka akce. Pokud uživatel ještě v NetSuite neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici NetSuite, měli byste se automaticky přihlásit k NetSuite, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Vyzkoušejte si NetSuite s Azure AD](https://aad.portal.azure.com/)
- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit NetSuite pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)