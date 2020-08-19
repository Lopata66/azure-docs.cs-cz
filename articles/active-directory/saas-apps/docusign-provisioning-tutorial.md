---
title: 'Kurz: Konfigurace DocuSign pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 5b4e74d5db2d1454360370c05d75cdf826875143
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535930"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Kurz: Konfigurace DocuSign pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v DocuSign a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až DocuSign.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

*   Tenant Azure Active Directory.
*   Předplatné s povoleným DocuSignm jednotným přihlašováním.
*   Uživatelský účet v DocuSign s oprávněními správce týmu.

## <a name="assigning-users-to-docusign"></a>Přiřazování uživatelů k DocuSign

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci DocuSign. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace DocuSign podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Důležité tipy pro přiřazení uživatelů k DocuSign

*   Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD DocuSign. Další uživatele je možné přiřadit později.

*   Při přiřazování uživatele k DocuSign je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

> [!NOTE]
> Azure AD nepodporuje zřizování skupin v aplikaci Docusign, takže je možné zřídit jenom uživatele.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v DocuSign na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro DocuSign, a to podle pokynů uvedených v tématu [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je vysvětlit, jak povolit uživatelům zřizování uživatelských účtů služby Active Directory pro DocuSign.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste už nakonfigurovali DocuSign pro jednotné přihlašování, vyhledejte vaši instanci DocuSign pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Docusign** v galerii aplikací. Ve výsledcích hledání vyberte DocuSign a přidejte je do seznamu aplikací.

1. Vyberte svou instanci DocuSign a pak vyberte kartu **zřizování** .

1. Nastavte **režim zřizování** na **automaticky**. 

    ![zřizování](./media/docusign-provisioning-tutorial/provisioning.png)

1. V části **přihlašovací údaje správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **správce uživatelské jméno** zadejte název účtu Docusign, který má přiřazený profil **správce systému** v Docusign.com.
   
    b. Do textového pole **heslo správce** zadejte heslo pro tento účet.

> [!NOTE]
> Pokud je nastavené jednotné přihlašování i zřizování uživatelů, musí být autorizační pověření používaná pro zřizování nakonfigurovaná tak, aby fungovala s přihlašováním a uživatelským jménem nebo heslem.

1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci Docusign.

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé Docusign.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Docusign. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Docusign pro operace aktualizace. Kliknutím na tlačítko Uložit potvrďte změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro DocuSign, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů přiřazených k DocuSign v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci Docusign.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](docusign-tutorial.md)
