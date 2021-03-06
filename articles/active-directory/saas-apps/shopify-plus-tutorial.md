---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Shopify plus | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Shopify Plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: d7a5d160fa8a0209478fe81d2880e8956828350d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Shopify plus

V tomto kurzu se dozvíte, jak integrovat Shopify Plus s Azure Active Directory (Azure AD). Když integruje Shopify Plus s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Shopify Plus.
* Umožněte uživatelům, aby se automaticky přihlásili k Shopify a s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Shopify plus odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Shopify Plus podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

* Po nakonfigurování Shopify Plus můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>Přidání Shopify Plus z Galerie

Pokud chcete nakonfigurovat integraci Shopify plus do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Shopify Plus z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Shopify plus** .
1. Vyberte **Shopify plus** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Shopify plus

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Shopify plus pomocí testovacího uživatele s názvem **B. Simon**. K tomu, aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Shopify Plus.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Shopify plus, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Shopify plus SSO](#configure-shopify-plus-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Shopify plus testovacího uživatele](#create-shopify-plus-test-user)** – můžete mít protějšek B. Simon v Shopify plus, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Shopify plus** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://shopify.plus/login`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, kontaktujte [tým podpory Shopify plus](mailto:plus-user-management@shopify.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Shopify plus očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace Shopify plus očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ---- | --------------- |
    | e-mail | uživatel. pošta |

1. Změňte formát **ID pro název** na **trvalá**. Vyberte možnost **jedinečný identifikátor uživatele (název ID)** a pak vyberte formát **identifikátoru názvu** . Pro tuto možnost vyberte možnost **trvalá** . Uložte provedené změny.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte tlačítko Kopírovat a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Shopify Plus.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Shopify plus**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-shopify-plus-sso"></a>Konfigurace jednotného přihlašování Shopify plus

Postup zobrazení úplných kroků najdete v [dokumentaci k Shopify o nastavení integrace SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Shopify plus** , zkopírujte **adresu URL federačních metadat aplikace** z Azure Active Directory. Pak se přihlaste ke [správci organizace](https://shopify.plus) a pokračujte **Users** na  >  **zabezpečení** uživatelů. Vyberte **nastavit konfiguraci** a potom do oddílu **Adresa URL metadat poskytovatele identity** vložte adresu URL federačních metadat aplikace. Chcete-li dokončit tento krok, vyberte možnost **Přidat** .

### <a name="create-shopify-plus-test-user"></a>Vytvořit Shopify a testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v Shopify Plus. Vraťte se do části **Uživatelé** a přidejte uživatele zadáním jejich e-mailu a oprávnění. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="enforce-saml-authentication"></a>Vynutilo ověřování SAML

> [!NOTE]
> Před použitím jednotlivých uživatelů doporučujeme testování integrace.

Jednotliví uživatelé:
1. Přejít na stránku individuálního uživatele v Shopify Plus s e-mailovou doménou spravovanou službou Azure AD a ověřit v Shopify Plus.
1. V části ověřování SAML vyberte možnost **Upravit**, vyberte možnost **požadováno** a pak vyberte **Uložit**.
1. Otestujte, jestli se tento uživatel může úspěšně přihlásit přes idP a iniciované toky SP.

Pro všechny uživatele v e-mailové doméně:
1. Vraťte se na stránku **zabezpečení** .
1. Vyberte možnost **požadováno** pro nastavení ověřování SAML. Vynutilo protokol SAML pro všechny uživatele s touto e-mailovou doménou v Shopify Plus.
1. Vyberte **Uložit**.

> [!IMPORTANT]
> Povolení protokolu SAML pro všechny uživatele v e-mailové doméně má vliv na všechny uživatele, kteří používají tuto aplikaci. Uživatelé se nebudou moci přihlásit pomocí běžné přihlašovací stránky. Budou mít k aplikaci přístup jenom prostřednictvím Azure Active Directory. Shopify neposkytuje přihlašovací adresu URL pro zálohování, na které se uživatelé můžou přihlásit pomocí normálního uživatelského jména a hesla. V případě potřeby můžete kontaktovat podporu Shopify a vypnout SAML.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Shopify Plus na přístupovém panelu, měli byste se automaticky přihlásit k Shopify a, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Shopify Plus s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Shopify Plus s pokročilou viditelností a ovládacími prvky](/cloud-app-security/proxy-intro-aad)