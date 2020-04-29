---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s monitorem výkonu AppNeta | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac9e1d32e0280bcf053578aa102cc6fd200a4b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75561207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s monitorováním výkonu AppNeta

V tomto kurzu se dozvíte, jak integrovat AppNeta Performance Monitor s Azure Active Directory (Azure AD). Když integrujete AppNeta Performance Monitor s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AppNeta Performance Monitor.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AppNeta sledování výkonu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AppNeta Performance Monitor (jednotné přihlašování) – odběr povolený.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AppNeta Performance Monitor podporuje jednotné přihlašování spouštěné v **SP**


* AppNeta Performance Monitor podporuje **jenom dobu** zřizování uživatelů

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Přidání sledování výkonu AppNeta z Galerie

Pokud chcete nakonfigurovat integraci sledování výkonu AppNeta do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat AppNeta sledování výkonu z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AppNeta Performance Monitor** .
1. Vyberte **AppNeta Performance Monitor** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Konfigurace a testování jednotného přihlašování Azure AD pro monitorování výkonu AppNeta

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s AppNeta sledováním výkonu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v monitorování výkonu AppNeta.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje AppNeta Performance Monitor, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování ke **[sledování výkonu AppNeta](#configure-appneta-performance-monitor-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** -to znamená, že má protějšek B. Simon v monitoru výkonu AppNeta, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **sledování výkonu AppNeta** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.pm.appneta.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte hodnotu:`PingConnect`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory pro monitorování výkonu AppNeta](mailto:support@appneta.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace AppNeta Performance Monitor očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace AppNeta Performance Monitor několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | --------| ----------------|
    | firstName| User. křestní jméno|
    | lastName| User. příjmení|
    | e-mail| User. userPrincipalName|
    | jméno| User. userPrincipalName|
    | skupiny  | User. assignedroles |
    | Android| User. telephoneNumber |
    | title| User. jobtitle|
    | | |

    > [!NOTE]
    > **skupiny** odkazují na skupinu zabezpečení v Appneta, která je namapovaná na **roli** ve službě Azure AD. Podívejte se prosím na [Tento](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) dokument, který vysvětluje, jak vytvářet vlastní role v Azure AD.

    1. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    1. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    1. Ponechte **obor názvů** prázdný.

    1. Jako **atribut**vyberte zdroj.

    1. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    1. Klikněte na **OK** .

    1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení sledování výkonu AppNeta** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke sledování výkonu AppNeta.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AppNeta Performance Monitor**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-appneta-performance-monitor-sso"></a>Konfigurace jednotného přihlašování AppNeta Performance Monitor

Chcete-li nakonfigurovat jednotné přihlašování na straně **nástroje AppNeta Performance Monitor** , je třeba odeslat stažená **metadata federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory AppNeta Performance Monitor](mailto:support@appneta.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-appneta-performance-monitor-test-user"></a>Vytvořit testovacího uživatele pro sledování výkonu AppNeta

V této části se v monitorování výkonu AppNeta vytvoří uživatel s názvem Britta Simon. AppNeta Performance Monitor podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v nástroji Sledování výkonu AppNeta neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici AppNeta Performance Monitor na přístupovém panelu byste měli být automaticky přihlášeni ke sledování výkonu AppNeta, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte AppNeta Performance Monitor s Azure AD](https://aad.portal.azure.com/)

