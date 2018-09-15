---
title: 'Kurz: Integrace Azure Active Directory s tlačí se k | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tlačí se k.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: jeedes
ms.openlocfilehash: fc4ea2538ebe5876e8f3572ab8ad76c4b3b44b8c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634332"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Kurz: Integrace Azure Active Directory s tlačí se k

V tomto kurzu se dozvíte, jak integrovat tlačí se k Azure Active Directory (Azure AD).

Integrování tlačí se k Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k tlačí se k
- Můžete povolit uživatelům, aby automaticky získat přihlášení k tlačí se k (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s tlačí se k, potřebujete následující položky:

- Předplatné Azure AD
- Tlačí se k jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tlačí se k z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-huddle-from-the-gallery"></a>Přidání tlačí se k z Galerie

Pokud chcete nakonfigurovat integraci tlačí se k Azure AD, musíte doplnit tlačí se k z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat tlačí se k z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **tlačí se k**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/tutorial_huddle_search.png)

5. Na panelu výsledků vyberte **tlačí se k**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s tlačí se k podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v tlačí se k je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v tlačí se k musí být vytvořeno.

Ke konfiguraci a Azure AD jednotné přihlašování s tlačí se k testování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele tlačí se k](#creating-a-huddle-test-user)**  – Pokud chcete mít protějšek Britta Simon tlačí, který je propojený s Azure AD reprezentace uživatel se k.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v tlačí se k aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s tlačí se k, proveďte následující kroky:**

1. Na webu Azure Portal na **tlačí se k** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

3. Na **tlačí se k doméně a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    > [!NOTE]
    > Tlačí se k instanci se zjišťují automaticky z domény, které zadáte níže.

    ![Tlačí se k doméně a adresy URL jednotné přihlašování – informace](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. V **identifikátor** textového pole zadejte některou z adresy URL, pomocí následujícího vzorce:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.net`|
    | `https://my.huddle.net` |
    | |

    b. V **adresy URL odpovědi** textového pole zadejte některou z adresy URL, pomocí následujícího vzorce:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.net/saml/idp-initiated-sso`|
    | `https://my.huddle.net/saml/idp-initiated-sso`|
    | |

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Tlačí se k doméně a adresy URL jednotné přihlašování – informace](./media/huddle-tutorial/tutorial_huddle_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte některou z adresy URL, pomocí následujícího vzorce:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.net`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [klienta tlačí se k týmu podpory](https://huddle.zendesk.com) k získání těchto hodnot.

5. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_general_400.png)

7. Na **tlačí se k konfigurace** klikněte na tlačítko **konfigurace tlačí se k** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_configure.png) 

8. Pokud chcete nakonfigurovat jednotné přihlašování na straně tlačí se k, je nutné odeslat na stažený **certifikát**, **SAML jednotné přihlašování – adresa URL služby**, a **SAML Entity ID** k [ Tlačí se k týmu podpory klienta](https://huddle.zendesk.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.  

    >[!NOTE]
    > Jednotné přihlašování je potřeba povolit tlačí se k tým podpory. Po dokončení konfigurace, dostanete oznámení.
    >

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-huddle-test-user"></a>Vytvoření zkušebního uživatele tlačí se k

Přihlaste se k tlačí se k Azure AD uživatelům umožnit, musí být poskytnuty do tlačí se k. V případě tlačí se k zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **tlačí se k** společnosti serveru jako správce.

2. Klikněte na tlačítko **pracovní prostor**.

3. Klikněte na tlačítko **lidé \> pozvat**.
   
    ![Lidé](./media/huddle-tutorial/IC787838.png "osoby")

4. V **vytvořit nová pozvánka** části, proveďte následující kroky:
   
    ![Nová Pozvánka](./media/huddle-tutorial/IC787839.png "nová pozvánka")
   
    a. V **zvolte tým, který pozvat** seznamu vyberte **týmu**.

    b. Typ **e-mailovou adresu** platný Azure AD účet, který chcete zřídit v **zadejte e-mailová adresa osoby, které chcete pozvat** textového pole.

    c. Klikněte na tlačítko **pozvat**.   
   
    >[!NOTE]
    > Držitel účtu Azure AD, dostanou e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní. 
    > 

>[!NOTE]
>Další tlačí se k nástrojů pro vytváření účtu uživatele nebo rozhraní API poskytovaných tlačí se k můžete použít ke zřízení uživatelských účtů služby Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k tlačí se k použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Chcete-li přiřadit tlačí se k Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **tlačí se k**.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici tlačí se k na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku tlačí se k aplikaci.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
