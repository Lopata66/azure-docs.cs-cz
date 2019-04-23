---
title: Upravit informace o vaší skupině – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak lze upravit informace o vaší skupiny pomocí služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 691f705574050b15869a0ac8b7d128507e5aae10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248843"
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

    ![Stránka s přehledem, s informacemi o zvýrazní a možnost členů skupiny](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Aktualizace **obecné nastavení** informace podle potřeby, včetně:

    ![Nastavení vlastností pro skupinu](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Název skupiny.** Upravte název existující skupiny.
    
    - **Popis skupiny**: Upravte existující popis skupiny.

    - **Typ skupiny.** Typ skupiny nelze změnit po vytvoření. Chcete-li změnit **typ skupiny**, musíte odstranit skupinu a vytvořte novou.
    
    - **Typ členství.** Změníte typ členství. Další informace o různých typech dostupné členy najdete v tématu [jak: Vytvoření základní skupiny a přidání členů pomocí portálu Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
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
