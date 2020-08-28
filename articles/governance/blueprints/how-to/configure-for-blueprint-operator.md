---
title: Nastavení prostředí pro operátor podrobného plánu
description: Přečtěte si, jak nakonfigurovat prostředí Azure pro použití s integrovanou rolí operátora podrobného plánu Azure.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ef911553b4985a6497fb397a1ae405387c6539fb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051453"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurace prostředí pro operátora podrobného plánu

Správa definic podrobných plánů a přiřazení podrobného plánu se dá přiřadit k různým týmům. Je běžné, že architekt nebo tým zásad správného řízení bude zodpovědný za správu životního cyklu vašich definicí podrobného plánu, zatímco provozní tým zodpovídá za správu přiřazení těchto centrálně kontrolovaných definic podrobných plánů.

**Operátor** podrobného plánu řízení přístupu na základě role (RBAC) je navržený speciálně pro použití v tomto typu scénáře. Role umožňuje týmům typu operací spravovat přiřazení definic podrobného plánu, ale ne možnost je upravovat. To vyžaduje určitou konfiguraci v prostředí Azure a tento článek popisuje nezbytné kroky.

## <a name="grant-permission-to-the-blueprint-operator"></a>Udělení oprávnění k operátoru podrobného plánu

Prvním krokem je udělení role **operátora** podrobného plánu na účet nebo skupinu zabezpečení (doporučeno), která bude přiřazovat modrotisky. Tato akce by se měla provést na nejvyšší úrovni v hierarchii skupiny pro správu, která zahrnuje všechny skupiny pro správu a odběry, ke kterým má provozní tým přístup v přiřazení podrobného plánu. Při udělování těchto oprávnění se doporučuje postupovat podle principu minimálního oprávnění.

1. Doporučil [Vytvoření skupiny zabezpečení a přidání členů](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Přidání přiřazení role](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operátora** podrobného plánu k účtu nebo skupině zabezpečení

## <a name="user-assign-managed-identity"></a>Přiřazení spravované identity uživatelem

Definice podrobného plánu může používat spravované identity přiřazené systémem nebo uživatelem. Při použití role **operátoru** podrobného plánu ale musí být definice podrobného plánu nakonfigurovaná tak, aby používala uživatelsky přiřazenou spravovanou identitu. Kromě toho musí být účtu nebo skupině zabezpečení udělená role **operátora** podrobného plánu udělena role **operátora** podrobného identity na spravované identitě přiřazené uživatelem. Bez tohoto oprávnění se přiřazení podrobného plánu nezdařila z důvodu nedostatku oprávnění.

1. [Vytvoření spravované identity přiřazené uživatelem](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) pro použití přiřazeným podrobným plánem

1. [Přidejte přiřazení role](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **spravovaného operátoru identity** k účtu nebo skupině zabezpečení. Určení rozsahu přiřazení role k nové uživatelsky přiřazené spravované identitě.

1. Jako **operátor**podrobného plánu [přiřaďte podrobný plán](../create-blueprint-portal.md#assign-a-blueprint) , který používá novou spravovanou identitu přiřazenou uživatelem.

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)