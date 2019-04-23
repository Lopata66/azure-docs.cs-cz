---
title: 'Kurz: Konfigurace Bonusly pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Bonusly.
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad0ee590572dbc92e67be9f84ffc65afc3e8473
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60282315"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Kurz: Konfigurace Bonusly pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Bonusly a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Bonusly.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

* Klient služby Azure AD
* A [Bonusly tenanta](https://bonus.ly/pricing)
* Uživatelský účet v Bonusly s oprávněními správce

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [Bonusly rozhraní Rest API](https://bonusly.gelato.io/reference), která je k dispozici Bonusly vývojářům.

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z Galerie

Před konfigurací Bonusly pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Bonusly z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Bonusly z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bonusly**vyberte **Bonusly** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Bonusly v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Přiřazování uživatelů k Bonusly

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Bonusly. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Bonusly podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Důležité tipy pro přiřazování uživatelů k Bonusly

* Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Bonusly otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Bonusly, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurace automatické zřizování uživatelů pro Bonusly

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Bonusly podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Bonusly, postupujte podle pokynů uvedených v [Bonusly jednotné přihlašování – kurz](bonus-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Bonusly ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Bonusly**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Bonusly**.

    ![Odkaz Bonusly v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **tajný klíč tokenu** vaše Bonusly účtu jak je popsáno v kroku 6.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. **Tajný klíč tokenu** Bonusly váš účet se nachází v **správce > společnost > integrace**. V **Pokud budete chtít kód** části, klikněte na **rozhraní API > vytvořit nové rozhraní API přístupový Token** vytvořit nový Token tajného kódu.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na následujícím obrázku, zadejte název pro přístupový token poskytnutý textového pole, stiskněte klávesu **vytvořit klíč rozhraní Api**. Nový přístupový token se zobrazí v automaticky otevírané okno na několik sekund.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/Token02.png)

8. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Bonusly. Pokud se nepovede, ujistěte se, že Bonusly účet má oprávnění správce a zkuste to znovu.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtněte políčko **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klikněte na **Uložit**.

11. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Bonusly**.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Bonusly v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Bonusly pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Služba pro Bonusly zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definovat uživatele a/nebo skupiny, které chcete k poskytování Bonusly výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Bonusly zřizování](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Bonusly zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
