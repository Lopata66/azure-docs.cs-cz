---
title: 'Kurz: zřizování uživatelů pro CERN – střed – Azure AD'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro Automatické zřizování uživatelů v seznamu v centru CERN.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058312"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Kurz: Konfigurace centrálního přivisionu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které potřebujete k tomu, abyste v rámci služby CERN a Azure AD automaticky zřídili a zrušili zřizování uživatelských účtů od Azure AD až po seznam uživatelů ve společnosti CERN – střed.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

* Tenanta Azure Active Directory.
* Centrální tenant pro CERN

> [!NOTE]
> Azure Active Directory se integruje se standardem CERN pomocí protokolu [SCIM](http://www.simplecloud.info/) .

## <a name="assigning-users-to-cerner-central"></a>Přiřazení uživatelů ke CERN – střed

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, měli byste se rozhodnout, co můžou uživatelé a skupiny v Azure AD zastupovat s uživateli, kteří potřebují přístup ke službě CERN (střed). Jakmile se rozhodnete, můžete těmto uživatelům přiřadit střed k CERN, a to podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Důležité tipy pro přiřazení uživatelů ke CERN – střed

* Doporučuje se, aby se jeden uživatel Azure AD přiřadil ke službě CERN Central, aby otestoval konfiguraci zřizování. Další uživatele a skupiny můžete přiřadit později.

* Po dokončení počátečního testování pro jednoho uživatele doporučuje společnost CERN přiřazovat úplný seznam uživatelů, kteří mají přístup k jakémukoli řešení CERN (ne jenom k centrálnímu úřadu), aby je bylo možné zřídit v seznamu uživatelů ve společnosti CERN.  Další řešení pro CERN využívají tento seznam uživatelů v pracovním seznamu uživatelů.

* Když přiřadíte uživatele do organizace CERN, musíte v dialogovém okně přiřazení vybrat roli **uživatele** . Uživatelé s rolí "výchozí přístup" se z zřizování vylučují.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurace zřizování uživatelů na CERN – střed

V této části se seznámíte s tím, že propojíte svůj účet Azure AD s využitím rozhraní API pro zřizování uživatelských účtů SCIM CERN a nakonfigurujete službu zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v centru CERN na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout, že povolíte jednotné přihlašování založené na SAML pro standard CERN, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když tyto dvě funkce doplňují sebe. Další informace najdete v [kurzu centrálního jednotného přihlašování ve službě CERN](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na CERN – střed v Azure AD:

Aby bylo možné zřídit uživatelské účty pro společnost "CERN", budete muset požádat o účet centrálního systému CERN od společnosti CERN a vygenerovat token nosiče OAuth, který může služba Azure AD použít pro připojení ke koncovému bodu SCIM společnosti CERN. Před nasazením do produkčního prostředí taky doporučujeme, aby se integrace prováděla v prostředí s izolovaným prostorem (CERN).

1. Prvním krokem je zajistit, aby lidé, kteří spravují CERN a integraci služby Azure AD, měli účet CernerCare, který je nezbytný pro přístup k dokumentaci, která je nutná k dokončení těchto pokynů. V případě potřeby pomocí níže uvedených adres URL vytvořte účty CernerCare v každém příslušném prostředí.

   * Úložištihttps://sandboxcernercare.com/accounts/create

   * Produkčníhttps://cernercare.com/accounts/create  

2. V dalším kroku se musí vytvořit systémový účet pro Azure AD. Pomocí níže uvedených pokynů si vyžádejte systémový účet pro izolovaný prostor a produkční prostředí.

   * Pokynůhttps://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Úložištihttps://sandboxcernercentral.com/system-accounts/

   * Produkčníhttps://cernercentral.com/system-accounts/

3. Dále vygenerujte nosný token OAuth pro každý účet systému. Pokud to chcete provést, postupujte podle následujících pokynů.

   * Pokynůhttps://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Úložištihttps://sandboxcernercentral.com/system-accounts/

   * Produkčníhttps://cernercentral.com/system-accounts/

4. Nakonec potřebujete pro dokončení konfigurace získat ID sféry soupisu uživatelů pro izolovaný prostor a produkční prostředí ve společnosti CERN. Informace o tom, jak to získat, najdete v tématu: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM . 

5. Teď můžete službu Azure AD nakonfigurovat tak, aby zřídila uživatelské účty pro CERN. Přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte do části **Azure Active Directory > podnikové aplikace > všechny aplikace** .

6. Pokud jste již v rámci jednotného přihlašování nakonfigurovali CERN (), vyhledejte svou instanci poskytovatele CERN pomocí vyhledávacího pole. V opačném případě vyberte možnost **Přidat** a vyhledejte v galerii aplikací položku **CERN** . Z výsledků hledání vyberte CERN – střed a přidejte ho do seznamu aplikací.

7. Vyberte svou instanci nástroje CERN (střed) a pak vyberte kartu **zřizování** .

8. Nastavte **režim zřizování** na **automaticky**.

   ![Centrální zřizování pro CERN](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Do následujících polí zadejte v části **přihlašovací údaje správce**:

   * V poli **Adresa URL tenanta** zadejte adresu URL ve formátu níže a nahraďte "User-The-REALM-ID" ID sféry, kterou jste získali v kroku #4.

    > Úložištihttps://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produkčníhttps://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * V poli **token tajného klíče** zadejte token nosiče OAuth, který jste vygenerovali v kroku #3 a klikněte na **Test připojení**.

   * Na straně upperright na portálu by se mělo zobrazit oznámení o úspěchu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

1. Klikněte na **Uložit**.

1. V části **mapování atributů** zkontrolujte atributy uživatelů a skupin, které se mají synchronizovat z Azure AD do společnosti CERN – střed. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů a skupin v centru CERN – střed pro operace aktualizace. Kliknutím na tlačítko Uložit potvrďte změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro CERN (střed), změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

1. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených ke službě CERN (střed) v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování v centrální aplikaci pro vaši organizaci.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [CERN – střed: publikování dat identity pomocí Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Kurz: Konfigurace služby CERN (střed) pro jednotné přihlašování pomocí Azure Active Directory](cernercentral-tutorial.md)
* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
