---
title: 'Kurz: Konfigurace TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063134"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Kurz: Konfigurace TheOrgWiki pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v TheOrgWiki a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro TheOrgWiki.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant OrgWiki](https://www.theorgwiki.com/welcome/)
* Uživatelský účet v TheOrgWiki s oprávněními správce.

## <a name="assign-users-to-theorgwiki"></a>Přiřazení uživatelů k TheOrgWiki

Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k TheOrgWiki. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k TheOrgWiki podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Důležité tipy pro přiřazení uživatelů k TheOrgWiki

* Doporučuje se, aby se k TheOrgWiki k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k TheOrgWiki musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-theorgwiki-for-provisioning"></a>Nastavení TheOrgWiki pro zřizování

Před konfigurací TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na TheOrgWiki.

1. Přihlaste se ke [konzole pro správu TheOrgWiki](https://www.theorgwiki.com/login/). Klikněte na **Konzola pro správu**.

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. V konzole pro správu klikněte na **kartu nastavení**. 

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Přejděte na **účty služeb**.

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klikněte na **+ účet služby**. V části **typ účtu služby**vyberte **založené na tokenech**. Klikněte na **Uložit**.

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Zkopírujte **aktivní tokeny**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace TheOrgWiki ve Azure Portal.
     
    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Přidání TheOrgWiki z Galerie

Pokud chcete nakonfigurovat TheOrgWiki pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat TheOrgWiki z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TheOrgWiki**, na panelu výsledků vyberte **TheOrgWiki** . 

    ![TheOrgWiki v seznamu výsledků](common/search-new-app.png)

5. Vyberte tlačítko **zaregistrovat se k TheOrgWiki** , které vás přesměruje na přihlašovací stránku TheOrgWiki. 

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  V pravém horním rohu vyberte **přihlášení**.

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. TheOrgWiki je aplikace OpenIDConnect, která se rozhodne přihlásit k OrgWiki pomocí pracovního účtu Microsoft.

    ![TheOrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Po úspěšném ověření se aplikace automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet TheOrgWiki.

    ![OrgWiki přidat SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurace automatického zřizování uživatelů na TheOrgWiki 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v TheOrgWiki na základě přiřazení uživatelů nebo skupin ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro TheOrgWiki ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **TheOrgWiki**.

    ![Odkaz OrgWiki v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **adresu URL tenanta**. 

    Příklad: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Hodnotu subdomény** lze nastavit pouze během počátečního procesu registrace pro TheOrgWiki.
 
6. Zadejte hodnotu tokenu v poli **tajný token** , který jste dříve získali z TheOrgWiki. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k TheOrgWiki. Pokud se připojení nepovede, ujistěte se, že má váš účet TheOrgWiki oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé TheOrgWiki**.

    ![Mapování uživatelů TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do TheOrgWiki v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v TheOrgWiki pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro TheOrgWiki, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro OrgWiki, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v TheOrgWiki. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).