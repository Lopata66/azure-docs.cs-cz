---
title: Přidání přiřazení role pro spravovanou identitu (Preview) – Azure RBAC
description: Naučte se, jak přidat přiřazení role pomocí spravované identity a pak vybrat rozsah a roli pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122089"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Přidání přiřazení role pro spravovanou identitu (Preview)

Přiřazení rolí pro spravovanou identitu můžete přidat pomocí stránky **řízení přístupu (IAM)** , jak je popsáno v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md). Když použijete stránku řízení přístupu (IAM), začnete s oborem a pak vyberete spravovanou identitu a roli. Tento článek popisuje alternativní způsob přidání přiřazení rolí pro spravovanou identitu. Pomocí těchto kroků zahájíte spravovanou identitu a pak vyberete obor a roli.

> [!IMPORTANT]
> Přidání přiřazení role pro spravovanou identitu pomocí těchto alternativních kroků je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

Pomocí těchto kroků přiřaďte roli spravované identitě přiřazené systémem, a to od spravované identity.

1. V Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. V nabídce vlevo klikněte na možnost **Identita**.

    ![Spravovaná identita přiřazená systémem](./media/shared/identity-system-assigned.png)

1. V části **oprávnění** klikněte na **přiřazení rolí Azure**.

    Pokud jsou role už přiřazené k vybrané spravované identitě přiřazené systémem, zobrazí se seznam přiřazení rolí. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/shared/role-assignments-system-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

1. Klikněte na **Přidat přiřazení role (Preview)**.

1. Pomocí rozevíracích seznamů vyberte sadu prostředků, na které se přiřazení role vztahuje, jako je například **předplatné**, **Skupina prostředků** nebo prostředek.

    Pokud nemáte oprávnění k zápisu do přiřazení role pro vybraný obor, zobrazí se vložená zpráva. 

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

   ![Přidat podokno přiřazení role pro spravovanou identitu přiřazenou systémem](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se spravované identitě přiřadí role ve vybraném oboru.

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

Pomocí těchto kroků přiřadíte roli spravované identitě přiřazené uživateli, a to od spravované identity.

1. V Azure Portal otevřete uživatelem přiřazenou spravovanou identitu.

1. V nabídce vlevo klikněte na **přiřazení rolí Azure**.

    Pokud jsou už role přiřazené k vybrané spravované identitě přiřazené uživateli, zobrazí se seznam přiřazení rolí. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou uživatelem](./media/shared/role-assignments-user-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

1. Klikněte na **Přidat přiřazení role (Preview)**.

1. Pomocí rozevíracích seznamů vyberte sadu prostředků, na které se přiřazení role vztahuje, jako je například **předplatné**, **Skupina prostředků** nebo prostředek.

    Pokud nemáte oprávnění k zápisu do přiřazení role pro vybraný obor, zobrazí se vložená zpráva. 

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

   ![Přidat podokno přiřazení role pro uživatelem přiřazenou spravovanou identitu](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se spravované identitě přiřadí role ve vybraném oboru.

## <a name="next-steps"></a>Další kroky

- [Jaké jsou spravované identity prostředků Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](role-assignments-portal.md)
- [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
