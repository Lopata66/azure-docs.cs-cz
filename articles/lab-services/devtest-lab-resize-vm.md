---
title: Změna velikosti virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak změnit velikost virtuálního počítače ve službě Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: a0bc618a9c0a02aae884d8be359df6bdbf4c0d2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868062"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Změna velikosti virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs
Mezi důležité funkce virtuálních počítačů Azure je, že umožňuje změnit velikost virtuálního počítače (VM) na základě vašich potřeb pro procesor, síť nebo výkon disku. Azure DevTest Labs podporuje tuto funkci pro virtuální počítače v testovacím prostředí teď. Funkce změny velikosti dodržuje zásady testovacího prostředí pro povolené velikosti virtuálních počítačů v testovacím prostředí. To znamená můžete změnit velikost virtuálního počítače na pouze povolené velikosti v testovacím prostředí. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Postup pro změnu velikosti virtuálního počítače v testovacím prostředí 
Změna velikosti virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs, proveďte následující kroky: 

> [!NOTE]
> Pokud jste připojeni k virtuálnímu počítači prostřednictvím relace vzdálené plochy (RDP), uložte si práci a odpojte od virtuálního počítače před jejich velikosti.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte testovací prostředí, která obsahuje virtuální počítač, kterého chcete změnit velikost.  
4. Na levém panelu, vyberte **Moje Virtual Machines**. 
5. Ze seznamu virtuálních počítačů vyberte virtuální počítač.
6. Vyberte **Zastavit** na panelu nástrojů, pokud je virtuální počítač spuštěný. Kontrola stavu operace **oznámení** okna. Počkejte, dokud je virtuální počítač zastavený a ukončete **oznámení** okna. 

    ![Zastavení virtuálního počítače](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stránce virtuální počítač pro virtuální počítač vyberte **velikost** pod **nastavení** v levé nabídce.

    ![Velikost nabídky](media/devtest-lab-resize-vm/size-menu.png)
1. V **zvolte velikost** Procházet a vyberte velikost virtuálního počítače a klikněte na tlačítko **vyberte**.     
1. Kontrola stavu operace změny velikosti v **oznámení** okna.

    ![Změnit velikost stavu](media/devtest-lab-resize-vm/resize-status.png)
10. Po změně velikosti operace skončí úspěšně, zavřete **oznámení** okna. 
11. Vyberte **přehled** v levé nabídce a vyberte **restartovat** na panelu nástrojů a restartujte virtuální počítač. 

## <a name="next-steps"></a>Další postup
Podrobné informace o funkci změny velikosti podporovaných virtuálních počítačích Azure najdete v tématu [Změna velikosti virtuálních počítačů](https://azure.microsoft.com/blog/resize-virtual-machines/).


