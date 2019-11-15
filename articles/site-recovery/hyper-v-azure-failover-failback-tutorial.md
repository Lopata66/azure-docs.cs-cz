---
title: Nastavení převzetí služeb při selhání a navrácení služeb po obnovení pro virtuální počítače Hyper-V Azure Site Recovery v
description: Zjistěte, jak provést převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V během zotavení po havárii do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8c197c2f0875bb31d091fb5839730ee1568b471
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082651"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovaných do Azure

Tento kurz popisuje, jak provést převzetí služeb při selhání virtuálního počítače Hyper-V do Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření vlastností virtuálního počítače Hyper-V a kontrola, že odpovídá požadavkům Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Navrácení služeb po obnovení z Azure do místní lokality
> * Zpětná replikace místních virtuálních počítačů a opětovné spuštění replikace do Azure

Tento kurz je pátou částí série. Předpokládá se, že jste už dokončili úlohy z předchozích kurzů.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavení zotavení po havárii pro [virtuální počítače Hyper-V](tutorial-hyper-v-to-azure.md) nebo [virtuální počítače Hyper-V spravované v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Ujistěte se, že virtuální počítač neobsahuje žádné snímky a že je místní virtuální počítač během navracení služeb po obnovení vypnutý. Pomáhá to zajistit konzistenci dat během replikace. Během navracení služeb po obnovení místní počítač nezapínejte. 

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve třech fázích:

1. **Převzetí služeb při selhání do Azure:** Proběhne převzetí služeb při selhání virtuálních počítačů Hyper-V z místní lokality do Azure.
2. **Navrácení služeb po obnovení do místní lokality:** Jakmile bude dostupná místní lokalita, proběhne převzetí služeb při selhání virtuálních počítačů Azure do místní lokality. Spustí se synchronizace dat z Azure do místní lokality a po dokončení se zprovozní virtuální počítače v místní lokalitě.  
3. **Zpětná replikace místních virtuálních počítačů:** Po navrácení služeb po obnovení do místní lokality se provede zpětná replikace místních virtuálních počítačů a spustí se jejich replikace do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před převzetím služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že splňuje [požadavky Azure](hyper-v-azure-support-matrix.md#replicated-vms).

V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

1. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

1. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a nastavení spravovaného disku.

1. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

1. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření převzetí služeb při selhání klikněte na **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Navrácení služeb po obnovení virtuálního počítače Azure do místní lokality a zpětná replikace místního virtuálního počítače

Operace navrácení služeb po obnovení je v podstatě převzetí služeb při selhání z Azure do místní lokality a zpětná replikace znovu spustí replikaci virtuálních počítačů z místní lokality do Azure.

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Plánované převzetí služeb při selhání**.
2. V části **Potvrdit plánované převzetí služeb při selhání** ověřte směr převzetí služeb při selhání (z Azure) a vyberte zdrojové a cílové umístění.
3. Vyberte **Před převzetím služeb při selhání synchronizovat data (synchronizovat pouze rozdílové změny)** . Tato možnost minimalizuje výpadky virtuálních počítačů, protože se synchronizace provedete bez vypnutí virtuálního počítače.
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
5. Jakmile po dokončení počáteční synchronizace dat budete připraveni vypnout virtuální počítače Azure, klikněte na **Úlohy** > název_úlohy_plánovaného_převzetí_služeb_při_selhání > **Dokončit převzetí služeb při selhání**. Tím se vypne virtuální počítač Azure, nejnovější změny se přenesou do místní lokality a spustí se místní virtuální počítač.
6. Přihlaste se k místnímu virtuálnímu počítači a ověřte, že je dostupný podle očekávání.
7. Místní virtuální počítač je teď ve stavu **Čekání na potvrzení**. Klikněte na **Potvrdit**. Tím se odstraní virtuální počítač Azure a jeho disky a místní virtuální počítač se připraví na zpětnou replikaci.
Pokud chcete spustit replikaci místního virtuálního počítače do Azure, povolte **zpětnou replikaci**. Tím se aktivuje replikace rozdílových změn, ke kterým došlo od vypnutí virtuálního počítače Azure.  
