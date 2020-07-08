---
title: 'Kurz: Konfigurace SmartFile pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77060214"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Kurz: Konfigurace SmartFile pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v SmartFile a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro SmartFile.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant SmartFile](https://www.SmartFile.com/pricing/)
* Uživatelský účet v SmartFile s oprávněními správce.

## <a name="assigning-users-to-smartfile"></a>Přiřazování uživatelů k SmartFile

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k SmartFile. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k SmartFile podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Důležité tipy pro přiřazení uživatelů k SmartFile

* Doporučuje se, aby se k SmartFile k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k SmartFile musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-smartfile-for-provisioning"></a>Nastavení SmartFile pro zřizování

Před konfigurací SmartFile pro Automatické zřizování uživatelů se službou Azure AD budete muset povolit SCIM zřizování na SmartFile a shromažďovat další podrobnosti, které potřebujete.

1. Přihlaste se ke konzole pro správu SmartFile. Přejděte do pravého horního rohu konzoly pro správu SmartFile. Vyberte **kód Product Key**.

    ![Konzola pro správu SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Pokud chcete vygenerovat nosný token, zkopírujte **kód Product Key** a **heslo k produktu**. Vložte je do poznámkového bloku s dvojtečkou mezi nimi.
    
     ![SmartFile přidat SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile přidat SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Přidání SmartFile z Galerie

Pokud chcete nakonfigurovat SmartFile pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat SmartFile z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat SmartFile z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SmartFile**, na panelu výsledků vyberte **SmartFile** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SmartFile v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Konfigurace automatického zřizování uživatelů na SmartFile 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v SmartFile na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro SmartFile podle pokynů uvedených v [kurzu SmartFile jednotného přihlašování](SmartFile-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro SmartFile ve službě Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SmartFile**.

    ![Odkaz SmartFile v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5.  V části **přihlašovací údaje správce** zadejte `https://<SmartFile sitename>.smartfile.com/ftp/scim` **adresu URL tenanta**. Příklad by vypadal jako `https://demo1test.smartfile.com/ftp/scim` . Zadejte hodnotu **tokenu nosiče** (ProductKey: ProductPassword), kterou jste získali dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k SmartFile. Pokud se připojení nepovede, ujistěte se, že má váš účet SmartFile oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé SmartFile**.

    ![Mapování uživatelů SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do SmartFile v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v SmartFile pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do SmartFile**.

    ![Mapování skupin SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do SmartFile v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v SmartFile pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![SmartFile – atributy skupiny](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro SmartFile, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro SmartFile, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

    Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v SmartFile.

    Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md) .
    
## <a name="connector-limitations"></a>Omezení konektoru

* SmartFile podporuje pouze pevné odstranění. 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

 [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
