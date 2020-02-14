---
title: Uživatelé a skupiny v zásadách podmíněného přístupu – Azure Active Directory
description: Kdo jsou uživatelé a skupiny v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192126"
---
# <a name="conditional-access-users-and-groups"></a>Podmíněný přístup: uživatelé a skupiny

Zásady podmíněného přístupu musí zahrnovat přiřazení uživatele jako jeden ze signálů v rámci procesu rozhodování. Uživatele můžete zahrnout nebo vyloučit ze zásad podmíněného přístupu. 

![Uživatel jako signál v rozhodnutích učiněných podmíněným přístupem](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Zahrnutí uživatelů

Tento seznam uživatelů obvykle zahrnuje všechny uživatele, kterým organizace cílí v zásadách podmíněného přístupu. 

Při vytváření zásad podmíněného přístupu jsou k dispozici následující možnosti:

- Žádná
   - Nejsou vybráni žádní uživatelé.
- Všichni uživatelé
   - Všichni uživatelé, kteří existují v adresáři včetně hostů v B2B.
- Vybrat uživatele a skupiny
   - Všichni uživatelé typu Host a externí uživatelé (Preview)
      - Tato volba zahrnuje všechny hosty a externí uživatele B2B včetně všech uživatelů s atributem `user type` nastaveným na `guest`. Tento výběr platí také pro všechny přihlášené externí uživatele z jiné organizace, jako je například poskytovatel Cloud Solution Provider (CSP). 
   - Role adresáře (Preview)
      - Umožňuje správcům vybrat konkrétní role adresáře Azure AD, které se používají k určení přiřazení. Organizace můžou například vytvořit přísnější zásadu pro uživatele, kteří mají přiřazenou roli globálního správce.
   - Uživatelé a skupiny
      - Umožňuje zaměřit se na konkrétní skupiny uživatelů. Organizace můžou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybraná aplikace pro HR. Skupina může být libovolný typ skupiny v Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce.

## <a name="exclude-users"></a>Vyloučení uživatelů

Vyloučení se běžně používají pro nouzový přístup nebo pro účty pro rozbití. Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, najdete v následujících článcích: 

* [Správa účtů pro nouzový přístup v Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)

Při vytváření zásad podmíněného přístupu je k dispozici možnost vyloučit následující možnosti.

- Všichni uživatelé typu Host a externí uživatelé (Preview)
   - Tato volba zahrnuje všechny hosty a externí uživatele B2B včetně všech uživatelů s atributem `user type` nastaveným na `guest`. Tento výběr platí také pro všechny přihlášené externí uživatele z jiné organizace, jako je například poskytovatel Cloud Solution Provider (CSP). 
- Role adresáře (Preview)
   - Umožňuje správcům vybrat konkrétní role adresáře Azure AD, které se používají k určení přiřazení. Organizace můžou například vytvořit přísnější zásadu pro uživatele, kteří mají přiřazenou roli globálního správce.
- Uživatelé a skupiny
   - Umožňuje zaměřit se na konkrétní skupiny uživatelů. Organizace můžou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybraná aplikace pro HR. Skupina může být libovolný typ skupiny v Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce.

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
