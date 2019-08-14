---
title: 'Kurz: Kurz: Azure Active Directory integrace jednotného přihlašování s SAP NetWeaver | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b002a9d5385d6cee3f22da7a1ddcf1f0864311ec
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989038"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Kurz: Azure Active Directory integrace jednotného přihlašování pomocí SAP NetWeaver

V tomto kurzu se dozvíte, jak integrovat SAP NetWeaver s Azure Active Directory (Azure AD). Když integrujete SAP NetWeaver s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SAP NetWeaver.
* Umožněte uživatelům, aby se do SAP NetWeaver automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP NetWeaver s jednotným přihlašováním (SSO).
* NetWeaver SAP 7.20 V vyžaduje minimálně

## <a name="scenario-description"></a>Popis scénáře

SAP NetWeaver podporuje protokol **SAML** (**SSO iniciovaná SP**) i **OAuth**. V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

> [!NOTE]
> Nakonfigurujte aplikaci buď v SAML, nebo v OAuth podle požadavků vaší organizace. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Přidání SAP NetWeaver z Galerie

Pokud chcete nakonfigurovat integraci SAP NetWeaver do služby Azure AD, musíte přidat SAP NetWeaver z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAP NetWeaver** .
1. Z panelu výsledků vyberte **SAP NetWeaver** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SAP NetWeaver

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP NetWeaver pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP NetWeaver.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP NetWeaver, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[NAKONFIGURUJTE SAP NetWeaver pomocí SAML](#configure-sap-netweaver-using-saml)** ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele SAP NetWeaver](#create-sap-netweaver-test-user)** , aby měl protějšek B. Simon v SAP NetWeaveru, která je propojená s reprezentací uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.
1. **[NAKONFIGURUJTE SAP NetWeaver pro OAuth](#configure-sap-netweaver-for-oauth)** pro konfiguraci nastavení OAuth na straně aplikace.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SAP NetWeaver, proveďte následující kroky:

1. Otevřete nové okno webového prohlížeče a přihlaste se k firemnímu webu SAP NetWeaver jako správce.

1. Ujistěte se, že jsou služby **http** a **https** aktivní a že se k nim přiřadí příslušné porty **SMICM** T-Code.

1. Přihlaste se k firemnímu klientovi se systémem SAP (T01), kde je vyžadováno jednotné přihlašování a aktivujte správu relace zabezpečení HTTP.

    a. Přejít na kód transakce **SICF_SESSIONS**. Zobrazí všechny relevantní parametry profilu s aktuálními hodnotami. Vypadají jako v následujícím příkladu:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Upravte výše uvedené parametry podle požadavků vaší organizace. výše uvedené parametry jsou uvedeny pouze jako indikace.

    b. V případě potřeby upravte parametry v části instance/výchozí profil systému SAP a restartujte systém SAP.

    c. Dvojím kliknutím na relevantního klienta povolte relaci zabezpečení protokolu HTTP.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivovat pod SICF službami:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Přejít na kód transakce **typu Saml2** v obchodním klientovi systému SAP [T01/122]. Otevře se uživatelské rozhraní v prohlížeči. V tomto příkladu jsme předpokládali 122 jako obchodní klient SAP.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Zadejte uživatelské jméno a heslo, které chcete zadat v uživatelském rozhraní, a klikněte na **Upravit**.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Nahraďte **název poskytovatele** z T01122 `http://T01122` na a klikněte na **Uložit**.

    > [!NOTE]
    > Ve výchozím nastavení se název poskytovatele `<sid><client>` používá jako formát `<protocol>://<name>`, ale Azure AD očekává název ve formátu. doporučuje `https://<sid><client>` se zachovat název poskytovatele, aby bylo možné nakonfigurovat více modulů SAP NetWeaver ABAP v Azure AD.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generují se metadata poskytovatele služby**: – Jakmile se dokončí konfigurace nastavení **místní zprostředkovatel** a **důvěryhodní zprostředkovatelé** na uživatelském rozhraní SAML 2,0, bude dalším krokem vygenerování souboru metadat poskytovatele služeb (který by obsahovalo všechna nastavení, kontexty ověřování a další konfigurace v SAP. Po vygenerování tohoto souboru je potřeba ho nahrát do Azure AD.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Přejít na **kartu Místní poskytovatel**.

    b. Klikněte na **metadata**.

    c. Uložte **soubor XML** generovaných metadat do vašeho počítače a nahrajte ho **do základního konfiguračního oddílu SAML** , abyste mohli automaticky vyplnit hodnoty **identifikátoru** **adresy URL pro odpovědi** v Azure Portal.

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SAP NetWeaver** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    a. Klikněte na **nahrát soubor metadat** a nahrajte **soubor metadat poskytovatele služeb**, který jste získali dříve.

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** identifikátoru a odpovědi získají automaticky v **základním** textovém poli konfiguračního oddílu SAML, jak je znázorněno níže:

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Zjistili jsme, že někteří zákazníci hlásí chybu nesprávné adresy URL odpovědi nakonfigurované pro jejich instanci. Pokud se zobrazí nějaká chybová zpráva, můžete k nastavení správné adresy URL odpovědi pro vaši instanci použít následující skript PowerShellu jako pracovní.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ID objektu ServicePrincipal je třeba nastavit sami sami nebo ho můžete předat také zde.

1. Aplikace SAP NetWeaver očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

1. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    a. Kliknutím na **ikonu Upravit** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. V seznamu **transformace** vyberte **ExtractMailPrefix ()** .

    c. V seznamu **parametr 1** vyberte **User. userprinicipalname**.

    d. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavení SAP NetWeaver** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
    1. Do pole **Název** zadejte `B.Simon`.  
    1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
    1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SAP NetWeaver.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP NetWeaver**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurace SAP NetWeaver pomocí SAML

1. Přihlaste se k systému SAP a použijte typu Saml2 kód transakce. Otevře se nové okno prohlížeče s obrazovkou konfigurace SAML.

2. Konfigurace koncových bodů pro důvěryhodného zprostředkovatele identity (Azure AD) přejít na kartu **důvěryhodní zprostředkovatelé** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Stiskněte **Přidat** a v místní nabídce vyberte **nahrát soubor metadat** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Odešlete soubor metadat, který jste stáhli z Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na další obrazovce zadejte název aliasu. Například aadsts a pokračujte stisknutím klávesy **Next** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Ujistěte se, že **algoritmus Digest** by měl být **SHA-256** a nevyžaduje žádné změny, a stiskněte tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. U **koncových bodů jednotného přihlašování**použijte **http post** a pokračujte kliknutím na **Další** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. V případě **jednoho koncového** bodu odhlášení vyberte **HttpRedirect** a pokračujte kliknutím na **Další** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. V **koncových bodech artefaktu**pokračujte stisknutím klávesy **Next** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. V nabídce **požadavky na ověřování**klikněte na **Dokončit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Přejít na kartu**ID federace** **důvěryhodných zprostředkovatelů** > (z dolní části obrazovky). Klikněte na **Upravit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Na kartě **federace identit** (dolní okno) klikněte na **Přidat** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. V místním okně vyberte Neurčeno z **podporovaných formátů NameId** a klikněte na OK.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Všimněte si, že hodnoty **zdrojového ID** a **mapování ID** uživatele URČUJÍ propojení mezi uživatelem SAP a deklarací Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scénář: Mapování uživatele SAP k Azure AD.

    a. NameID Podrobnosti obrazovky z SAP.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Snímek obrazovky, který zmiňuje požadované deklarace identity z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scénář: Na základě konfigurované e-mailové adresy v SU01 vyberte SAP User ID. V tomto případě by se mělo v su01 nakonfigurovat ID e-mailu pro každého uživatele, který vyžaduje jednotné přihlašování.

    a.  NameID Podrobnosti obrazovky z SAP.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. snímek obrazovky, který zmiňuje požadované deklarace identity z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klikněte na **Uložit** a pak kliknutím na **Povolit** povolte poskytovatele identity.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration1.png)

16. Po zobrazení výzvy klikněte na **OK** .

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Vytvořit testovacího uživatele SAP NetWeaver

    V této části vytvoříte uživatele s názvem B. Simon v SAP NetWeaver. Pokud chcete přidat uživatele na platformě SAP NetWeaver, obraťte se na svého firemního odborného týmu SAP nebo pracujte s partnerem SAP vaší organizace.

## <a name="test-sso"></a>Test SSO

1. Po aktivaci poskytovatele identity Azure AD se pokuste získat přístup k ověření jednotného přihlašování pod adresou URL (uživatelské jméno & heslo se nezobrazí.)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (nebo) použijte níže uvedenou adresu URL.

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Nahraďte sapurl skutečným názvem hostitele SAP.

2. Výše uvedená adresa URL by vám měla přebrat pod uvedenou obrazovkou. Pokud máte přístup k této stránce, instalace služby Jednotné přihlašování k Azure AD se úspěšně dokončila.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/testingsso.png)

3. Pokud se zobrazí výzva k zadání uživatelského jména & hesla, Diagnostikujte prosím problém povolením trasování pod adresou URL.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurace SAP NetWeaver pro OAuth

1. Dokumentovaný proces SAP je k dispozici v tomto umístění: [NetWeaver, povolování a vytváření oborů OAuth 2,0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Přejít na SPRO a najít **služby Aktivace a údržba**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth01.png)

3. V tomto příkladu chceme připojit službu OData: `DAAG_MNGGRP` s OAuth pro jednotné přihlašování Azure AD. Pro službu `DAAG_MNGGRP` použijte hledání názvu technické služby a v případě, že ještě není aktivní (podívejte se na `green` kartu uzly pro bránu firewall), se už neaktivuje. Zajistěte, aby byl systémový alias (připojený back-end systém, kde je služba skutečně spuštěná) správný.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth02.png)

    * Pak na horním panelu tlačítek klikněte na (pushbutton) **OAuth** a `scope` přiřaďte (nechejte výchozí název nabídnutý).

4. V našem příkladu je rozsah `DAAG_MNGGRP_001`generovaný z názvu služby tím, že automaticky přidá číslo. Pomocí `/IWFND/R_OAUTH_SCOPES` sestavy lze změnit název oboru nebo vytvořit ručně.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Zpráva `soft state status is not supported` – může být ignorována, nejedná se o problém. Další podrobnosti najdete [tady](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true) .

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Vytvoření uživatele služby pro klienta OAuth 2,0

1. OAuth2 používá `service ID` k získání přístupového tokenu pro koncového uživatele jménem uživatele. Důležité omezení podle návrhu OAuth: `OAuth 2.0 Client ID` musí být stejná `username` jako u klienta OAuth 2,0, který se při vyžádání přístupového tokenu používá pro přihlášení. Proto budeme pro náš příklad registrovat klienta OAuth 2,0 s názvem KLIENT1 a jako předpoklad, že v systému SAP musí existovat uživatel se stejným názvem (KLIENT1) a že se tento uživatel bude konfigurovat, aby ho použila odkazovaná aplikace. 

2. Při registraci klienta OAuth použijeme `SAML Bearer Grant type`.

    >[!NOTE]
    >Další podrobnosti najdete [tady](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type) . registrace klienta OAuth 2,0 pro typ udělení NOSIČe SAML

3. tcod: SU01/vytvořte uživatele KLIENT1 `System type` a přiřaďte heslo, uložte ho jako potřebu k poskytnutí přihlašovacích údajů programátoru rozhraní API, který by se měl zapsat s uživatelským jménem na volající kód. Neměl by být přiřazen žádný profil nebo role.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrace nového ID klienta OAuth 2,0 pomocí Průvodce vytvořením

1. Registrace nového **klienta OAuth 2,0** spuštění transakce **SOAUTH2**. Transakce zobrazí přehled o klientech OAuth 2,0, které už jsou zaregistrované. Zvolením možnosti **vytvořit** spusťte Průvodce pro nového klienta OAuth s názvem jako CLIENT1in v tomto příkladu.

2. Přejít na T-Code: **SOAUTH2** a zadejte popis a pak klikněte na **Další**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth04.png)

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth05.png)

3. V rozevíracím seznamu vyberte už přidané **typu Saml2 IDP – Azure AD** a uložte ho.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth06.png)

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth07.png)

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth08.png)

4. Kliknutím na **Přidat** v části přiřazení oboru přidejte dřív vytvořený rozsah:`DAAG_MNGGRP_001`

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth09.png)

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/oauth10.png)

5. Klikněte na tlačítko **Dokončit**.

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte SAP NetWeaver s Azure AD](https://aad.portal.azure.com/)