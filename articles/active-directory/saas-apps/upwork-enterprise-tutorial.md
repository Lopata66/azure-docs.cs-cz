---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s podporou pro práci v podniku | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pracovními podniky.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 254631e2-1a77-43e6-acb1-b26ba7e77efc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 421f799d7f4c76992794684379f3bb0088602ac0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533619"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upwork-enterprise"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s podporou pro práci v podniku

V tomto kurzu se dozvíte, jak integrovat práci v podniku pomocí Azure Active Directory (Azure AD). Když integrujete práci v podniku s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k podnikovým činnostem.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k podnikovým firmám prostřednictvím svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Vypracujte předplatné s povoleným podnikovým jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Práce v podniku podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Práce v podniku podporuje zřizování uživatelů **jenom včas**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-upwork-enterprise-from-the-gallery"></a>Přidání práce v podniku z Galerie

Pokud chcete nakonfigurovat integraci podnikových prostředí do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat organizaci pro práci z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Enterprise Work** .
1. Vyberte možnost **práce v podniku** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-upwork-enterprise"></a>Konfigurace a testování jednotného přihlašování Azure AD pro podnikovou práci

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí podnikového testu s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v podnikovém prostředí.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím podnikového podniku, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace inpracujení podnikového jednotného přihlašování](#configure-upwork-enterprise-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte si pracovníka pro práci v podniku](#create-upwork-enterprise-test-user)** , abyste měli protějšek B. Simon v zaměstnání, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **podnikových** aplikací pro práci najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.upwork.com/ab/account-security/login`

1. Klikněte na možnost **Uložit**.

1. Podniková aplikace při práci očekává kontrolní výrazy SAML v konkrétním formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává podniková aplikace pro práci v reakci SAML méně dalších atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) |   Zdrojový atribut|
    | ------------ | --------- |
    | Jméno | user.givenname |
    | Příjmení | user.surname |
    | Země | user.country |
    | E-mail | user.userprincipalname |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavit podnik pro práci** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k podnikovému provozu.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **práce v podniku**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-upwork-enterprise-sso"></a>Konfigurace inpracování podnikového jednotného přihlašování

Chcete-li nakonfigurovat jednotné přihlašování při **práci na podnikové** straně, je třeba odeslat stažená **metadata federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podnikové podpory pro práci](https://support.upwork.com/hc/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-upwork-enterprise-test-user"></a>Vytvořit pracovní uživatele pro práci v podniku

V této části se uživatel s názvem B. Simon vytvoří v podniku pro práci. Při provozu podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v podniku v rámci práce ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici práce v podniku, měli byste být automaticky přihlášeni k podnikovému podniku, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si práci v podniku s Azure AD](https://aad.portal.azure.com/)