---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160290"
---
## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
1. Vyberte virtuální počítač, kterou chcete znovu nasadit a pak vyberte *znovu nasadit* tlačítko *nastavení* okno. Budete muset posunout dolů viz **podpora a řešení potíží** části, která obsahuje tlačítko "Znovu nasadit" jako v následujícím příkladu:
   
    ![Okno virtuálního počítače Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Chcete-li potvrdit operaci, vyberte *znovu nasadit* tlačítka:
   
    ![Opětovné nasazení virtuálního počítače okno](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stav** virtuálního počítače se změní na *aktualizace* jako připraví virtuální počítač znovu nasadit, jak je znázorněno v následujícím příkladu:
   
    ![Aktualizuje se virtuální počítač](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stav** pak změní na *počáteční* jako virtuální počítač se spustí na nového hostitele Azure, jak je znázorněno v následujícím příkladu:
   
    ![Virtuální počítač se spouští.](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Po dokončení procesu spouštění virtuálního počítače **stav** vrátí do *systémem*, označující, virtuální počítač má se znovu úspěšně nasadil:
   
    ![Virtuální počítač je spuštěný.](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

