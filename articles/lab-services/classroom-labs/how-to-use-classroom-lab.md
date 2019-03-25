---
title: Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu budete přistupovat k virtuálním počítačům v testovacím prostředí v učebně, kterou nastavil vyučující.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 8e20f612bc54433091036377d51a7e59e3abec51
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402146"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak získat přístup k testovacímu prostředí v učebně, připojit se k virtuálnímu počítači v daném testovacím prostředí a zastavit daný virtuální počítač. 

## <a name="register-to-a-lab"></a>Zaregistrujte se a testovacího prostředí
1. Přejděte na **adresu URL pro registraci**, kterou jste dostali od vyučujícího. 
2. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 
3. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
2. Počkejte, až bude virtuální počítač připravený, a pak ho **spusťte**. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Zobrazit všechna testovací prostředí v učebně
Až dokončíte registraci na tato praktická cvičení, můžete zobrazit všechny testovací prostředí v učebnách provedením následujících kroků: 

1. Přejděte do [ https://labs.azure.com ](https://labs.azure.com). 
2. Přihlaste se ke službě pomocí uživatelského účtu, který jste použili k registraci do testovacího prostředí. 
3. Ověřte, že se všechny testovací prostředí, ke kterým máte přístup k. 

    ![Zobrazit všechna testovací prostředí](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Připojení k virtuálnímu počítači v testovacím prostředí v učebně

1. Spuštění virtuálního počítače, pokud ještě není spuštěná, vyberte **Start** na dlaždici. 
2. Na dlaždici, která představuje virtuální počítač v testovacím prostředí, ke kterému chcete získat přístup, vyberte **Připojit**. 
3. Proveďte jednu z následujících kroků: 
   1. Pro **Windows** ukládat virtuální počítače, **RDP** soubor na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. Použití **uživatelské jméno** a **heslo** získáte od educator/profesor se přihlaste k počítači. 
   3. Pro **Linux** virtuální počítače, zkopírujte a uložte připojovací řetězec SSH na **připojit k virtuálnímu počítači** dialogové okno. Použít tento připojovací řetězec z terminálu SSH (například [Putty](https://www.putty.org/)) pro připojení k virtuálnímu počítači.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Zastavení virtuálního počítače v testovacím prostředí v učebně

Chcete-li zastavit virtuální počítač, vyberte **Zastavit** na dlaždici. Po zastavení virtuálního počítače se na dané dlaždici zpřístupní tlačítko **Spustit**. 

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
 