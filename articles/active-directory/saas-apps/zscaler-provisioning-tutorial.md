---
title: 'Kurz: Konfigurace Zscaler pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936579"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Kurz: Konfigurace Zscaler pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Zscaler a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Zscaler.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující:

* Tenanta Azure AD.
* Tenant Zscaler
* Uživatelský účet v Zscaler s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na rozhraní Zscaler SCIM API, které je k dispozici pro vývojáře Zscaler pro účty s podnikovým balíčkem.

## <a name="adding-zscaler-from-the-gallery"></a>Přidání Zscaler z Galerie

Před konfigurací Zscaler pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Zscaler z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler**, vyberte **Zscaler** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zscaler v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Přiřazování uživatelů k Zscaler

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Zscaler. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Zscaler podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Důležité tipy pro přiřazení uživatelů k Zscaler

* Doporučuje se, aby se k Zscaler k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Zscaler musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurace automatického zřizování uživatelů na Zscaler

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Zscaler na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Zscaler podle pokynů uvedených v [kurzu Zscaler jednotného přihlašování](zscaler-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

> [!NOTE]
> Pokud jsou uživatelé a skupiny zřízené nebo nezřízené, doporučujeme, abyste pravidelně restartovali zřizování, aby se zajistilo, že členství ve skupině bude správně aktualizováno. Při restartování dojde k vynucení, aby naše služba znovu vyhodnotila všechny skupiny a aktualizovala členství. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Zscaler ve službě Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Zscaler**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler**.

    ![Odkaz Zscaler v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s postranním panelem Zscaler-Provisioning pro podnikovou aplikaci se zvýrazněnou možností zřizování](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek stránky zřizování s režimem zřizování nastaveným na automatické.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** a **tajný token** účtu Zscaler, jak je popsáno v kroku 6.

6. Pokud chcete získat **adresu URL tenanta** a **tajný token**, přejděte do části **Správa > nastavení ověřování** v uživatelském rozhraní portálu Zscaler a klikněte na protokol **SAML** pod položkou **typ ověřování**.

    ![Snímek obrazovky se stránkou nastavení ověřování.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknutím na **nakonfigurovat SAML** otevřete nastavení možnosti **SAML** .

    ![Snímek obrazovky s dialogovým oknem konfigurovat S A M L se základními textovými poli pro R L a nosičem s tokeny](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Vyberte **povolit SCIM-Based zřizování** pro načtení **základní adresy URL** a **nosného tokenu** a pak nastavení uložte. Zkopírujte **základní adresu URL** na **adresu URL tenanta** a **token nosiče**  do **tajného tokenu** v Azure Portal.

7. Po vyplnění polí zobrazených v kroku 5 klikněte na **Test připojení** , aby se služba Azure AD mohla připojit k Zscaler. Pokud se připojení nepovede, ujistěte se, že má váš účet Zscaler oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky s oddílem přihlašovací údaje správce s možností připojení testu s názvem.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k selhání**.

    ![Snímek obrazovky s textovým e-mailem s oznámením](./media/zscaler-provisioning-tutorial/notification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Zscaler**.

    ![Snímek obrazovky oddílu mapování s zvýrazněnou možností synchronizovat Azure Active Directory uživatele do Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Zscaler v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Zscaler pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými sedmi mapováními](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Zscaler**.

    ![Snímek obrazovky s oddílem mapování se zvýrazněnou možností synchronizovat Azure Active Directory skupin do Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD do Zscaler v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Zscaler pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky oddílu mapování atributů se třemi zobrazenými mapováními](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Zscaler, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Snímek obrazovky s možností stavu zřizování nastaven na zapnuto](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Zscaler, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Snímek obrazovky s nastavením oboru se zvýrazněnou možností synchronizovat pouze přiřazení uživatelé a skupiny.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Snímek obrazovky s postranním panelem Zscaler-Provisioning pro podnikovou aplikaci pomocí možnosti uložit s názvem.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Zscaler.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png