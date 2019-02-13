---
title: 'Kurz: Integrace Azure Active Directory s využitím The Fabric cloudové zabezpečení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zabezpečení infrastruktury cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a556b38ca4947b71555ba7b023607b392900bdaf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210386"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Kurz: Integrace Azure Active Directory s využitím The Fabric zabezpečení cloudu

V tomto kurzu se dozvíte, jak integrovat The Fabric zabezpečení cloudu s Azure Active Directory (Azure AD).

Integrace zabezpečení infrastruktury cloudu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k The Fabric zabezpečení cloudu.
- Uživatele, aby automaticky získat přihlášení k The Fabric zabezpečení cloudu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s využitím The Fabric zabezpečení cloudu, potřebujete následující položky:

- Předplatné Azure AD
- The Fabric zabezpečení cloudu jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání The Fabric zabezpečení cloudu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Přidání The Fabric zabezpečení cloudu z Galerie
Konfigurace integrace The Fabric zabezpečení cloudu do služby Azure AD, budete muset přidat The Fabric zabezpečení cloudu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat The Fabric zabezpečení cloudu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **The Fabric zabezpečení cloudu**vyberte **The Fabric zabezpečení cloudu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zabezpečení infrastruktury cloudu v seznamu výsledků](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí The Fabric zabezpečení cloudu na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v prostředcích infrastruktury zabezpečení cloudu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v prostředcích infrastruktury zabezpečení cloudu je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím The Fabric zabezpečení cloudu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele The Fabric zabezpečení cloudu](#create-a-the-cloud-security-fabric-test-user)**  – Pokud chcete mít protějšek Britta Simon v prostředcích infrastruktury cloudové zabezpečení, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Fabric zabezpečení v cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s využitím The Fabric zabezpečení cloudu, proveďte následující kroky:**

1. Na webu Azure Portal na **The Fabric zabezpečení cloudu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

1. Na **cloudové zabezpečení infrastruktury domén a adres URL** části, proveďte následující kroky:

    ![Cloudové zabezpečení infrastruktury domény a adresy URL jednotného přihlašování – informace](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Identifikátor skutečné zaktualizujte příslušnou hodnotu. Kontakt [tým podpory klient Fabric zabezpečení cloudu](mailto:support@cloudlock.com) má být získána hodnota. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **The Fabric zabezpečení cloudu** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory zabezpečení infrastruktury cloudu](mailto:support@cloudlock.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Vytvoření zkušebního uživatele zabezpečení infrastruktury cloudu

V této části vytvořte uživatele Britta Simon v prostředcích infrastruktury zabezpečení cloudu. Práce s [tým podpory zabezpečení infrastruktury cloudu](mailto:support@cloudlock.com) přidat uživatele na platformě zabezpečení infrastruktury cloudu. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k The Fabric zabezpečení cloudu.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit The Fabric zabezpečení cloudu, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **The Fabric zabezpečení cloudu**.

    ![Zabezpečení cloudových prostředků infrastruktury propojení v seznamu aplikací](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici The Fabric zabezpečení cloudu na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci zabezpečení infrastruktury cloudu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
