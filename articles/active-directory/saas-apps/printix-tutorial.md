---
title: 'Kurz: Azure Active Directory integrace s Printix | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "62111447"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Kurz: Azure Active Directory integrace s Printix

V tomto kurzu se dozvíte, jak integrovat Printix s Azure Active Directory (Azure AD).

Integrace Printix s Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, kteří mají přístup k Printix.
- Uživatelům můžete povolit, aby se k účtům Azure AD automaticky přihlásili k Printix (jednotné přihlašování).
- Účty můžete spravovat v jednom centrálním umístění – Azure Portal

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Printix potřebujete následující položky:

- Předplatné Azure AD
- Předplatné s povoleným Printixm jednotným přihlašováním

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [si ho stáhnout](https://azure.microsoft.com/pricing/free-trial/)na měsíc zdarma.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. Scénář, který je popsaný v tomto kurzu, se skládá ze dvou hlavních stavebních bloků:

1. Přidání Printix z Galerie
1. Konfigurace a testování jednotného přihlašování Azure AD

## <a name="adding-printix-from-the-gallery"></a>Přidání Printix z Galerie
Pokud chcete nakonfigurovat integraci Printix do služby Azure AD, musíte přidat Printix z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Printix z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** . 

    ![Active Directory][1]

1. Přejděte k **podnikovým aplikacím**. Pak přejdete na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Printix**.

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. Na panelu výsledků vyberte **Printix**a pak klikněte na **Přidat** tlačítko pro přidání aplikace.

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD
V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Printix na základě testovacího uživatele s názvem "Britta Simon".

Aby se jednotné přihlašování fungovalo, musí Azure AD zjistit, co se uživatel partnerského systému v Printix uživateli v Azure AD. Jinými slovy, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Printix.

V Printix přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu uživatelského **jména** , aby se mohl vytvořit vztah propojení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Printix, musíte dokončit tyto stavební bloky:

1. **[Konfiguruje se jednotné přihlašování Azure AD](#configuring-azure-ad-single-sign-on)** , které umožní uživatelům používat tuto funkci.
1. **[Vytvoření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)** – testování jednotného přihlašování Azure AD pomocí Britta Simon
1. **[Vytvoření testovacího uživatele Printix](#creating-a-printix-test-user)** , který má protějšek Britta Simon v Printix, který je propojený s reprezentací uživatele Azure AD.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)** – povolení Britta Simon pro použití jednotného přihlašování Azure AD
1. **[Testování jednotného přihlašování](#testing-single-sign-on)** – ověřte, zda konfigurace funguje.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguruje se jednotné přihlašování Azure AD.

V této části povolíte jednotné přihlašování Azure AD v Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Printix.

**Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Printix, proveďte následující kroky:**

1. V Azure Portal na stránce integrace aplikací **Printix** klikněte na **jednotné přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. V dialogovém okně **jednotného přihlašování** vyberte **režim** jako **přihlašování založené na SAML** pro povolení jednotného přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. V části **doména a adresy URL Printix** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_url.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Printix](mailto:support@printix.net) . 
 
1. V části **podpisový certifikát SAML** klikněte na **metadata XML** a uložte soubor metadat do svého počítače.

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klikněte na tlačítko **Uložit** .

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_general_400.png)

1. Přihlaste se ke svému tenantovi Printix jako správce.

1. V nabídce v horní části klikněte na ikonu v pravém horním rohu a vyberte**ověřování**.
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_06.png)

1. Na kartě **Nastavení** vyberte **Povolit ověřování Azure/Office 365** .
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_07.png)

1. Na kartě **Azure** zadejte adresu URL federačních metadat do textového pole**dokumentu federačních metadat**. 

    Připojte soubor XML s metadaty, který jste stáhli z Azure AD do [Printix týmu podpory](mailto:support@printix.net). Pak nahrajte soubor XML a zadejte adresu URL federačních metadat.
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klikněte na tlačítko**test**a po úspěšném dokončení testu klikněte na tlačítko**OK**.
   
     Po kliknutí na tlačítko **test** se zobrazí stránka Azure Active Directory. "Test byl úspěšný, sem znamená po zadání přihlašovacích údajů ke svému účtu Azure test se zobrazí zpráva" nastavení testováno OK ". Pak klikněte na tlačítko **OK** .
   
    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_09.png)

1. Klikněte na tlačítko **Uložit** na stránce**ověřování**.


> [!TIP]
> Při nastavování aplikace si teď můžete přečíst stručnou verzi těchto instrukcí v rámci [Azure Portal](https://portal.azure.com).  Po přidání této aplikace z části **Active Directory > Enterprise** Apps stačí kliknout na kartu **jednotného přihlašování** a získat přístup k integrované dokumentaci v části **Konfigurace** v dolní části. Další informace o funkci integrovaná dokumentace si můžete přečíst tady: [dokumentace k Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985) .
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD
Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **Azure Portal**v levém navigačním podokně klikněte na ikonu **Azure Active Directory** .

    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Pokud chcete zobrazit seznam uživatelů, přejděte na **Uživatelé a skupiny** a klikněte na **Všichni uživatelé**.
    
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít dialogové okno **uživatel** , klikněte na tlačítko **Přidat** v horní části dialogového okna.
 
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Na stránce **uživatelský** dialog proveďte následující kroky:
 
    ![Vytvoření testovacího uživatele Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. Do textového pole **název** zadejte **BrittaSimon**.

    b. Do textového pole **uživatelské jméno** zadejte **e-mailovou adresu** BrittaSimon.

    c. Vyberte možnost **Zobrazit heslo** a zapište hodnotu **hesla**.

    d. Klikněte na **Vytvořit**.
 
### <a name="creating-a-printix-test-user"></a>Vytváření Printix testovacího uživatele

Cílem této části je vytvořit uživatele s názvem Britta Simon v Printix. Printix podporuje zřizování za běhu, které je ve výchozím nastavení povolené.

V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k Printix, pokud ještě neexistuje. 

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazuje se testovací uživatel Azure AD.

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Printix.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon k Printix, proveďte následující kroky:**

1. V Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře a přejděte do části **podnikové aplikace** a klikněte na **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikace vyberte **Printix**.

    ![Konfigurace jednotného přihlašování](./media/printix-tutorial/tutorial_printix_app.png) 

1. V nabídce na levé straně klikněte na **Uživatelé a skupiny**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **Přidat** . Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Přiřadit uživatele][203]

1. V dialogu **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** .

1. Klikněte na tlačítko **Vybrat** v dialogovém okně **Uživatelé a skupiny** .

1. Klikněte na tlačítko **přiřadit** v dialogovém okně **Přidat přiřazení** .
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Printix, měli byste se automaticky přihlásili ke své aplikaci Printix.

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

