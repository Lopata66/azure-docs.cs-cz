---
title: Seznam zamítnout přiřazení pro prostředky Azure pomocí rozhraní REST API – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit seznam zamítnout přiřazení pro uživatele, skupiny nebo aplikace pomocí řízení přístupu na základě role (RBAC) pro prostředky Azure a rozhraní REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118289"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Seznam zamítnout přiřazení pro prostředky Azure pomocí rozhraní REST API

[Zamítnout přiřazení](deny-assignments.md) zablokuje uživatelům možnost provádět akce konkrétních prostředků Azure i v případě přiřazení role uděluje přístup. Tento článek popisuje, jak zobrazit seznam zamítnout přiřazení pomocí rozhraní REST API.

> [!NOTE]
> Nelze přímo vytvořit vlastní zamítnout přiřazení. Informace o tom, zakáže se vytvoří přiřazení, najdete v části [zamítnout přiřazení](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete získat informace o přiřazení odepřít, musíte mít:

- `Microsoft.Authorization/denyAssignments/read` oprávnění, která je obsažena ve většině [předdefinované role pro prostředky Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Seznam jedné zamítnout přiřazení

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam přiřazení odepřít.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Nahraďte *{odepřít id přiřazení}* s identifikátorem Odepřít přiřazení, které chcete načíst.

## <a name="list-multiple-deny-assignments"></a>Seznam více zamítnout přiřazení

1. Začněte s jedním z následujících požadavků:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    S volitelnými parametry:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam přiřazení odepřít.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu přiřazení odepřít.

    | Filtr | Popis |
    | --- | --- |
    | (žádný filtr) | Seznam všech zamítnout přiřazení na výše a níže zadaném oboru. |
    | `$filter=atScope()` | Seznam zamítnout přiřazení pro zadaný obor a vyšší. Nezahrnuje přiřazení odepřít v subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Seznam zamítnout přiřazení se zadaným názvem. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Seznam zamítnout přiřazení v kořenovém oboru (/)

1. Zvýšení úrovně přístupu, jak je popsáno v [zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory](elevate-access-global-admin.md).

1. Pomocí následující žádosti:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu přiřazení odepřít. Filtr je povinný.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Seznam zamítnout přiřazení kořenového oboru. Nezahrnuje přiřazení odepřít v subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Seznam zamítnout přiřazení se zadaným názvem. |

1. Odeberte přístup se zvýšeným oprávněním.

## <a name="next-steps"></a>Další postup

- [Vysvětlení zamítnout přiřazení pro prostředky Azure](deny-assignments.md)
- [Zvýšení úrovně přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
