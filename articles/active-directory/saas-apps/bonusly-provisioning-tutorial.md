---
title: 'Kurz: pro Automatické zřizování uživatelů pomocí Azure Active Directory nakonfigurujte bonus | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro bonus.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058837"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Kurz: konfigurace pro Automatické zřizování uživatelů v bonusovém prostředí

Cílem tohoto kurzu je předvést kroky, které je třeba provést v bonusu a Azure Active Directory (Azure AD), abyste mohli nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřizování uživatelů a skupin.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* Tenant Azure AD
* [Bonusový tenant](https://bonus.ly/pricing)
* Účet uživatele v bonusu s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [bonusové rozhraní REST API](https://konghq.com/solutions/gateway/), které je dostupné pro mimořádné vývojáře.

## <a name="adding-bonusly-from-the-gallery"></a>Přidávání bonusů z Galerie

Předtím, než v rámci služby Azure AD nakonfigurujete bonus pro Automatické zřizování uživatelů, budete muset přidat bonus z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat bonus z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **bonus**, z panelu výsledků vyberte **bonus** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Bonusně v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Přiřazení uživatelů k bonusu

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD. 

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD budou potřebovat přístup za bonus. Po rozhodnutí můžete tyto uživatele a skupiny přiřadit za bonus podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Důležité tipy pro přiřazení uživatelů k bonusu

* Doporučujeme, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD za bonus. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele Bonusně, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurace automatického zřizování uživatelů pro bonus

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v bonusovém prostředí na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro prémii jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu jednotného přihlašování v bonusovém](bonus-tutorial.md)rámci. Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Postup při konfiguraci automatického zřizování uživatelů pro prémii v Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **bonusně**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **bonusně**.

    ![Bonusový odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** zadejte **tajný token** účtu, který je popsaný v kroku 6.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. **Tajný token** pro váš účet v bonusovém účtu najdete v části **správce > integrace > společnosti**. V části **Pokud chcete Code Code** klikněte na **rozhraní API > vytvořit nový přístupový token rozhraní API** k vytvoření nového tajného tokenu.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na následující obrazovce zadejte do zadaného textového pole název přístupového tokenu a pak stiskněte tlačítko **vytvořit klíč rozhraní API**. Nový přístupový token se v automaticky otevíraném okně zobrazí během několika sekund.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/Token02.png)

8. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k bonusům. Pokud se připojení nepovede, zajistěte, aby měl účet v bonusovém účtu oprávnění správce, a zkuste to znovu.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k selhání**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klikněte na **Uložit**.

11. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelé pro bonus**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Zkontrolujte atributy uživatele synchronizované z Azure AD, aby byly v oddílu **mapování atributů** bonusně. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v bonusu za operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pokud chcete povolit službu Azure AD Provisioning pro bonus, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definujte uživatele nebo skupiny, které chcete zřídit, aby se vybraly bonusem, a to výběrem požadovaných hodnot v **rozsahu** v části **Nastavení** .

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Bonusové zřizování](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následnému odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na bonus.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
