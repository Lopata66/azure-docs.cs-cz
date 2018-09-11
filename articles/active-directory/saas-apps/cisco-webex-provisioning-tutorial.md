---
title: 'Kurz: Konfigurace Cisco pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: 8f5af3cba01e925591c9d90ea0e96ed78b2823e2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348374"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Kurz: Konfigurace Cisco Webex pro automatické zřizování uživatelů


Cílem tohoto kurzu je předvést postup provést v Cisco Webex a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatelů pro Cisco Webex.


> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

*   Klient služby Azure AD
*   Cisco Webex tenanta
*   Uživatelský účet v Cisco Webex s oprávněními správce


> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [Cisco Webex Webservice](https://developer.webex.com/getting-started.html), který je dostupný pro týmy Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie
Před konfigurací Cisco Webex pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Cisco Webex z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cisco Webex z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]

3. Chcete-li přidat Cisco Webex, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Webex**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Cisco Webex**a potom klikněte na tlačítko **přidat** tlačítko pro přidání do seznamu aplikací SaaS Cisco Webex.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Přiřazování uživatelů k Cisco Webex

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup ke Cisco Webex. Jakmile se rozhodli, můžete přiřadit tyto uživatele Cisco Webex podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Důležité tipy pro přiřazování uživatelů k Cisco Webex

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazená Cisco Webex otestovat automatické konfigurace zřizování uživatelů. Další uživatele může být přiřazen později.

*   Při přiřazení uživatele k Cisco Webex, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurace automatické zřizování uživatelů pro Cisco Webex

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele v Cisco Webex podle přiřazení uživatelů ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Cisco Webex ve službě Azure AD:


1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Cisco Webex.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta**, a **tajný klíč tokenu** účtu vaší společnosti Cisco Webex.

    *   V **adresy URL Tenanta** pole, vyplnit adresy URL Cisco Webex SCIM rozhraní API pro vašeho tenanta, který má formu `https://api.webex.com/v1/scim/[Tenant ID]/`, kde `[Tenant ID]` je alfanumerický řetězec, jak je popsáno v kroku 6.

    *   V **tajný klíč tokenu** pole, vyplňte tajný klíč tokenu, jak je popsáno v kroku 6.

1. **ID Tenanta** a **tajný klíč tokenu** pro vaše Cisco Webex účtu najdete po přihlášení na [webu pro vývojáře Cisco Webex](https://developer.webex.com/) pomocí účtu správce. Po přihlášení -
    * Přejděte [stránka Začínáme](https://developer.webex.com/getting-started.html)
    * Přejděte dolů k položce [oddíl Authentication](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Webex ověřovací Token](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * Alfanumerický řetězec v poli je vaše **tajný klíč tokenu**. Zkopírujte tento token do schránky
    * Přejděte [získat Moje vlastní stránka s podrobnostmi o](https://developer.webex.com/endpoint-people-me-get.html)
        * Zajistěte, aby byl Test režimu ON
        * Zadejte slovo "Nosiče" následované mezerou a vložte Token tajného klíče do pole autorizace ![Cisco Webex ověřovací Token](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Klikněte na tlačítko Spustit
    * V textu odpovědi na pravé straně **ID Tenanta** se zobrazí jako "orgId":

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Cisco Webex. Pokud se nepovede, ujistěte se, že váš účet Cisco Webex má oprávnění správce a zkuste to znovu.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelům Cisco Webex**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Cisco Webex v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Cisco Webex pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Služba pro Cisco Webex zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Definovat uživatele a/nebo skupiny, které chcete k poskytování Cisco Webex výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/Save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Cisco Webex zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Cisco Webex je momentálně ve fázi včasného testování pole (EFT) Cisco. Další informace, obraťte se prosím na [tým podpory Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png
