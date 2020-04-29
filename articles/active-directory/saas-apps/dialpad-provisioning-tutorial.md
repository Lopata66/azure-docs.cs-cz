---
title: 'Kurz: Konfigurace Dialpad pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Dialpad.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058336"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Kurz: Konfigurace Dialpad pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Dialpad a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Dialpad.

> [!NOTE]
>  Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

> Tento konektor je momentálně ve verzi Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Dialpad](https://www.dialpad.com/pricing/)
* Uživatelský účet v Dialpad s oprávněními správce.

## <a name="assign-users-to-dialpad"></a>Přiřazení uživatelů k Dialpad
Azure Active Directory používá koncept nazvaný přiřazení k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Dialpad. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Dialpad podle pokynů uvedených tady:
 
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Důležité tipy pro přiřazení uživatelů k Dialpad

 * Doporučuje se, aby se k Dialpad k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Dialpad musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s výchozí rolí přístupu se z zřizování vylučují.

## <a name="setup-dialpad-for-provisioning"></a>Nastavení Dialpad pro zřizování

Před konfigurací Dialpad pro Automatické zřizování uživatelů se službou Azure AD bude nutné načíst některé informace o zřizování z Dialpad.

1. Přihlaste se ke [konzole správce Dialpad](https://dialpadbeta.com/login) a vyberte **Nastavení správce**. Ujistěte se, že je v rozevíracím seznamu vybraná možnost **Moje společnost** . Přejděte na **ověřovací > klíče rozhraní API**.

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Vygenerujte nový klíč kliknutím na **Přidat klíč** a nakonfigurujete vlastnosti tajného tokenu.

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klikněte na tlačítko **kliknutím zobrazit hodnotu** pro nedávno vytvořený klíč rozhraní API a zkopírujte zobrazenou hodnotu. Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace Dialpad ve Azure Portal. 

    ![Dialpad vytvořit token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Přidání Dialpad z Galerie

Pokud chcete nakonfigurovat Dialpad pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Dialpad z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Dialpad z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dialpad**, na panelu výsledků vyberte **Dialpad** .
    ![Dialpad v seznamu výsledků](common/search-new-app.png)

5. V samostatném prohlížeči přejděte na **adresu URL** zvýrazněnou níže. 

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. V pravém horním rohu vyberte možnost **přihlásit > použijte Dialpad online**.

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Dialpad je aplikace OpenIDConnect, která se rozhodne přihlásit k Dialpad pomocí pracovního účtu Microsoft.

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po úspěšném ověření Přijměte výzvu k zadání souhlasu pro stránku souhlasu. Aplikace se pak automaticky přidá do vašeho tenanta a budete přesměrováni na svůj účet Dialpad.

    ![Dialpad přidat SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurace automatického zřizování uživatelů na Dialpad

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Dialpad na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Dialpad ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Dialpad**.

    ![Odkaz Dialpad v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://dialpad.com/scim` **adresu URL tenanta**. Zadejte hodnotu, kterou jste načetli a uložili dříve ze Dialpad v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Dialpad. Pokud se připojení nepovede, ujistěte se, že má váš účet Dialpad oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Dialpad**.

    ![Mapování uživatelů Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Dialpad v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Dialpad pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Dialpad, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Dialpad, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Dialpad.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md) .
##  <a name="connector-limitations"></a>Omezení konektoru
* Dialpad v současné době nepodporuje přejmenování skupin. To znamená, že jakékoli změny **DisplayName** skupiny ve službě Azure AD nebudou aktualizovány a projeví se v Dialpad.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
