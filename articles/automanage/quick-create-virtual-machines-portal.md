---
title: Rychlý Start – povolení automatického spravování Azure pro virtuální počítače v Azure Portal
description: Zjistěte, jak rychle povolit automanage u virtuálních počítačů na novém nebo existujícím virtuálním počítači v Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 69f43b626bb50171ceaca1b7a8761bec040d1dd6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897224"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Rychlý Start: povolení služby Azure automanage pro virtuální počítače v Azure Portal

Začínáme s Azure automanage pro virtuální počítače pomocí Azure Portal pro povolení automatizované správy na novém nebo existujícím virtuálním počítači.


## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

> [!IMPORTANT]
> Abyste mohli povolit autosprávu pomocí stávajícího účtu pro správu, musíte mít ve skupině prostředků, která obsahuje vaše virtuální počítače, roli **Přispěvatel** . Pokud povolujete možnost automanage pomocí nového účtu pro autosprávu, budete potřebovat následující oprávnění: role **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatelů** v rámci vašeho předplatného.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Povolení automanage pro virtuální počítače na existujícím virtuálním počítači

1. Na panelu hledání vyhledejte a vyberte možnost **automanage – osvědčené postupy pro virtuální počítače Azure** .

2. Vyberte **Povolit na stávajícím virtuálním počítači** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

3. V okně **Vybrat počítače** :
    1. Vyfiltrujte seznam virtuálních počítačů podle vašeho **předplatného** a **skupiny prostředků** .
    1. Zaškrtněte políčko u každého virtuálního počítače, který chcete připojit.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

4. V části **konfigurační profil** klikněte na **Procházet a změňte profily a předvolby** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

5. V okně **vybrat konfigurační profil + Předvolby** :
    1. Vyberte profil vlevo: *vývoj/testování* pro testování, výrobní *zakázka* pro produkci.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

6. Klikněte na tlačítko **Povolit** .


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Povolení automanage pro virtuální počítače na novém virtuálním počítači

Pokud chcete vytvořit nový virtuální počítač a povolit možnost automanage, přihlaste [se k Azure Portal](https://aka.ms/automanageportalnextstep) .

1. Postupujte podle kroků pro vytvoření v [rychlém startu – vytvoření virtuálního počítače s Windows v Azure Portal](..\virtual-machines\windows\quick-create-portal.md).

2. Po nasazení virtuálního počítače na stránce Stav nasazení budete mít na konci doporučené **Další kroky** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

3. V části **Další kroky** vyberte možnost **Povolit autospravovat osvědčené postupy pro virtuální počítače** .

4. Na stránce s **osvědčenými postupy pro automatickou správu – Azure Virtual Machine Best Practices** se **počítače** automaticky naplní nově vytvořeným virtuálním počítačem.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

5. V části **konfigurační profil** klikněte na **Procházet a změňte profily a předvolby** .

6. V okně **vybrat konfigurační profil + Předvolby** :
    1. Vyberte profil vlevo: *vývoj/testování* pro testování, výrobní *zakázka* pro produkci.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

7. Klikněte na tlačítko **Povolit** .

## <a name="disable-automanage-for-vms"></a>Zakázat pro virtuální počítače automanage

Rychlé ukončení používání služby Azure automanage pro virtuální počítače zakázáním automatizované správy.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

1. Navštivte stránku pro automatické spravování **– osvědčené postupy pro virtuální počítače Azure** se seznamem všech vašich automatických spravovaných virtuálních počítačů.
1. Zaškrtněte políčko vedle virtuálního počítače, který chcete zakázat.
1. Klikněte na tlačítko **Zakázat automanagent** .
1. Než se odsouhlaste s **zakázáním** , důkladně si přečtěte zprávy ve výsledném okně.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků, abyste si vyzkoušeli službu Azure automanage pro virtuální počítače a už ji nepotřebujete, můžete odstranit skupinu prostředků. Odstraněním skupiny se odstraní taky virtuální počítač a všechny prostředky ve skupině prostředků.

Azure automanage vytvoří výchozí skupiny prostředků pro ukládání prostředků v. Ověřte skupiny prostředků, které mají konvence pojmenování "DefaultResourceGroupRegionName" a "AzureBackupRGRegionName" pro vyčištění všech prostředků.

1. Vyberte **skupinu prostředků** .
1. Na stránce skupiny prostředků vyberte **Odstranit** .
1. Po zobrazení výzvy potvrďte název skupiny prostředků a pak vyberte **Odstranit** .


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste povolili Azure automanage pro virtuální počítače. 

Zjistěte, jak můžete vytvářet a používat vlastní předvolby při povolování automatické správy na virtuálním počítači. 

> [!div class="nextstepaction"]
> [Azure automanage pro virtuální počítače – vlastní konfigurační profil](virtual-machines-custom-preferences.md)
