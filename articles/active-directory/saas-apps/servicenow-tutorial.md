---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s ServiceNow | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a09cc83259a6fbb07b8c88e1d8f82b720400c0d1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507705"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ServiceNow

V tomto kurzu se dozvíte, jak integrovat ServiceNow s Azure Active Directory (Azure AD). Když integrujete ServiceNow s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ServiceNow.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ServiceNow svým účtům Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným ServiceNowm jednotným přihlašováním (SSO).
* Pro ServiceNow, instanci nebo tenanta ServiceNow, verze Calgary nebo novější.
* Pro ServiceNow Express je to instance ServiceNow Express, Helsinky verze nebo novější.
* Tenant ServiceNow musí mít povolený [modul plug-in jednotného přihlašování více zprostředkovatelů](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) . To můžete provést [odesláním žádosti o služby](https://hi.service-now.com).
* Pro automatickou konfiguraci povolte modul plug-in více zprostředkovatelů pro ServiceNow.
* Pokud chcete nainstalovat aplikaci ServiceNow Classic (Mobile), vyhledejte příslušné úložiště a vyhledejte aplikaci ServiceNow Classic. Pak ho stáhněte.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

* ServiceNow podporuje jednotné přihlašování iniciované v **SP** .

* ServiceNow podporuje [automatizované zřizování uživatelů](servicenow-provisioning-tutorial.md).

* Po nakonfigurování ServiceNow můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Pro povolení jednotného přihlašování můžete nakonfigurovat aplikaci ServiceNow Classic (mobilní) se službou Azure AD. Podporuje uživatele se systémem Android i iOS. V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

## <a name="add-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie

Pokud chcete nakonfigurovat integraci ServiceNow do služby Azure AD, musíte přidat ServiceNow z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace**a vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ServiceNow** .
1. Na panelu výsledků vyberte **ServiceNow** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ServiceNow

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ServiceNow pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ServiceNow.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ServiceNow, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
    1. [NAKONFIGURUJTE jednotné přihlašování Azure AD pro ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) , aby vaši uživatelé mohli používat tuto funkci.
2. [Nakonfigurujte ServiceNow](#configure-servicenow) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte ServiceNow testovacího uživatele](#create-servicenow-test-user) , který bude mít protějšek B. Simon v ServiceNow, který se odkazuje na reprezentaci uživatele v Azure AD.
    1. Nakonfigurujte jednotné přihlašování [ServiceNow Express](#configure-servicenow-express-sso) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.  
3. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.
4. [Otestujte jednotné přihlašování pro ServiceNow Classic (mobilní zařízení)](#test-sso-for-servicenow-classic-mobile) , abyste ověřili, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ServiceNow** Najděte oddíl **Manage** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou pera](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do pole **přihlašovací adresa URL**zadejte adresu URL, která používá následující vzor:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Do pole **identifikátor (ID entity)** zadejte adresu URL, která používá následující vzor:`https://<instance-name>.service-now.com`

    c. V poli **Adresa URL odpovědi**zadejte jednu z těchto adres URL:

    | Adresa URL odpovědi|
    |----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty musíte aktualizovat pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. 

   ![Snímek obrazovky oddílu podpisového certifikátu SAML se zvýrazněnou možností stažení](common/certificatebase64.png)

   a. Kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace**a vložte ji do poznámkového bloku. Tato adresa URL bude použita později v tomto kurzu.

    b. Vyberte **Stáhnout** a stáhněte **certifikát (Base64)** a pak soubor certifikátu uložte do počítače.

1. V části **Nastavení ServiceNow** zkopírujte podle vašich požadavků příslušné adresy URL.

   ![Snímek obrazovky s oddílem nastavení ServiceNow se zvýrazněnými adresami URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Jako **název**zadejte `B.Simon` .  
   1. Do pole **uživatelské jméno**zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ServiceNow.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **ServiceNow**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s oddílem spravovat se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek obrazovky uživatelů a skupin se zvýrazněnou možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak zvolte **možnost vybrat**.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte **Vybrat**.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurace jednotného přihlašování služby Azure AD pro ServiceNow Express

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ServiceNow** vyberte **jednotné přihlašování**.

    ![Snímek obrazovky se stránkou integrace aplikace ServiceNow se zvýrazněným jednotným přihlašováním](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování.

    ![Snímek obrazovky s výběrem metody jednotného přihlašování se zvýrazněným elementem SAML](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera, čímž otevřete dialogové okno **základní konfigurace SAML** .

    ![Snímek obrazovky nastavení jednotného přihlašování pomocí stránky SAML se zvýrazněnou ikonou pera](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. V poli **Adresa URL pro přihlášení**zadejte adresu URL, která používá následující vzor:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. V poli **identifikátor (ID entity)** zadejte adresu URL, která používá následující vzor:`https://<instance-name>.service-now.com`

    c. V poli **Adresa URL odpovědi**zadejte jednu z těchto adres URL:

    | Adresa URL odpovědi |
    |-----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty musíte aktualizovat pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si **certifikát (Base64)** ze zadaných možností dle vašeho požadavku. Uložte ho do svého počítače.

    ![Snímek obrazovky s oddílem podpisového certifikátu SAML se zvýrazněnou možností stažení](common/certificatebase64.png)

6. Službu Azure AD můžete mít automaticky nakonfigurovanou ServiceNow pro ověřování založené na SAML. Pokud chcete tuto službu povolit, v části **Nastavení ServiceNow** vyberte **Zobrazit podrobné pokyny** a otevřete okno **Konfigurovat přihlašování** .

    ![Snímek obrazovky s oddílem nastavení ServiceNow se zvýrazněnými podrobnými pokyny zobrazení](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Ve formuláři **Konfigurace přihlášení** zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce. Vyberte **Konfigurovat nyní**. Aby tato funkce fungovala, musí mít zadané uživatelské jméno správce přiřazenou roli **SECURITY_ADMIN** v ServiceNow. Jinak, pokud chcete ServiceNow nakonfigurovat, aby používala Azure AD jako poskytovatele identity SAML, vyberte **ručně konfigurovat jednotné přihlašování**. Zkopírujte **adresu URL pro odhlášení, identifikátor Azure AD a přihlašovací adresu URL** z oddílu rychlé reference.

    ![Snímek obrazovky s konfigurací přihlašovacího formuláře se zvýrazněnou možností konfigurovat](./media/servicenow-tutorial/configure.png "Konfigurace adresy URL aplikace")

## <a name="configure-servicenow"></a>Konfigurace ServiceNow

1. Přihlaste se k aplikaci ServiceNow jako správce.

1. Aktivujte integraci – modul plug-in **instalačního programu jednotného přihlašování s více poskytovateli** pomocí následujících kroků:

    a. V levém podokně vyhledejte v poli hledání oddíl **definice systému** a pak vyberte **moduly plug-in**.

    ![Snímek obrazovky oddílu definice systému s zvýrazněnou definicí systému a moduly plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivovat modul plug-in")

    b. Vyhledat **integraci – instalační program jednotného přihlašování s více poskytovateli**

     ![Snímek obrazovky systémových modulů plug-in se zvýrazněnou možností instalace jednotného přihlašování s více poskytovateli](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktivovat modul plug-in")

    c. Vyberte modul plug-in. Klikněte pravým tlačítkem a vyberte **aktivovat nebo upgradovat**.

     ![Snímek obrazovky se kliknutím pravým tlačítkem myši, se zvýrazněnou možností aktivovat nebo upgradovat](./media/servicenow-tutorial/tutorial_activate.png "Aktivovat modul plug-in")

    d. Vyberte **aktivovat**.

     ![Snímek obrazovky dialogového okna aktivovat modul plug-in se zvýrazněnou možností aktivovat](./media/servicenow-tutorial/tutorial_activate1.png "Aktivovat modul plug-in")

1. V levém podokně vyhledejte v panelu hledání oddíl **jednotného přihlašování k více zprostředkovatelům** a pak vyberte **vlastnosti**.

    ![Snímek obrazovky oddílu jednotného přihlašování s více zprostředkovateli a zvýrazněné jednotné přihlašování a vlastnosti](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurace adresy URL aplikace")

1. V dialogovém okně **vlastnosti jednotného přihlašování více zprostředkovatelů** proveďte následující kroky:

    ![Snímek obrazovky dialogového okna vlastnosti jednotného přihlašování pro více zprostředkovatelů](./media/servicenow-tutorial/ic7694981.png "Konfigurace adresy URL aplikace")

    * Pokud chcete **Povolit vícenásobné přihlašování více zprostředkovatelů**, vyberte **Ano**.
  
    * Pokud chcete **Povolit automatický import uživatelů ze všech zprostředkovatelů identity do uživatelské tabulky**, vyberte **Ano**.

    * Pokud chcete **Povolit protokolování ladění pro integraci více zprostředkovatelů**, vyberte **Ano**.

    * Pro **pole v tabulce uživatel, které je...**, zadejte **e-mail**.
  
    * Vyberte **Uložit**.

1. ServiceNow můžete nakonfigurovat automaticky nebo ručně. K automatické konfiguraci ServiceNow použijte následující postup:

    1. Vraťte se na stránku jednotného přihlašování **ServiceNow** v Azure Portal.

    1. Služba pro konfiguraci jedním kliknutím je k dispozici pro ServiceNow. Tuto službu povolíte tak, že přejdete do **konfiguračního oddílu ServiceNow** a vyberete **Konfigurovat ServiceNow** . otevře se okno **Konfigurovat přihlašování** .

        ![Snímek obrazovky s nastavením ServiceNow a zvýrazněnými podrobnými pokyny pro zobrazení](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. Ve formuláři **Konfigurace přihlášení** zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce. Vyberte **Konfigurovat nyní**. Aby tato funkce fungovala, musí mít zadané uživatelské jméno správce přiřazenou roli **SECURITY_ADMIN** v ServiceNow. Jinak, pokud chcete ServiceNow nakonfigurovat, aby používala Azure AD jako poskytovatele identity SAML, vyberte **ručně konfigurovat jednotné přihlašování**. Zkopírujte adresu **URL pro odhlášení, ID entity SAML a adresu URL služby jednotného přihlašování SAML** z oddílu rychlé reference.

        ![Snímek obrazovky s konfigurací přihlašovacího formuláře se zvýrazněnou možností konfigurovat](./media/servicenow-tutorial/configure.png "Konfigurace adresy URL aplikace")

    1. Přihlaste se k aplikaci ServiceNow jako správce.

       * V rámci automatické konfigurace jsou všechna potřebná nastavení nakonfigurovaná na straně **ServiceNow** , ale **certifikát X. 509** není ve výchozím nastavení povolený. Musíte ho ručně namapovat na svého poskytovatele identity v ServiceNow. Postupujte takto:

         1. V levém podokně vyhledejte v poli hledání oddíl **jednotného přihlašování k více poskytovatelům** a vyberte **Zprostředkovatelé identity**.

            ![Snímek obrazovky s oddílem jednotného přihlašování s více zprostředkovateli se zvýrazněnými poskytovateli identity](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurace jednotného přihlašování")

         1. Vyberte automaticky generovaný zprostředkovatel identity.

            ![Snímek pro zprostředkovatele identity se zvýrazněným automaticky generovaným poskytovatelem identity](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurace jednotného přihlašování")

         1.  V části **zprostředkovatel identity** proveďte následující kroky:

             ![Snímek obrazovky oddílu zprostředkovatele identity](./media/servicenow-tutorial/automatic_config.png "Konfigurace jednotného přihlašování")

               * Jako **název**zadejte název vaší konfigurace (například **Microsoft Azure federované jednotné přihlašování**).

               * Z textového pole odeberte hodnotu SingleLogoutRequest vyplněné **poskytovatele identity** .

               * Zkopírujte hodnotu **domovské stránky ServiceNow** a vložte ji do **přihlašovací adresy URL** v části **ServiceNow Basic SAML konfigurace** Azure Portal.

                  > [!NOTE]
                  > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do` ).

              * Zkopírujte hodnotu **ID nebo vystavitele entity** a vložte **ji do části** **ServiceNow Basic konfigurace SAML** v Azure Portal.

              * Potvrďte, že **zásada NameId** je nastavená na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu. 

         1. Přejděte dolů k části **certifikát X. 509** a vyberte **Upravit**.

             ![Snímek obrazovky oddílu certifikátu X. 509 se zvýrazněnou možností upravit](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurace jednotného přihlašování")

         1. Vyberte certifikát a kliknutím na ikonu se šipkou vpravo přidejte certifikát.

            ![Snímek obrazovky kolekce se zvýrazněnou ikonou pro ikonu certifikátu a pravé šipky](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurace jednotného přihlašování")

          1. Vyberte **Uložit**.

          1. V pravém horním rohu stránky vyberte **Test připojení**.

             ![Snímek stránky se zvýrazněným testovým připojením](./media/servicenow-tutorial/tutorial_activate2.png "Aktivovat modul plug-in")

             > [!NOTE]
             > Pokud test připojení selhává a nebudete moct aktivovat toto připojení, pak ServiceNow nabídne přepínač override. Je nutné zadat **Sys_properties. Vypíšete** stránku s **navigací hledání** a otevře se nová stránka vlastností systému. Tady je nutné vytvořit novou vlastnost s názvem, jako je třeba považovat **. Authenticate. multisso. test. Connection. povinné** s **datovým typem** **true/false** a nastavte **hodnotu** na **false**.

             > ![Obrazovka stránky Výsledky testů](./media/servicenow-tutorial/testconnection-fail.png "Konfigurace jednotného přihlašování")
        
          1. Po zobrazení výzvy k zadání přihlašovacích údajů je zadejte. Zobrazí se následující stránka. Očekává se chyba **výsledky testů odhlášení SSO** . Ignorujte chybu a vyberte **aktivovat**.

             ![Obrazovka stránky Výsledky testů](./media/servicenow-tutorial/servicenowactivate.png "Konfigurace jednotného přihlašování")
  
1. Pokud chcete nakonfigurovat **ServiceNow** ručně, postupujte takto:

    1. Přihlaste se k aplikaci ServiceNow jako správce.

    1. V levém podokně vyberte **Zprostředkovatelé identity**.

        ![Snímek obrazovky jednotného přihlašování s více zprostředkovateli se zvýrazněnými poskytovateli identity](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurace jednotného přihlašování")

    1. V dialogovém okně **Zprostředkovatelé identity** vyberte možnost **Nový**.

        ![Snímek obrazovky dialogového okna poskytovatelé identity s novým zvýrazněným](./media/servicenow-tutorial/ic7694977.png "Konfigurace jednotného přihlašování")

    1. V dialogovém okně **Zprostředkovatelé identity** vyberte **SAML**.

        ![Snímek obrazovky dialogového okna poskytovatelé identity se zvýrazněným SAML](./media/servicenow-tutorial/ic7694978.png "Konfigurace jednotného přihlašování")

    1. V části **importovat metadata zprostředkovatele identity**proveďte následující kroky:

        ![Snímek obrazovky import metadat zprostředkovatele identity se zvýrazněnou adresou URL a importem](./media/servicenow-tutorial/idp.png "Konfigurace jednotného přihlašování")

        1. Zadejte **adresu URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

        1. Vyberte **Importovat**.

    1. Načte adresu URL metadat IdP a vyplní všechny informace o polích.

        ![Snímek obrazovky poskytovatele identity](./media/servicenow-tutorial/ic7694982.png "Konfigurace jednotného přihlašování")

        * Jako **název**zadejte název vaší konfigurace (například **Microsoft Azure federované jednotné přihlašování**).

        * Z textového pole odeberte vyplněnou hodnotu **SingleLogoutRequest poskytovatele identity** .

        * Zkopírujte hodnotu **domovské stránky ServiceNow** . Vložte ho do **přihlašovací adresy URL** v části **ServiceNow Basic SAML konfigurace** Azure Portal.

            > [!NOTE]
            > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do` ).

        * Zkopírujte hodnotu **ID nebo vystavitele entity** . Vloží ho do **identifikátoru** v části **SERVICENOW Basic konfigurace SAML** v Azure Portal.

        * Potvrďte, že **zásada NameId** je nastavená na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu.

        * Vyberte **Upřesnit**. Do **pole uživatelské pole**zadejte **e-mail**.

            > [!NOTE]
            > Službu Azure AD můžete nakonfigurovat tak, aby vygenerovala buď ID uživatele Azure AD (hlavní název uživatele), nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML. Provedete to tak, **ServiceNow**že v Azure Portal zadáte v  >  **Attributes**  >  části**jednotné přihlašování** atributy ServiceNow a namapujete požadované pole na atribut **NameIdentifier** . Hodnota uložená pro vybraný atribut ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například user_name).

        * V pravém horním rohu stránky vyberte **Test připojení** .

          > [!NOTE]
          > Pokud test připojení selhává a nebudete moct aktivovat toto připojení, pak ServiceNow nabídne přepínač override. Je nutné zadat **Sys_properties. Vypíšete** stránku s **navigací hledání** a otevře se nová stránka vlastností systému. Tady je nutné vytvořit novou vlastnost s názvem, jako je třeba považovat **. Authenticate. multisso. test. Connection. povinné** s **datovým typem** **true/false** a nastavte **hodnotu** na **false**.

          > ![Obrazovka stránky Výsledky testů](./media/servicenow-tutorial/testconnection-fail.png "Konfigurace jednotného přihlašování")

        * Po zobrazení výzvy k zadání přihlašovacích údajů je zadejte. Zobrazí se následující stránka. Očekává se chyba **výsledky testů odhlášení SSO** . Ignorujte chybu a vyberte **aktivovat**.

          ![Obrazovka stránky Výsledky testů](./media/servicenow-tutorial/servicenowactivate.png "Konfigurace jednotného přihlašování")

### <a name="create-servicenow-test-user"></a>Vytvořit testovacího uživatele ServiceNow

Cílem této části je vytvořit uživatele s názvem B. Simon v ServiceNow. ServiceNow podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurace jednotného přihlašování ServiceNow Express

1. Přihlaste se k aplikaci ServiceNow Express jako správce.

2. V levém podokně vyberte **jednotné přihlašování**.

    ![Snímek obrazovky aplikace ServiceNow Express se zvýrazněným jednotným přihlašováním](./media/servicenow-tutorial/ic7694980ex.png "Konfigurace adresy URL aplikace")

3. V dialogovém okně **jednotné přihlašování** vyberte ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Snímek obrazovky s dialogovým oknem jednotného přihlašování](./media/servicenow-tutorial/ic7694981ex.png "Konfigurace adresy URL aplikace")

    a. Přepněte na právo **Povolit jednotné přihlašování pro více zprostředkovatelů** .

    b. Přepněte **možnost povolit protokolování ladění pro integraci více zprostředkovatelů jednotného PŘIhlašování** napravo.

    c. Do **pole v tabulce uživatel**zadejte **user_name**.

4. V dialogovém okně **jednotné přihlašování** vyberte **Přidat nový certifikát**.

    ![Snímek obrazovky dialogového okna jednotného přihlašování s zvýrazněným přidáním nového certifikátu](./media/servicenow-tutorial/ic7694973ex.png "Konfigurace jednotného přihlašování")

5. V dialogovém okně **certifikáty X. 509** proveďte následující kroky:

    ![Snímek obrazovky s dialogovým oknem Certifikáty X. 509](./media/servicenow-tutorial/ic7694975.png "Konfigurace jednotného přihlašování")

    a. Jako **název**zadejte název vaší konfigurace (například: **testsaml 2.0**).

    b. Vyberte **aktivní**.

    c. Jako **Formát**vyberte **PEM**.

    d. Jako **typ**vyberte **certifikát důvěryhodného úložiště**.

    e. Otevřete certifikát kódovaný ve formátu base64 stažený z Azure Portal v programu Poznámkový blok. Zkopírujte obsah této složky do schránky a vložte ji do textového pole **certifikát PEM** .

    f. Vybrat **aktualizaci**

6. V dialogovém okně **jednotné přihlašování** vyberte **Přidat nový IDP**.

    ![Snímek obrazovky dialogového okna jednotného přihlašování se zvýrazněnou možností přidat nový IdP](./media/servicenow-tutorial/ic7694976ex.png "Konfigurace jednotného přihlašování")

7. V dialogovém okně **Přidat nového zprostředkovatele identity** v části **Konfigurovat zprostředkovatele identity**proveďte následující kroky:

    ![Snímek obrazovky dialogového okna Přidat nového zprostředkovatele identity](./media/servicenow-tutorial/ic7694982ex.png "Konfigurace jednotného přihlašování")

    a. Jako **název**zadejte název vaší konfigurace (například **SAML 2,0**).

    b. Do pole **Adresa URL poskytovatele identity**vložte hodnotu ID zprostředkovatele identity, kterou jste zkopírovali z Azure Portal.

    c. Do **AuthnRequest poskytovatele identity**vložte hodnotu adresy URL žádosti o ověření, kterou jste zkopírovali z Azure Portal.

    d. V případě **SingleLogoutRequest poskytovatele identity**vložte hodnotu adresy URL pro odhlášení, kterou jste zkopírovali z Azure Portal.

    e. V části **certifikát poskytovatele identity**vyberte certifikát, který jste vytvořili v předchozím kroku.

8. Vyberte **Upřesnit nastavení**. V části **Další vlastnosti zprostředkovatele identity**proveďte následující kroky:

    ![Snímek obrazovky dialogového okna Přidat nového zprostředkovatele identity s zvýrazněnou možností Upřesnit nastavení](./media/servicenow-tutorial/ic7694983ex.png "Konfigurace jednotného přihlašování")

    a. Pro **vazbu protokolu pro SINGLELOGOUTREQUEST IDP**zadejte **název urn: Oasis: names: TC: SAML: 2.0: vazby: http-redirect**.

    b. V případě **zásad NameId**zadejte **název urn: Oasis: names: TC: SAML: 1.1: NameId-Format: unspecifikovan**.

    c. Pro **metodu AuthnContextClassRef**zadejte `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` .

    d. Pro **Vytvoření AuthnContextClass**ho přepněte na vypnuto (Nevybráno).

9. V části **Další vlastnosti poskytovatele služeb**proveďte následující kroky:

    ![Snímek obrazovky dialogového okna Přidat nového zprostředkovatele identity se zvýrazněnými různými vlastnostmi](./media/servicenow-tutorial/ic7694984ex.png "Konfigurace jednotného přihlašování")

    a. Pro **domovskou stránku ServiceNow**zadejte adresu URL domovské stránky instance ServiceNow.

    > [!NOTE]
    > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do` ).

    b. Jako **ID entity/vystavitele**zadejte adresu URL vašeho tenanta ServiceNow.

    c. Jako **identifikátor URI cílové skupiny**zadejte adresu URL vašeho tenanta ServiceNow.

    d. Jako **časový posun**zadejte **60**.

    e. Do **pole uživatelské pole**zadejte **e-mail**.

    > [!NOTE]
    > Službu Azure AD můžete nakonfigurovat tak, aby vygenerovala buď ID uživatele Azure AD (hlavní název uživatele), nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML. Provedete to tak, **ServiceNow**že v Azure Portal zadáte v  >  **Attributes**  >  části**jednotné přihlašování** atributy ServiceNow a namapujete požadované pole na atribut **NameIdentifier** . Hodnota uložená pro vybraný atribut ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například user_name).

    f. Vyberte **Uložit**.

## <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici ServiceNow, měli byste se automaticky přihlásit k ServiceNow, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Test jednotného přihlašování pro ServiceNow Classic (mobilní zařízení)

1. Otevřete **klasickou aplikaci v ServiceNow (Mobile)** a proveďte následující kroky:

    a. V pravém dolním rohu vyberte znaménko plus.

    ![Snímek obrazovky klasické aplikace ServiceNow se zvýrazněným znaménkem plus](./media/servicenow-tutorial/test03.png)

    b. Zadejte název instance ServiceNow a vyberte **pokračovat**.

    ![Snímek stránky přidat instanci se zvýrazněnou možností pokračovat](./media/servicenow-tutorial/test04.png)

    c. Na **přihlašovací** stránce proveďte následující kroky:

    ![Snímek obrazovky s přihlašovací stránkou s zvýrazněnou možností použít externí přihlášení](./media/servicenow-tutorial/test01.png)

    *  Zadejte **uživatelské jméno**, například B.simon@contoso.com .

    *  Vyberte **použít externí přihlášení**. Budete přesměrováni na stránku služby Azure AD, abyste se přihlásili.

    *  Zadejte svoje přihlašovací údaje. Pokud je k dispozici jakékoli ověřování třetí strany nebo jakákoli jiná funkce zabezpečení, musí uživatel odpovídajícím způsobem odpovědět. Zobrazí se **Domovská stránka** aplikace.

        ![Snímek obrazovky domovské stránky aplikace](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](servicenow-provisioning-tutorial.md)

- [Vyzkoušejte si ServiceNow s Azure AD](https://aad.portal.azure.com)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Jak chránit ServiceNow pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
