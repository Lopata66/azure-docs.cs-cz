---
title: 'Kurz: udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a Azure Portal '
description: V tomto kurzu se dozvíte, jak udělit uživateli přístup k prostředkům Azure pomocí řízení přístupu na základě role (RBAC) v Azure Portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: 6281c9a1818a3b46ac4f7a62f8ae76668db56887
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138089"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Kurz: udělení přístupu uživatele k prostředkům Azure pomocí RBAC a Azure Portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. V tomto kurzu udělíte uživateli přístup k vytváření a správě virtuálních počítačů ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v oboru skupiny prostředků
> * Odebrání přístupu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. V navigačním seznamu klikněte na **skupiny prostředků**.

1. Kliknutím na **Přidat** otevřete okno **Skupina prostředků** .

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Jako **název skupiny prostředků**zadejte **RBAC-Resource-Group**.

1. Vyberte předplatné a umístění.

1. Kliknutím na **vytvořit** vytvořte skupinu prostředků.

1. Kliknutím na **aktualizovat** aktualizujte seznam skupin prostředků.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. V seznamu **skupin prostředků**klikněte na novou skupinu prostředků **RBAC-Resource-Group** .

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **přiřazení rolí** zobrazíte aktuální seznam přiřazení rolí.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kliknutím na **přidat** > **Přidat přiřazení role** otevřete podokno přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidat podokno přiřazení role](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Kliknutím na **Uložit** vytvořte přiřazení role.

   Po chvíli se uživateli přiřadí role Přispěvatel virtuálního počítače v oboru skupin prostředků RBAC-Resource-Group.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle uživatele s rolí Přispěvatel virtuálních počítačů.

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

## <a name="clean-up"></a>Vyčištění

1. V navigačním seznamu klikněte na **skupiny prostředků**.

1. Kliknutím na **RBAC-Resource-Group** otevřete skupinu prostředků.

1. Odstraňte skupinu prostředků kliknutím na **Odstranit skupinu prostředků** .

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. V okně jste si jisti, že **chcete odstranit** okno, zadejte název skupiny prostředků: **RBAC-Resource-Group**.

1. Kliknutím na **Odstranit** odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí RBAC a Azure PowerShell](tutorial-role-assignments-user-powershell.md)
