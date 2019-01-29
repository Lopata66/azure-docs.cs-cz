---
title: 'Kurz: Integrace Azure Active Directory s ordinovaný světový | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Versal odkazů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: 91f069da5572394934da41039d7e9a5950089ea1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185103"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Kurz: Integrace Azure Active Directory s Versal odkazů

V tomto kurzu se dozvíte, jak integrovat Versal odkazů se službou Azure Active Directory (Azure AD).

Ordinovaný světový integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Versal odkazů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Versal odkazů (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Versal odkazů, potřebujete následující položky:

- Předplatné Azure AD
- Verzál jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Versal odkazů z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-versal-from-the-gallery"></a>Přidání Versal odkazů z Galerie
Konfigurace integrace Versal odkazů do služby Azure AD, budete muset přidat Versal odkazů na seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Versal odkazů z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **ordinovaný světový**vyberte **ordinovaný světový** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Versal odkazů v seznamu výsledků](./media/versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Versal odkazů podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Versal odkazů je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Versal odkazů.

V Versal odkazů, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Versal odkazů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořte verzál testovacího uživatele](#create-a-versal-test-user)**  – Pokud chcete mít protějšek Britta Simon Versal odkazů, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci verzál.

**Ke konfiguraci Azure AD jednotné přihlašování s Versal odkazů, proveďte následující kroky:**

1. Na webu Azure Portal na **ordinovaný světový** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/versal-tutorial/tutorial_versal_samlbase.png)

1. Na **ordinovaný světový domény a adresy URL** části, proveďte následující kroky:

    ![Verzál domény a adresy URL jednotného přihlašování – informace](./media/versal-tutorial/tutorial_versal_url.png)

    a. V **identifikátor** textového pole zadejte hodnotu: `VERSAL`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > Hodnota adresy URL odpovědi není skutečný. Aktualizujte tuto hodnotu Skutečná adresa URL odpovědi. Kontakt [tým podpory verzál](https://support.versal.com/hc/) tuto výhodu získáte.
    
1. Vaše aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale **ordinovaný světový** očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace.
    
    ![Rozevírací nabídka identifikátor uživatele](./media/versal-tutorial/tutorial_versal_attribute.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/versal-tutorial/tutorial_versal_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/versal-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **ordinovaný světový** straně, je nutné odeslat na stažený **soubor XML s metadaty** a **podpisový certifikát SAML** k [tým verzál podpory ](https://support.versal.com/hc/). Nakonfiguruje organizaci verzál SAML SSO připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/versal-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/versal-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/versal-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/versal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-versal-test-user"></a>Vytvořte verzál testovacího uživatele

V této části vytvořte uživatele Britta Simon v Versal odkazů. Postupujte prosím podle [vytváření SAML testovací uživatele](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) podpora průvodce pro vytvoření uživatele Britta Simon v rámci vaší organizace. Uživatelé musí vytvořit a aktivovat v Versal odkazů, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Versal odkazů.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Versal odkazů, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ordinovaný světový**.

    ![Odkaz Versal odkazů v seznamu aplikací](./media/versal-tutorial/tutorial_versal_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí verzál kurzu vložené na vašem webu.
Podrobnosti najdete [vkládání organizační kurzy](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML jednotného přihlašování** podpora příručka pokyny o tom, jak díky podpoře pro Azure AD pro vložení verzál kurzu jednotného přihlašování. 

Je potřeba vytvořit kurz, sdílet s organizací a publikovat účely otestování kurzu vkládání. Podrobnosti najdete na [vytváření kurz](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [publikování kurz](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), a [kurzu a learner správu](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) Další informace.  
                     

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/versal-tutorial/tutorial_general_01.png
[2]: ./media/versal-tutorial/tutorial_general_02.png
[3]: ./media/versal-tutorial/tutorial_general_03.png
[4]: ./media/versal-tutorial/tutorial_general_04.png

[100]: ./media/versal-tutorial/tutorial_general_100.png

[200]: ./media/versal-tutorial/tutorial_general_200.png
[201]: ./media/versal-tutorial/tutorial_general_201.png
[202]: ./media/versal-tutorial/tutorial_general_202.png
[203]: ./media/versal-tutorial/tutorial_general_203.png

