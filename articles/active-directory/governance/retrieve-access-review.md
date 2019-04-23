---
title: Načíst výsledky kontroly přístupu pro skupiny a aplikace v kontroly přístupu – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak načíst výsledky kontroly přístupu pro členy skupiny nebo přístupu k aplikacím v kontrol přístupu Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae4bafb3eefcee2785c784030d7be8dde66988e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245827"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>Načíst kontroly přístupu pro skupiny a aplikace ve službě Azure AD výsledky kontroly přístupu

Správci můžou pomocí Azure Active Directory (Azure AD) [vytvořit kontrolu přístupu](create-access-review.md) pro členy skupiny nebo uživatele přiřazené k aplikaci.  Uživatel, který je v **globálního správce**, **Správce uživatelů**, **správce zabezpečení** nebo **Čtenář zabezpečení** role můžete také přečíst výsledky kontroly přístupu.  Přiřazení uživatelů k jedné z těchto rolí, správce privilegovaných rolí můžete použít Azure AD PIM do nastavit jako oprávněné aktivaci této role uživatele, nebo globální správce může trvale [přiřazení uživatele k roli](../fundamentals/active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Vyhledání kontroly přístupu

Pokud víte, který program obsahuje kontrolu přístupu, přejděte na stránku kontroly přístupu, vyberte **Programy** a vyberte program, který obsahuje ovládací prvek pro kontrolu přístupu.  Potom vyberte **Ovládací prvky** a vyberte ovládací prvek kontroly přístupu. Pokud v programu existuje mnoho ovládacích prvků, můžete vyfiltrovat ovládací prvky konkrétního typu a seřadit je podle jejich stavu. Můžete také hledat podle názvu ovládacího prvku kontroly přístupu nebo zobrazovaného názvu vlastníka, který ho vytvořil. 

Pokud nevíte, který program obsahuje kontrolu přístupu, přejděte na stránku kontrol přístupu a vyberte **Ovládací prvky**.  Můžete vyfiltrovat ovládací prvky konkrétního typu a seřadit je podle jejich stavu a také můžete hledat podle názvu ovládacího prvku kontroly přístupu nebo zobrazovaného názvu vlastníka, který ho vytvořil. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Načítání výsledků pro jednorázovou kontrolu přístupu

Pokud typ opakování kontroly je jednou, potom se průběh aktivní kontroly přístupu a výsledky dokončené kontroly přístupu dají získat v oddíle **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje, a zobrazit tak jenom přístup tohoto uživatele.  Pokud chcete načíst všechny výsledky dokončené kontroly přístupu, klikněte na tlačítko **Stáhnout**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Načítání výsledků pro více instancí opakované kontroly přístupu

Pokud chcete zobrazit průběh aktivní kontroly přístupu, která se opakuje, klikněte na oddíl **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje.

Pokud chcete zobrazit výsledky dokončené instance kontroly přístupu, která se opakuje, vyberte **Historie kontrol** a potom vyberte konkrétní instanci ze seznamu dokončených instancí kontrol přístupu podle počátečního a koncového data instance.   Výsledky této instance jsou dostupné v oddíle **Výsledky**.  Můžete zadat zobrazovaný název uživatele nebo hlavní název uživatele (UPN), jehož přístup se kontroluje, a zobrazit tak jenom přístup tohoto uživatele.  Pokud chcete načíst všechny výsledky dokončené instance opakující se kontroly přístupu, klikněte na tlačítko **Stáhnout**.


## <a name="removing-users-from-an-access-review"></a>Odebírání uživatelů z kontroly přístupu

Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure Active Directory odstraněný na 30 dní, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se následně data o tomto uživateli z aktivních kontrol přístupu.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="next-steps"></a>Další postup

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](manage-guest-access-with-access-reviews.md)
- [Správa programů a ovládacích prvků pro kontroly přístupu Azure AD](manage-programs-controls.md)
- [Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)


