---
title: 'Kurz: Konfigurace konektoru meta Networks pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k konektoru meta Networks.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 40927597031205b5fb1bcc5869922bcc4f3f265c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518730"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Kurz: Konfigurace konektoru meta Networks Connector pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v konektorech meta Networks a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin k síťovému konektoru meta Networks.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant konektoru meta Networks](https://www.metanetworks.com/)
* Uživatelský účet v konektoru meta Networks s oprávněními správce.

## <a name="assigning-users-to-meta-networks-connector"></a>Přiřazování uživatelů k konektoru meta Networks

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k konektoru meta Networks. Jakmile se rozhodnete, můžete tyto uživatele a skupiny přiřadit k konektoru meta Networks pomocí pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Důležité tipy pro přiřazení uživatelů ke konektoru meta Networks

* Doporučuje se, aby se k otestování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD k síťovému konektoru meta Networks. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k síťovému konektoru meta Networks musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Nastavení konektoru meta Networks pro zřizování

1. Přihlaste se ke [konzole pro správu konektoru meta Networks](https://login.metanetworks.com/login/) pomocí názvu vaší organizace. Přejděte do **administrace > klíče rozhraní API**.

    ![Konzola pro správu konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kliknutím na znaménko plus v pravé horní části obrazovky vytvoříte nový **klíč rozhraní API**.

    ![Ikona s konektorem meta Networks plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Nastavte **název klíče rozhraní API** a **Popis klíče rozhraní API**.

    ![Vytvoření tokenu pro konektor meta Networks](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Zapněte oprávnění **zapisovat** pro **skupiny** a **uživatele**.

    ![Oprávnění konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klikněte na **Přidat**. Zkopírujte **tajný klíč** a uložte ho tak, jak se to bude zobrazovat jenom v takovém případě. Tato hodnota se zadá do pole token tajného klíče na kartě zřizování v aplikaci konektoru meta Networks v Azure Portal.

    ![Vytvoření tokenu pro konektor meta Networks](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  IdP můžete přidat tak, že přejdete do **Nastavení správa > > IdP > vytvořit nový**.

    ![Přidat IdP konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na stránce **Konfigurace IDP** můžete **pojmenovat** konfiguraci IDP a vybrat **ikonu**.

    ![Název IdP konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![IdP ikona konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  V části **Konfigurovat SCIM** vyberte název klíče rozhraní API vytvořený v předchozích krocích. Klikněte na **Uložit**.

    ![Konektor meta Networks – konfigurace SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Přejděte na **> nastavení pro správu > karta IDP**. Kliknutím na název konfigurace IdP vytvořené v předchozích krocích zobrazíte **ID IDP**. Toto **ID** se přidá na konec **adresy URL klienta** při zadávání hodnoty do pole **Adresa URL tenanta** na kartě zřizování v aplikaci konektoru meta Networks v Azure Portal.

    ![ID IdP konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Přidání konektoru meta Networks z Galerie

Před konfigurací konektoru meta Networks pro Automatické zřizování uživatelů pomocí Azure AD je potřeba přidat konektor meta Networks z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat konektor meta Networks z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **konektor meta sítě**, vyberte na panelu výsledků položku **meta Networks Connector** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Konektor meta Networks v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurace automatického zřizování uživatelů pro konektory meta Networks 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin ve službě meta Networks Connector na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro konektory meta Networks, a to podle pokynů uvedených v [kurzu jednotného přihlašování v části meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro konektory meta Networks ve službě Azure AD:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **meta sítě konektor**.

    ![Odkaz na konektor meta Networks v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.metanetworks.com/v1/scim/<IdP ID>` na **adrese URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k konektoru meta Networks. Pokud se připojení nepovede, ujistěte se, že má účet konektoru pro meta Network oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele s konektorem meta Networks**.

    ![Mapování uživatelů v konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizované z Azure AD do meta Networks Connectoru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v konektoru meta Networks pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele konektoru meta Networks](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s konektorem meta Networks**.

    ![Mapování skupin konektorů meta Networks](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do meta Networks Connectoru v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v části meta Networks Connector pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny konektorů meta Networks](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning Connector pro konektory meta Networks, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro konektor meta Networks, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část s **podrobnostmi o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v konektoru meta Networks Connector.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)

