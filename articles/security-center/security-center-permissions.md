---
title: Oprávnění ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak Azure Security Center používá řízení přístupu na základě rolí k přiřazení oprávnění pro uživatele a identifikuje povolené akce pro každou roli.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905615"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění ve službě Azure Security Center

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí jenom informace související s prostředku, když máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Čtenář zabezpečení**: Zobrazení oprávnění ke službě Security Center má uživatel, který patří do této role. Uživatel můžete zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny.
* **Správce zabezpečení**: Uživatel, který patří do této role má stejná práva jako čtenář zabezpečení a můžete taky aktualizovat zásady zabezpečení a zavřít upozornění a doporučení.

> [!NOTE]
> Role zabezpečení, čtenář zabezpečení a správce zabezpečení, mají přístup pouze ve službě Security Center. Role zabezpečení nemají přístup k jiným oblastem služeb Azure, jako je úložiště, webové a mobilní telefon nebo Internet of Things.
>
>

## <a name="roles-and-allowed-actions"></a>Rolí a povolených akcí

Následující tabulka zobrazuje role a povolené akce ve službě Security Center. X označuje, že tato akce je povolena pro tuto roli.

| Role | Upravit zásady zabezpečení | Použití doporučení zabezpečení pro určitý prostředek | Zavřít upozornění a doporučení | Zobrazení výstrah a doporučení |
|:--- |:---:|:---:|:---:|:---:|
| Vlastník předplatného | X | X | X | X |
| Přispěvatel předplatného | -- | X | X | X |
| Vlastník skupiny prostředků | -- | X | -- | X |
| Přispěvatelem skupiny prostředků | -- | X | -- | X |
| Čtenář | -- | -- | -- | X |
| Správce zabezpečení | X | -- | X | X |
| Čtenář zabezpečení | -- | -- | -- | X |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Například přiřadíte role Čtenář pro uživatele, kteří potřebují jenom zobrazovat informace o stav zabezpečení prostředku, ale nemusí provádět žádné kroky, třeba uplatňovat doporučení nebo upravovat zásady.
>
>

## <a name="next-steps"></a>Další postup
Tento článek je vysvětleno, jak Security Center používá RBAC pro přiřazení oprávnění pro uživatele a identifikovat povolené akce pro každou roli. Teď, když jste obeznámeni s přiřazení rolí, které jsou potřeba k monitorování stavu zabezpečení vašeho předplatného, upravit zásady zabezpečení a doporučení uplatnit, zjistěte, jak:

- [Nastavení zásad zabezpečení ve službě Security Center](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Security Center](security-center-recommendations.md)
- [Monitorování stavu zabezpečení vašich prostředků Azure](security-center-monitoring.md)
- [Správa a reakce na výstrahy zabezpečení ve službě Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorování partnerských řešení zabezpečení](security-center-partner-solutions.md)
