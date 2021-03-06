---
title: Udělení přístupu na základě role uživatelům
titleSuffix: Azure Maps
description: Udělení autorizace uživatelům pomocí řízení přístupu na základě role Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895561"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Udělení přístupu na základě role uživatelům Azure Maps

*Řízení přístupu na základě role Azure (Azure RBAC)* udělíte pomocí skupiny Azure AD nebo objektů zabezpečení pro jednu nebo víc Azure Maps definic rolí. Pokud chcete zobrazit definice rolí Azure, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)** . Vyberte **role** a potom vyhledejte role, které začínají na *Azure Maps* .

* Pokud chcete efektivně spravovat velké množství přístupu uživatelů k Azure Maps, přečtěte si téma [skupiny Azure AD](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Aby se uživatelé mohli k aplikaci ověřit, musí se v Azure AD vytvořit. Viz téma [Přidání nebo odstranění uživatelů pomocí Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Další informace najdete v tématu [Azure AD](../../active-directory/fundamentals/index.yml) , abyste mohli efektivně spravovat adresář pro uživatele.

1. Přejít na **účet Azure Maps** . Vyberte přiřazení role **řízení přístupu (IAM)**  >  **Role assignment** .

    ![Udělení přístupu pomocí Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na kartě **přiřazení rolí** v části **Role** vyberte předdefinované Azure Maps definice role, jako je **Azure Maps data Reader** nebo **Azure Maps Přispěvatel dat** . V části **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD** . Vyberte objekt zabezpečení podle názvu. Pak vyberte **Uložit** .

   * Podívejte se na podrobnosti o [Přidání nebo odebrání přiřazení rolí](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps předdefinované definice rolí poskytují velmi velký přístup k mnoha Azure Mapsám rozhraní REST API. Chcete-li omezit rozhraní API pro uživatele na minimum, přečtěte si téma [Vytvoření vlastní definice role a přiřazení uživatelů](../../role-based-access-control/custom-roles.md) k definici vlastní role. Umožníte tak uživatelům, aby pro aplikaci měli k dispozici nejnižší oprávnění.