---
title: 'Kurz: Azure Active Directory integrace s konzolou infrastruktura cloudu Oracle | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konzolou cloudové infrastruktury Oracle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "76289097"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Kurz: Integrujte konzolu cloudové infrastruktury Oracle pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat konzolu cloudové infrastruktury Oracle pomocí Azure Active Directory (Azure AD). Když integruje konzolu cloudové infrastruktury Oracle s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k konzole cloudové infrastruktury Oracle
* Umožněte uživatelům, aby se automaticky přihlásili ke konzole cloudové infrastruktury Oracle pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné nástroje Oracle Cloud infrastruktura konzoly pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konzola cloudové infrastruktury Oracle **podporuje jednotné** přihlašování.
* Po nakonfigurování konzoly cloudové infrastruktury Oracle můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Přidání konzoly cloudové infrastruktury Oracle z Galerie

Pokud chcete nakonfigurovat integraci konzoly cloudové infrastruktury Oracle do služby Azure AD, musíte přidat konzolu cloudové infrastruktury Oracle z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Oracle Cloud infrastruktura konzola** .
1. Vyberte z panelu výsledků položku **Konzola cloudová infrastruktura Oracle** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí konzoly pro cloudovou infrastrukturu Oracle pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v konzole pro cloudovou infrastrukturu Oracle.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konzoly cloudové infrastruktury Oracle, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Nakonfigurujte konzolu cloudové infrastruktury Oracle](#configure-oracle-cloud-infrastructure-console)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele konzoly pro Oracle Cloud Infrastructure](#create-oracle-cloud-infrastructure-console-test-user)** , abyste měli protějšek B. Simon v konzole Oracle Cloud infrastruktura, která je propojená s reprezentací uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **konzoly cloudová infrastruktura Oracle** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   > [!NOTE]
   > Soubor metadat poskytovatele služeb získáte v části **konfigurace pro jednotné přihlašování konzoly Oracle Cloud infrastruktura** v tomto kurzu.
    
   1. Klikněte na **nahrát soubor metadat**.

   1. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

   1. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi získají automaticky v **základním** textovém poli konfiguračního oddílu SAML.
    
      > [!NOTE]
      > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískají auto polulated, pak hodnoty ručně vyplníte podle vašich požadavků.

      Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory pro klienta podpory Oracle Cloud Infrastructure](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. Aplikace konzoly cloudové infrastruktury Oracle očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

   ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace konzoly cloudové infrastruktury Oracle u odpovědi SAML několik atributů, které se mají vrátit zpátky. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

   1. Klikněte na **pero** vedle **názvu hodnota identifikátoru**.

   1. Vyberte možnost **trvalá** jako **Zvolte formát identifikátoru názvu**.
 
   1. Klikněte na **Uložit**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

   1. V seznamu přepínačů vyberte **skupiny zabezpečení** .

   1. Vyberte **zdrojový atribut** **ID skupiny**.

   1. Ověřte **přizpůsobení názvu deklarace identity skupiny**.

   1. Do textového pole **název** zadejte příkaz **název_skupiny**.

   1. Do textového pole **obor názvů (volitelné)** zadejte `https://auth.oraclecloud.com/saml/claims`.

   1. Klikněte na **Uložit**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. V části **nastavení konzoly cloudové infrastruktury Oracle** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B. Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke konzole cloudové infrastruktury Oracle.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Oracle Cloud infrastruktura konzoly**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurace konzoly pro cloudovou infrastrukturu Oracle

1. V jiném okně webového prohlížeče se přihlaste ke konzole cloudové infrastruktury Oracle jako správce.

1. Klikněte na levou stranu nabídky, klikněte na **Identita** a pak přejděte na **federace**.

   ![Konfigurace](./media/oracle-cloud-tutorial/config01.png)

1. Uložte **soubor metadat poskytovatele služby** kliknutím na odkaz **stáhnout tento dokument** a nahrajte ho do části **základní konfigurace SAML** v Azure Portal a pak klikněte na **Přidat zprostředkovatele identity**.

   ![Konfigurace](./media/oracle-cloud-tutorial/config02.png)

1. V místní nabídce **Přidat zprostředkovatele identity** proveďte následující kroky:

   ![Konfigurace](./media/oracle-cloud-tutorial/config03.png)

   1. Do textového pole **název** zadejte své jméno.

   1. Do textového pole **Popis** zadejte svůj popis.

   1. Jako **typ**vyberte **poskytovatele identity služby AD FS (Active Directory Federation Service) nebo SAML 2,0** .

   1. Klikněte na tlačítko **Procházet** a nahrajte XML federačních metadat, které jste stáhli z Azure Portal.

   1. Klikněte na **pokračovat** a v části **Upravit zprostředkovatele identity** proveďte následující kroky:

      ![Konfigurace](./media/oracle-cloud-tutorial/config09.png)

   1. **Skupina zprostředkovatelů identity** by měla být vybraná jako vlastní skupina. ID skupiny by mělo být identifikátor GUID skupiny z Azure Active Directory. Skupina musí být namapována s odpovídající skupinou v poli **skupiny rozhraní OCI** .

   1. Můžete mapovat více skupin podle vašich nastavení v Azure Portal a potřebnou organizaci. Kliknutím na **+ Přidat mapování** přidejte libovolný počet skupin, kolik potřebujete.

   1. Klikněte na **Odeslat**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Vytvořit testovacího uživatele konzoly pro cloudovou infrastrukturu Oracle

 Konzola cloudové infrastruktury Oracle podporuje zřizování za běhu, což je ve výchozím nastavení. V této části není žádná položka akce. Nový uživatel se nevytvoří během pokusu o přístup a také není potřeba vytvořit uživatele.

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici konzoly cloudové infrastruktury Oracle na přístupovém panelu, budete přesměrováni na přihlašovací stránku ke konzole cloudové infrastruktury Oracle. V rozevírací nabídce vyberte **poskytovatele identity** a přihlaste se kliknutím na tlačítko **pokračovat** , jak je uvedeno níže. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfigurace](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Jak chránit konzolu cloudové infrastruktury Oracle s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
