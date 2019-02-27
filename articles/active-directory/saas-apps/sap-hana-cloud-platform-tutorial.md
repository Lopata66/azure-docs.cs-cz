---
title: 'Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAPU | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudové platformy SAPU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a7fa71f2cb9d2c5326eea75af40e1ab6b986eb9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864868"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAP

V tomto kurzu se dozvíte, jak integrovat cloudové platformy SAPU se službou Azure Active Directory (Azure AD).
Integrace cloudové platformy SAPU v Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke cloudové platformy SAPU.
* Uživatelům se automaticky přihlášeni do cloudové platformy SAP (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s využitím cloudové platformy SAP, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP Cloudovou platformu jednotného přihlašování povolená předplatného

Po dokončení tohoto kurzu, budou moci jednotné přihlašování do aplikace pomocí uživatele Azure AD, který jste přiřadili do cloudové platformy SAP [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Budete muset nasaďte svou vlastní aplikaci nebo si chcete otestovat jednotné přihlašování v aplikaci na vašem účtu cloudové platformy SAPU. V tomto kurzu je aplikace nasazená v rámci účtu.
> 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje cloudové platformy SAPU **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání cloudové platformy SAPU z Galerie

Konfigurace integrace cloudové platformy SAP do Azure AD, budete muset přidat cloudové platformy SAPU v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat cloudové platformy SAPU z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **cloudové platformy SAPU**vyberte **cloudové platformy SAPU** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cloudové platformy SAPU v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí SAP Cloudová platforma založená na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v cloudové platformy SAPU.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím cloudové platformy SAP, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP Cloud Platform jednotného přihlašování](#configure-sap-cloud-platform-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření cloudové platformy SAPU testovacího uživatele](#create-sap-cloud-platform-test-user)**  – Pokud chcete mít protějšek Britta Simon SAP cloudové platformě, která souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s využitím cloudové platformy SAP, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **cloudové platformy SAPU** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Doména cloudové platformy SAP a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL použili vašich uživatelů pro přihlášení do vaší **cloudové platformy SAPU** aplikace. Toto je adresa URL účtu konkrétní chráněného prostředku ve vaší aplikaci cloudové platformy SAPU. Adresa URL je založena na vzoru následující: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Toto je adresa URL, která vyžaduje, aby uživatel k ověření aplikace cloudové platformy SAPU.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. V **identifikátor** textové pole, které vám poskytne vaše cloudové platformy SAPU zadejte adresu URL pomocí jedné z následujících vzorů: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory SAP Cloud Platform klienta](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) přihlašovací adresu URL a identifikátor. Adresa URL odpovědi můžete získat z části Správa vztahu důvěryhodnosti, který je vysvětlen později v tomto kurzu.
    > 
4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurace SAP Cloud Platform jednotného přihlašování

1. V okně jiný webový prohlížeč, přihlaste se k SAP řídicí panel Cloud Platform na `https://account.<landscape host>.ondemand.com/cockpit`(například: https://account.hanatrial.ondemand.com/cockpit).

2. Klikněte na tlačítko **důvěřovat** kartu.
   
    ![Důvěřovat](./media/sap-hana-cloud-platform-tutorial/ic790800.png "důvěřovat")

3. V části Správa důvěřovat v rámci **místní poskytovatele služeb**, proveďte následující kroky:

    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic793931.png "důvěřovat správy")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace**vyberte **vlastní**.

    c. Jako **místní název zprostředkovatele**, ponechte výchozí hodnotu. Zkopírujte tuto hodnotu a vložte ho do **identifikátor** v konfiguraci Azure AD pro cloudové platformy SAPU.

    d. Ke generování **podpisový klíč** a **certifikát pro podpis** pár klíčů, klikněte na tlačítko **Generování páru klíč**.

    e. Jako **hlavní šíření**vyberte **zakázané**.

    f. Jako **Authentication vynutit**vyberte **zakázané**.

    g. Klikněte na **Uložit**.

4. Po uložení **místní poskytovatele služeb** nastavení, proveďte následující kroky k získání adresy URL odpovědi:
   
    ![Získat Metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "získat Metadata")

    a. Kliknutím stáhněte soubor metadat cloudové platformy SAPU **získat Metadata**.

    b. Otevřete stažený soubor metadat XML cloudové platformy SAPU a najděte **ns3:AssertionConsumerService** značky.
 
    c. Zkopírujte hodnotu **umístění** atribut a vložte jej do **adresy URL odpovědi** v konfiguraci Azure AD pro cloudové platformy SAPU.

5. Klikněte na tlačítko **důvěryhodné zprostředkovatele Identity** kartu a potom klikněte na tlačítko **přidat důvěryhodného zprostředkovatele Identity**.
   
    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic790802.png "důvěřovat správy")
   
    >[!NOTE]
    >Ke správě seznamu Důvěryhodné zprostředkovatele identity, budete muset zvolili vlastní typ konfigurace v části místní poskytovatele služeb. Pro typ výchozí konfigurace je nutné jako neupravovatelné a implicitní vztah důvěryhodnosti k ID služby SAP. Pro žádný není nutné žádné nastavení vztahu důvěryhodnosti.
    > 
    > 

6. Klikněte na tlačítko **Obecné** kartu a potom klikněte na tlačítko **Procházet** nahrát soubor stažený metadat.
    
    ![Důvěřovat správu](./media/sap-hana-cloud-platform-tutorial/ic793932.png "důvěřovat správy")
    
    >[!NOTE]
    >Po nahrání souboru metadat, hodnoty **přihlašovací adresu URL jednotného**, **adresu URL jednotného odhlašování**, a **certifikát pro podpis** se vyplní automaticky.
    > 
     
7. Klikněte na kartu **Atributy**.

8. Na **atributy** kartu, proveďte následující kroky:
    
    ![Atributy](./media/sap-hana-cloud-platform-tutorial/ic790804.png "atributy") 

    a. Klikněte na tlačítko **Add Assertion-Based atribut**a poté přidejte následující atributy na základě kontrolního výrazu:
       
    | Atribut kontrolního výrazu | Atribut instanční objekt |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |jméno |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Příjmení |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Konfigurace atributů závisí na tom, jak tyto aplikace na spojovací bod služby jsou vyvíjeny, to znamená, atributy, které očekávají odpověď SAML a v rámci které název (atribut instanční objekt) přistupují tento atribut v kódu.
     > 
    
    b. **Výchozí atribut** na snímku obrazovky je pouze pro ilustraci. Není potřeba provádět tento scénář fungovat.  
 
    c. Názvy a hodnoty pro **instanční objekt atributu** zobrazené na snímku obrazovky závisí na tom, jak se vyvíjí aplikace. Je možné, že vaše aplikace vyžaduje jiný mapování.

### <a name="assertion-based-groups"></a>Skupiny založené na kontrolní výraz

Jako volitelný krok můžete nakonfigurovat skupiny založené na kontrolní výraz pro zprostředkovatele Identity Azure Active Directory.

Používání skupin v cloudové platformy SAPU vám umožní dynamicky přiřadit jeden nebo více uživatelů k jedné nebo více rolím ve svých aplikacích cloudové platformy SAPU dáno hodnoty atributů v kontrolního výrazu SAML 2.0. 

Například, pokud výraz obsahuje atribut "*kontraktu = dočasné*", můžete chtít všechny ovlivněné uživatele přidat do skupiny"*dočasné*". Skupina "*dočasné*" může obsahovat jednu nebo více rolí z jedné nebo více aplikací, které jsou nasazené ve vašem účtu cloudové platformy SAPU.
 
Použijte skupiny založené na výrazu, když potřebujete mnoho uživatelů současně přiřadit jednu nebo více rolí aplikací ve vašem účtu cloudové platformy SAPU. Pokud chcete přiřadit specifické role pouze na jeden nebo malé počet uživatelů, doporučujeme přiřadíte přímo v "**povolení**" karty řídicí panel cloudové platformy SAPU.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do cloudové platformy SAPU.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **cloudové platformy SAPU**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **cloudové platformy SAPU**.

    ![Odkaz cloudové platformy SAPU v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-cloud-platform-test-user"></a>Vytvoření cloudové platformy SAPU testovacího uživatele

Chcete-li povolit Azure AD uživatelům přihlášení do cloudové platformy SAP, je třeba přiřadit role v cloudové platformy SAPU k nim.

**Přiřazení role pro uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **cloudové platformy SAPU** řídicí panel.

2. Proveďte následující kroky:
   
    ![Povolení](./media/sap-hana-cloud-platform-tutorial/ic790805.png "autorizace")
   
    a. Klikněte na tlačítko **autorizace**.

    b. Klikněte na tlačítko **uživatelé** kartu.

    c. V **uživatele** textového pole zadejte e-mailovou adresu uživatele.

    d. Klikněte na tlačítko **přiřadit** přiřadit uživatele k roli.

    e. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici cloudové platformy SAPU na přístupovém panelu, vám by měl být automaticky přihlášeni do cloudové platformy SAP, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

