---
title: 'Kurz: Konfigurace služby WSS (Symantec Web Security Service) pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřizování uživatelských účtů pro službu Symantec Web Security Service (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 083bb7549bb555cbbefaf680d401696ecd64b9c9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544906"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby WSS (Symantec Web Security Service) pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést ve službě Symantec Web Security Service (WSS) a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro službu Symantec Web Security Service (WSS).

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant služby Web Security Service (WSS) společnosti Symantec](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uživatelský účet ve službě Symantec Web Security Service (WSS) s oprávněními správce.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Přiřazení uživatelů ke službě Symantec Web Security Service (WSS)

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup ke službě WSS (Symantec Web Security Service). Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k službě Symantec Web Security Service (WSS) podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Důležité tipy pro přiřazení uživatelů ke službě Symantec Web Security Service (WSS)

* Doporučuje se, aby se jeden uživatel Azure AD přiřadil ke službě Symantec Web Security Service (WSS), aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele ke službě Symantec Web Security Service (WSS) musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Nastavit pro zřizování službu Symantec Web Security Service (WSS)

Než začnete konfigurovat službu Symantec Web Security Service (WSS) pro Automatické zřizování uživatelů s Azure AD, budete muset povolit zřizování SCIM na službě Symantec Web Security Service (WSS).

1. Přihlaste se ke [konzole pro správu služby zabezpečení webu Symantec](https://portal.threatpulse.com/login.jsp). Přejděte do **Solutions**  >  **služby**řešení.

    ![WSS (Symantec Web Security Service)](media/symantec-web-security-service/service.png)

2. Přejděte k **integraci údržby účtů**  >  **Integrations**  >  **Nová integrace**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Vyberte možnost **Uživatelé třetích stran synchronizovat skupiny &**. 

    ![Služba zabezpečení webu společnosti Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Zkopírujte **adresu URL** a **token**pro SCIM. Tyto hodnoty se zadají do pole **Adresa URL klienta** a **tajný token** na kartě zřizování aplikace služby Web Security (WSS) společnosti Symantec v Azure Portal.

    ![Služba zabezpečení webu společnosti Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Přidání služby WSS (Symantec Web Security Service) z Galerie

Pokud chcete nakonfigurovat službu WSS Web Security Service (WSS) pro Automatické zřizování uživatelů pomocí Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat službu WSS (Symantec Web Security Service) z Galerie aplikací Azure AD.

**Pokud chcete přidat službu WSS Web Security Service (WSS) z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **společnost Symantec Web Security**, na panelu výsledků vyberte možnost **Symantec Web Security** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Symantec Web Security Service (WSS) v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurace automatického zřizování uživatelů pro službu Symantec Web Security Service (WSS)

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin ve službě WSS (Symantec Web Security Service) na základě přiřazení uživatelů nebo skupin v Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Symantec Web Security Service (WSS) podle pokynů uvedených v [kurzu jednotného přihlašování služby WSS (Web Security Service) společnosti Symantec](symantec-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro službu WSS (Web Security Service) ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Symantec Web Security Service**.

    ![Odkaz na službu webu Symantec Web Security (WSS) v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte **adresu URL** a hodnoty **tokenu** SCIM dříve načtené v **adrese URL tenanta** a **tajného tokenu** v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit ke službě Symantec Web Security. Pokud se připojení nepovede, zajistěte, aby měl účet Symantec Web Security Service (WSS) oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele se službou Symantec Web Security Service (WSS)**.

    ![Mapování uživatelů na službu Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD se službou Symantec Web Security Service (WSS) v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů ve WSS (Symantec Web Security Service) pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Mapování uživatelů na službu Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny se službou Symantec Web Security**.

    ![Mapování uživatelů na službu Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD se službou Symantec Web Security Service (WSS) v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin ve službě WSS (Web Security Service) pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Mapování uživatelů na službu Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro službu Symantec Web Security, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro službu Symantec Web Security Service (WSS), a vyberte požadované hodnoty v části **Rozsah** **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na službě Symantec Web Security Service (WSS). Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
