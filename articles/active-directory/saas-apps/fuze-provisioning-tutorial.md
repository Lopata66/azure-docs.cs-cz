---
title: 'Kurz: Konfigurace Fuze pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 937ed160a6a4ee1e08070abd177bd5853f35e9e4
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602149"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Kurz: Konfigurace Fuze pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Fuze a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Fuze.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Fuze](https://www.fuze.com/)
* Uživatelský účet v fuze s oprávněními správce.

## <a name="assigning-users-to-fuze"></a>Přiřazování uživatelů k Fuze

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Fuze. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Fuze podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>Důležité tipy pro přiřazení uživatelů k Fuze

* Doporučuje se, aby se k Fuze k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Fuze musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-fuze-for-provisioning"></a>Nastavení Fuze pro zřizování

Před konfigurací Fuze pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Fuze. 

1. Začněte tím, že se obrátíte na zástupce Fuze, kde najdete následující požadované informace:

    * Seznam SKU produktů Fuze aktuálně používaných ve vaší společnosti.
    * Seznam kódů umístění pro umístění vaší společnosti.
    * Seznam kódů oddělení vaší společnosti

2. Tyto SKU a kódy můžete najít v rámci smlouvy Fuze a konfiguračních dokumentů nebo se můžete obrátit na zástupce Fuze.

3. Po přijetí požadavků vám váš zástupce Fuze poskytne ověřovací token Fuze, který je nutný k povolení integrace. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace Fuze ve Azure Portal.

## <a name="add-fuze-from-the-gallery"></a>Přidání Fuze z Galerie

Před konfigurací Fuze pro Automatické zřizování uživatelů se službou Azure AD je nutné přidat Fuze z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Fuze z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Fuze**, na panelu výsledků vyberte **Fuze** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Fuze v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>Konfigurace automatického zřizování uživatelů na Fuze 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Fuze na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Fuze podle pokynů uvedených v [kurzu Fuze jednotného přihlašování](fuze-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Fuze ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Fuze**.

    ![Odkaz Fuze v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.fuze.com/scim/v2` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve od zástupce Fuze v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Fuze. Pokud se připojení nepovede, ujistěte se, že má váš účet Fuze oprávnění správce, a zkuste to znovu.

    ![Token adresy URL klienta](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Fuze**.

    ![Mapování uživatelů Fuze](media/fuze-provisioning-tutorial/image01.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Fuze v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Fuze pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Mapování uživatelů Fuze](media/fuze-provisioning-tutorial/image00.png)

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Fuze, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Fuze, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Fuze.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Fuze podporuje vlastní atributy SCIM s názvem **oprávnění**. Tyto atributy je možné vytvořit a Neaktualizovat. 

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../manage-apps/check-status-user-account-provisioning.md).