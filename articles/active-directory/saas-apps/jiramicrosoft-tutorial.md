---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML JIRA microsoftem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML JIRA společností Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: jeedes
ms.openlocfilehash: 7d25b072ffc41608bca9e0b23116b2eb453dd160
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740344"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML JIRA společností Microsoft

V tomto kurzu se dozvíte, jak integrovat JIRA SAML SSO, Microsoft Azure Active Directory (Azure AD).

Integrace jednotného přihlašování SAML JIRA společností Microsoft s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k systému JIRA SAML SSO společností Microsoft.
- Uživatele, aby automaticky získat podepsaný ve službě JIRA SAML SSO společností Microsoft (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Popis

Povolit jednotné přihlašování pomocí účtu Microsoft Azure Active Directory serverem Atlassian JIRA. Tímto způsobem všichni uživatelé vaší organizace můžete použít přihlašovací údaje služby Azure AD k přihlášení do aplikace systému JIRA. Tento modul plug-in používá protokol SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML JIRA microsoftem, potřebujete následující položky:

- Předplatné Azure AD
- Základní JIRA a softwaru 6.0 k 7.8 nebo JIRA Service Desk 3.0 3.2 by měl nainstalovaná a nakonfigurovaná na verzi Windows 64-bit
- JIRA server je povolen protokol HTTPS
- Všimněte si, že podporované verze pro modul plug-in JIRA jsou uvedeny v následující části.
- JIRA server je dostupný na Internetu zejména na stránku pro přihlášení k Azure AD pro ověřování a měli byste může přijímat token ze služby Azure AD
- Přihlašovací údaje správce jsou nastavené ve službě JIRA
- WebSudo je zakázaný ve službě JIRA
- Testovací uživatel v serveru aplikaci JIRA

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkčního prostředí JIRA. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Podporované verze systému JIRA

* Základní JIRA a Software: 6.0 k 7.12
* Oddělení služeb JIRA 3.0.0 k 3.5.0
* JIRA podporuje také 5.2. Další podrobnosti získáte kliknutím [Microsoft Azure Active Directory jednotného přihlašování pro JIRA 5.2](jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování SAML JIRA společností Microsoft z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotného přihlašování SAML JIRA společností Microsoft z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML JIRA microsoftem do služby Azure AD, budete muset přidat JIRA SAML SSO společností Microsoft z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání jednotného přihlašování SAML JIRA společností Microsoft z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **JIRA SAML SSO microsoftem**vyberte **JIRA SAML SSO microsoftem** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![JIRA SAML SSO společností Microsoft v seznamu výsledků](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA společností Microsoft na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v systému JIRA SAML SSO společností Microsoft je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému JIRA SAML SSO společností Microsoft.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA microsoftem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření JIRA SAML SSO Microsoft testovací uživatel](#creating-jira-saml-sso-by-microsoft-test-user)**  – Pokud chcete mít protějšek Britta Simon v systému JIRA SAML SSO společnosti Microsoft, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vaší službě JIRA SAML SSO aplikací společnosti Microsoft.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML JIRA microsoftem, proveďte následující kroky:**

1. Na webu Azure Portal na **JIRA SAML SSO microsoftem** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![JIRA SAML SSO Microsoft Domain a adresy URL jednotné přihlašování – informace](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Port je volitelný, v případě, že je pojmenovaný URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in Jira, který je vysvětlen později v tomto kurzu.

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/jiramicrosoft-tutorial/tutorial_metadataurl.png) 

6. V okně jiné webové prohlížeče Přihlaste se k vaší instanci JIRA jako správce.

7. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.

    ![Konfigurace jednotného přihlašování](./media/jiramicrosoft-tutorial/addon1.png)

8. Stáhněte si modul plug-in z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Ručně nahrát modul plug-in poskytl pomocí Microsoft **nahrát doplněk** nabídky. Stáhnout modul plug-in se vztahuje smlouva [servisní smlouvou aplikace Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurace jednotného přihlašování](./media/jiramicrosoft-tutorial/addon12.png)

9. Když je nainstalovaný modul plug-in, zobrazí se v **uživatel nainstaloval** doplňky část **spravovat doplněk** části. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/jiramicrosoft-tutorial/addon13.png)

10. Proveďte následující kroky na stránce konfigurace:

    ![Konfigurace jednotného přihlašování](./media/jiramicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Ujistěte se, že je pouze jeden certifikát mapují na aplikaci tak, aby se nezobrazí žádná chyba překladu metadat. Pokud existuje víc certifikátů, po vyřešení metadata a získá správce k chybě.

    a. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z webu Azure portal a klikněte **vyřešit** tlačítko. Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

    b. Kopírovat **identifikátor, adresa URL odpovědi a adresa URL přihlašování** hodnoty a vložit je do **identifikátor, adresa URL odpovědi a adresa URL přihlašování** textová pole v uvedeném pořadí **JIRA SAML SSO Microsoft Domain a adresy URL** části na webu Azure portal.

    c. V **přihlašovací jméno tlačítko** zadejte název tlačítka, které vaše organizace chce, aby se uživatelům zobrazí na obrazovce přihlášení.

    d. V **SAML uživatelské ID umístění** vyberte buď **ID uživatele není v elementu NameIdentifier příkazu subjektu** nebo **ID uživatele není v elementu atribut**.  Toto ID musí být id uživatele JIRA. Pokud neodpovídá id uživatele, systém nedovolí uživatelům přihlášení.

    > [!Note]
    > Výchozí umístění SAML ID uživatele je název identifikátoru. Můžete to změnit atribut možnost a zadejte odpovídající název.

    e. Pokud vyberete **ID uživatele není v elementu atribut** možnost, pak v **název atributu** textového pole zadejte název atributu, kde se očekává Id uživatele.

    f. Pokud používáte federovanou doménu (například služby AD FS atd.) pomocí služby Azure AD, potom klikněte na **povolit zjišťování domovské sféry** řádku a nakonfigurovat **název domény**.

    g. V **název domény** zadejte název domény zde v případě přihlášení na základě služby AD FS.

    h. Zkontrolujte **povolit jednotné přihlašování,** Pokud se chcete odhlásit z Azure AD, když uživatel odhlásí ze systému JIRA.

    i. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží s [příručky pro správce konektoru MS JIRA jednotného přihlašování](../ms-confluence-jira-plugin-adminguide.md) a k dispozici je také [nejčastější dotazy k](../ms-confluence-jira-plugin-faq.md) vaši pomoc

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-jira-saml-sso-by-microsoft-test-user"></a>Vytváření JIRA jednotného přihlašování SAML podle Microsoft testovacího uživatele

Umožňuje uživatelům Azure AD pro přihlášení k systému JIRA na místním serveru, musí být zřízená do JIRA SAML SSO společností Microsoft. Zřizování je pro jednotné přihlašování SAML JIRA microsoftem, úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k serveru v místním systému JIRA jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/jiramicrosoft-tutorial/user1.png)

3. Budete přesměrováni na stránku přístup správce k zadání **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Přidat zaměstnance](./media/jiramicrosoft-tutorial/user2.png)

4. V části **Správa uživatelů** kartě oddíl, klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/jiramicrosoft-tutorial/user3.png) 

5. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/jiramicrosoft-tutorial/user4.png) 

    a. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k JIRA SAML SSO společností Microsoft.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **JIRA SAML SSO microsoftem**.

    ![Konfigurace jednotného přihlašování](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na JIRA SAML SSO podle Microsoft dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší JIRA SAML SSO aplikací společnosti Microsoft.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
