---
title: Upravit informace o vaší skupině – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak lze upravit informace o vaší skupiny pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561874"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Upravit skupinu informací pomocí Azure Active Directory

Pomocí Azure Active Directory (Azure AD), můžete upravit nastavení skupiny, včetně aktualizace jeho název, popis nebo členství v typu.

## <a name="to-edit-your-group-settings"></a>Chcete-li upravit nastavení skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

    **Skupiny – všechny skupiny** se zobrazí stránka zobrazuje všechny aktivní skupiny.

3. Z **skupiny – všechny skupiny** stránky, zadejte co největší část názvu skupiny, jak můžete do **hledání** pole. Pro účely tohoto článku jsme při hledání **zásady MDM - západní** skupiny.

    Výsledky hledání zobrazeny podle **hledání** pole, aktualizují se při psaní více znaků.

    ![Stránka všech skupin, se hledaný text do vyhledávacího pole](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Vyberte skupinu **zásady MDM - západní**a pak vyberte **vlastnosti** z **spravovat** oblasti.

    ![Stránka s přehledem skupin s možnostmi členů a zvýrazněnými informacemi](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Aktualizace **obecné nastavení** informace podle potřeby, včetně:

    ![Nastavení vlastností pro skupinu](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Název skupiny.** Upravte název existující skupiny.
    
    - **Popis skupiny**: Upravte existující popis skupiny.

    - **Typ skupiny.** Typ skupiny nelze změnit po vytvoření. Chcete-li změnit **typ skupiny**, musíte odstranit skupinu a vytvořte novou.
    
    - **Typ členství.** Změníte typ členství. Další informace o různých dostupných typech členství najdete v tématu [How to: Vytvořte základní skupinu a přidejte členy pomocí portálu](active-directory-groups-create-azure-portal.md)Azure Active Directory.
    
    - **ID objektu.** ID objektu nelze změnit, ale můžete je zkopírovat používané k příkazům Powershellu pro skupinu. Další informace o používání rutin prostředí PowerShell najdete v tématu [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Další postup
Následující články poskytují další informace o službě Azure Active Directory.

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Postup přidání nebo odebrání členů ze skupiny](active-directory-groups-members-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
