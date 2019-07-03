---
title: 'Kurz: Integrace s Azure Active Directory pomocí Amazon Web Services (AWS) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f897653442a3e1b2d6098b3be60c85e75ca54f9a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551479"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory Amazon Web Services (AWS)

V tomto kurzu se dozvíte, jak integrovat Amazon Web Services (AWS) pomocí služby Azure Active Directory (Azure AD). Když integrujete Amazon Web Services (AWS) pomocí služby Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Amazon Web Services (AWS).
* Aby uživatelé mohli být automaticky přihlášeni k Amazon Web Services (AWS) pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)?](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Jak je uvedeno níže, můžete nakonfigurovat více identifikátorů pro víc instancí.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

S těmito hodnotami, Azure AD odeberete hodnotu **#** a odeslat správnou hodnotu `https://signin.aws.amazon.com/saml` jako adresu URL cílové skupiny v tokenu SAML.

**Doporučujeme použít tento přístup z následujících důvodů:**

a. Každá aplikace poskytne jedinečný X509 certifikátu. Každá instance instance AWS aplikace pak může mít jiný certifikát datum vypršení platnosti, které lze spravovat jednotlivě účtu AWS. Celkové výměny certifikátu bude snazší v tomto případě.

b. Můžete povolit zřizování uživatelů s AWS aplikací ve službě Azure AD a pak naše služba načte všechny role z daného účtu AWS. Není nutné ručně přidat nebo aktualizovat AWS role v aplikaci.

c. Můžete přiřadit vlastníka aplikace jednotlivě pro aplikace, který můžete spravovat aplikaci přímo ve službě Azure AD.

> [!Note]
> Ujistěte se, že používáte pouze aplikaci z Galerie

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Amazon Web Services (AWS) jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Amazon Web Services (AWS) podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Přidání Amazon Web Services (AWS) z Galerie

Ke konfiguraci integrace služby Amazon Web Services (AWS) do služby Azure AD, budete muset přidat Amazon Web Services (AWS) do seznamu spravovaných aplikací SaaS z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Amazon Web Services (AWS)** do vyhledávacího pole.
1. Vyberte **Amazon Web Services (AWS)** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí Amazon Web Services (AWS) pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské Amazon Web Services (AWS).

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí Amazon Web Services (AWS), proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace služby Amazon Web Services (AWS)](#configure-amazon-web-services-aws)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)**  mít protějšek B.Simon v Amazon Web Services (AWS), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Amazon Web Services (AWS)** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části aplikace je předem nakonfigurovaný a potřebné adresy URL se už předem vyplní s Azure. Uživatel musí uložte konfiguraci kliknutím **Uložit** tlačítko.

5. Pokud konfigurujete víc než jednu instanci, zadejte prosím hodnotu identifikátoru. Z druhé instance a vyšší Zadejte prosím hodnotu identifikátoru v následujícím formátu. Použijte prosím **#** přihlašovat se k zadejte jedinečnou hodnotu hlavního názvu služby.

    `https://signin.aws.amazon.com/saml#2`

6. Amazon Web Services (AWS) aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

7. Kromě toho výše Amazon Web Services (AWS) aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název  | Zdrojový atribut  | Obor názvů |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "zadejte hodnotu mezi 900 sekundách (15 minutách) na 43200 sekund (12 hodin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. V **Namespace** textového pole zadejte hodnotu Namespace zobrazený pro tento řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **kód XML metadat federace** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. Na **nastavit až Amazon Web Services (AWS)** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Konfigurace služby Amazon Web Services (AWS)

1. V jiném okně prohlížeče přihlašování k webu společnosti Amazon Web Services (AWS) jako správce.

2. Klikněte na tlačítko **AWS domovské**.

    ![Konfigurovat Single Sign-On Domovská stránka][11]

3. Klikněte na tlačítko **správu identit a přístupu**.

    ![Konfigurace Identity jednotné přihlašování][12]

4. Klikněte na tlačítko **zprostředkovatelé Identity**a potom klikněte na tlačítko **vytvořit poskytovatele**.

    ![Konfigurace zprostředkovatele přihlášení][13]

5. Na **konfigurovat zprostředkovatele** dialogového okna stránky, proveďte následující kroky:

    ![Konfigurovat Single Sign-On dialogového okna][14]

    a. Jako **typ zprostředkovatele**vyberte **SAML**.

    b. V **název zprostředkovatele** textového pole zadejte název zprostředkovatele (například: *WAAD*).

    c. K odeslání vaší stažené **soubor metadat** z webu Azure portal, klikněte na tlačítko **zvolit soubor**.

    d. Klikněte na tlačítko **další krok**.

6. Na **ověřte informace o poskytovateli** dialogového okna stránky, klikněte na tlačítko **vytvořit**.

    ![Konfigurovat Single Sign-On ověření][15]

7. Klikněte na tlačítko **role**a potom klikněte na tlačítko **vytvořit roli**.

    ![Konfiguraci rolí jednotné přihlašování][16]

8. Na **vytvořit roli** stránce, proveďte následující kroky:  

    ![Nakonfigurovat jednotné přihlašování vztah důvěryhodnosti][19]

    a. Vyberte **SAML 2.0 federace** pod **vyberte typ entity pro důvěryhodného**.

    b. V části **zvolte oddíl SAML 2.0 poskytovatele**, vyberte **SAML zprostředkovatele** jste vytvořili dříve (například: *WAAD*)

    c. Vyberte **povolit programové a přístup ke konzole správy AWS**.
  
    d. Klikněte na tlačítko **Další: Oprávnění**.

9. Na **připojit zásady oprávnění** dialogového okna, přiložte prosím příslušnou zásadu podle vaší organizace. Klikněte na tlačítko **Další: Kontrola**.  

    ![Nakonfigurujte zásady přihlašování][33]

10. Na **revize** dialogového okna, proveďte následující kroky:

    ![Nakonfigurovat jednotné přihlašování revize][34]

    a. V **název Role** textového pole zadejte název Role.

    b. V **popis Role** textového pole zadejte popis.

    c. Klikněte na tlačítko **vytvořit roli**.

    d. Vytvořte tolik role podle potřeby a jejich namapování na zprostředkovatele Identity.

11. Pomocí přihlašovacích údajů účtu služby AWS pro načtení rolí z účtu AWS v zřizování uživatelů služby Azure AD. V takovém případě otevřete konzolu AWS home.

12. Klikněte na **služby** -> **Security, Identity & Compliance** -> **IAM**.

    ![Načítání rolí z účtu AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Vyberte **zásady** kartu v oddílu IAM.

    ![Načítání rolí z účtu AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Vytvořit novou zásadu kliknutím na **vytvořit zásadu** pro načtení rolí z účtu AWS v zřizování uživatelů služby Azure AD.

    ![Vytvoření nové zásady](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Vytvoření vlastních zásad pro načtení všech rolí z účtů AWS provedením následujících kroků:

    ![Vytvoření nové zásady](./media/amazon-web-service-tutorial/policy1.png)

    a. V **"Vytvořit zásady"** části klikněte na **"JSON"** kartu.

    b. V dokumentu zásad, přidejte následující JSON.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Klikněte na **zásady revize tlačítko** ověřit zásady.

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy5.png)

16. Definovat **nové zásady** provedením následujících kroků:

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy2.png)

    a. Zadejte **Název_zásady** jako **AzureAD_SSOUserRole_Policy**.

    b. Můžete zadat **popis** zásad jako **tato zásada vám umožní načíst role z účtů AWS**.

    c. Klikněte na **"Vytvořit zásadu"** tlačítko.

17. Vytvořte nový uživatelský účet ve službě AWS IAM provedením následujících kroků:

    a. Klikněte na **uživatelé** navigace v konzole AWS IAM.

    ![Definovat nové zásady](./media/amazon-web-service-tutorial/policy3.png)

    b. Klikněte na **přidat uživatele** pro vytvoření nového uživatele.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/policy4.png)

    c. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser1.png)

    * Zadejte uživatelské jméno jako **AzureADRoleManager**.

    * Typ přístupu, vyberte **programový přístup** možnost. Tímto způsobem uživatele můžete volat rozhraní API a načíst role z účtu AWS.

    * Klikněte na **další oprávnění** tlačítko v pravém dolním rohu.

18. Teď vytvořte novou zásadu pro tohoto uživatele pomocí následujících kroků:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser2.png)

    a. Klikněte na **připojit existující zásady přímo** tlačítko.

    b. Vyhledejte nově vytvořenou zásadu do oddílu filtrů **AzureAD_SSOUserRole_Policy**.

    c. Vyberte **zásady** a potom klikněte na **Další: Kontrola** tlačítko.

19. Projděte si zásady tak, aby připojený uživatel provedením následujících kroků:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser3.png)

    a. Zkontrolujte uživatelské jméno, typ přístupu a zásady, které jsou namapované na uživatele.

    b. Klikněte na **vytvořit uživatele** tlačítko v pravém dolním rohu pro vytvoření uživatele.

20. Stáhněte si přihlašovací údaje uživatele uživatele tak, že provedete následující kroky:

    ![Přidání uživatele](./media/amazon-web-service-tutorial/adduser4.png)

    a. Zkopírujte uživatel **přístup klíče ID** a **tajný přístupový klíč**.

    b. Zadejte tyto přihlašovací údaje do služby Azure AD zřizování uživatelů části načíst role z konzoly AWS.

    c. Klikněte na **Zavřít** tlačítko dole.

21. Přejděte do **zřizování uživatelů** oddíl aplikace služby Amazon Web Services v portálu pro správu Azure AD.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning.png)

22. Zadejte **přístupový klíč** a **tajný kód** v **tajný kód klienta** a **tajný klíč tokenu** pole v uvedeném pořadí.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Zadejte přístupový klíč pro uživatele AWS v **clientsecret** pole.

    b. Zadejte tajný kód uživatele AWS v **tajný klíč tokenu** pole.

    c. Klikněte na **Test připojení** tlačítko by toto připojení úspěšně otestovat.

    d. Uložte nastavení kliknutím na **Uložit** tlačítko v horní části.

23. Nyní Ujistěte se, že povolíte stav zřizování **na** v sekci nastavení tím, že přepínač na a pak kliknutím na **Uložit** tlačítko v horní části.

    ![Přidání uživatele](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon používat jednotné přihlašování Azure díky udělení přístupu k Amazon Web Services (AWS).

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Amazon Web Services (AWS)** .
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-amazon-web-services-aws-test-user"></a>Vytvořit testovacího uživatele Amazon Web Services (AWS)

Cílem této části je vytvořte uživatele B.Simon Amazon Web Services (AWS). Amazon Web Services (AWS) nemusí uživatel mají být vytvořeny v jejich systému pro jednotné přihlašování, takže není nutné provádět žádnou akci zde.

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Amazon Web Services (AWS) na přístupovém panelu, vám by měl být automaticky přihlášeni k Amazon Web Services (AWS) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Známé problémy

 * V **zřizování** části **mapování** dílčí části zobrazit zprávu "Načítání...", který se nikdy zobrazit mapování atributů. Pouze dnes podporuje zřizování pracovního postupu je import role z AWS do Azure AD pro výběr během přiřazování uživatele nebo skupiny. Mapování atributů pro toto jsou předem a nelze jej konfigurovat.
 
 * **Zřizování** části podporuje pouze zadat jednu sadu přihlašovacích údajů pro jednoho tenanta AWS najednou. Všechny importované role se zapisují do vlastnost appRoles služby Azure AD [objekt servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) AWS tenanta. Více tenantů AWS (reprezentovaný identifikátorem servicePrincipals) mohou být přidány do služby Azure AD z Galerie pro zřizování, ale jde o známý problém s nebude moct automaticky všechny importované role zapisovat z více servicePrincipals AWS použit pro zřizování do jednoho servicePrincipal použít pro jednotné přihlašování. Jako alternativní řešení [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) umožňuje extrahovat všechny appRoles importovat do jednotlivých AWS servicePrincipal kde zřizování je nakonfigurované. Tyto role řetězce lze následně přidat k servicePrincipal AWS, kde nakonfigurován jednotného přihlašování.

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
