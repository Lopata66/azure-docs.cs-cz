---
title: Jak nakonfigurovat spravované identity pro prostředky Azure na Virtuálním počítači Azure pomocí webu Azure portal
description: Podrobné pokyny ke konfiguraci spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí webu Azure portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab0a5b021048f0f684473d3f54bbeadf870cd007
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445309"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač pomocí webu Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak můžete povolit nebo zakázat systému a uživatelsky přiřazené identity spravované pro virtuální počítač (Virtuálním počítači Azure), pomocí webu Azure portal. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak povolit a zakázat identitu spravované systém přiřadil pro virtuální počítač pomocí webu Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače

Chcete-li systém přiřadil spravovanou identitu virtuálního počítače při jeho vytváření, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

- V části **správu** kartu **Identity** části, přepněte **se identita spravované služby** k **na**.  

![Povolit systém přiřadil identitu během vytváření virtuálních počítačů](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Najdete v následujících šablon rychlý start k vytvoření virtuálního počítače: 

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Povolit systém přiřadil spravovaná identita na existující virtuální počítač

Aby systém přiřadil spravovaná identita na virtuálním počítači, který byl původně zřízený bez ní, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.

2. Přejděte na požadovaný virtuální počítač a vyberte **Identity**.

3. V části **přiřazenou systémem**, **stav**vyberte **na** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Odeberte systém přiřadil spravovaná identita z virtuálního počítače

Z virtuálního počítače odebrat systém přiřadil spravovanou identitu, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

Pokud máte virtuální počítač, který už musí systém přiřadil spravovaná identita:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. 

2. Přejděte na požadovaný virtuální počítač a vyberte **Identity**.

3. V části **přiřazenou systémem**, **stav**vyberte **vypnout** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživateli

 V této části se dozvíte, jak přidávat a odebírat spravované identity přiřazené uživateli z virtuálního počítače pomocí webu Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Přiřazení uživatelsky přiřazené identity během vytváření virtuálního počítače

K virtuálnímu počítači přiřadit uživatelsky přiřazené identity, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) přiřazení rolí. Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

Na webu Azure portal v současné době nepodporuje přiřazování uživatelsky přiřazené spravovanou identitu při vytváření virtuálního počítače. Místo toho odkazovat na jeden z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače a pak pokračujte k další části Podrobnosti o přiřazení spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Přiřadit uživateli přiřazena spravovanou identitu do existujícího virtuálního počítače

K virtuálnímu počítači přiřadit uživatelsky přiřazené identity, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) přiřazení rolí. Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli** a potom  **\+přidat**.

   ![Přidat spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klikněte na uživatelsky přiřazené identity chcete přidat do virtuálního počítače a pak klikněte na tlačítko **přidat**.

    ![Přidat spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Odeberte uživatelsky přiřazené spravovanou identitu z virtuálního počítače

Odebrání virtuálního počítače uživatelsky přiřazené identity, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role. Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli**, spravovat identitu, kterou chcete odstranit a potom klikněte na název přiřazený uživatel **odebrat** (klikněte na tlačítko  **Ano** v podokně potvrzení).

   ![Odeberte uživatelsky přiřazené identity spravované z virtuálního počítače](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Další postup

- Identity spravované virtuálního počítače Azure pomocí webu Azure portal, zadejte [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

