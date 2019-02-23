---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure IaaS replikovaných do sekundární oblasti Azure pro účely zotavení po havárii pomocí služby Azure Site Recovery
description: Zjistěte, jak pomocí služby Azure Site Recovery provést převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure replikovaných do sekundární oblasti Azure pro účely zotavení po havárii.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46dae28fd6c9eaa3d5e03f5f06c5e92449653679
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737718"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Převzetí služby při selhání a navrácení služby po obnovení pro virtuální počítače Azure mezi oblastmi Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

Tento kurz popisuje, jak provést převzetí služeb při selhání jednoho virtuálního počítače Azure do sekundární oblasti Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do primární oblasti, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Azure
> * Znovunastavení ochrany sekundárního virtuálního počítače Azure, aby se replikoval do primární oblasti
> * Navrácení služeb po obnovení sekundárního virtuálního počítače
> * Znovunastavení ochrany primárního virtuálního počítače do sekundární oblasti

> [!NOTE]
> Cílem tohoto kurzu je provést uživatele postupem převzetí služeb při selhání do cílové oblasti a zpět s minimálním přizpůsobením. Pokud se chcete dozvědět více o různých aspektech souvisejících s převzetím služeb při selhání, včetně důležitých aspektů sítí, automatizace nebo řešení potíží, prostudujte si dokumenty v části Postupy pro virtuální počítače Azure.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončením [postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) zkontrolovali, že vše funguje podle očekávání.
- Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače. Virtuální počítač musí splňovat [požadavky Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Spuštění převzetí služeb při selhání do sekundární oblasti

1. V části **Replikované položky** vyberte virtuální počítač, u kterého chcete provést převzetí služeb při selhání, a vyberte **Převzít služby při selhání**.

   ![Převzetí služeb při selhání](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:

   * **Nejnovější** (výchozí): Tato možnost zpracuje všechna data ve službě Site Recovery a poskytuje nejnižší cíl bodu obnovení (RPO).
   * **Nejnovější zpracovaný**: Tato možnost vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery.
   * **Vlastní**: Tuto možnost použijte k převzetí služeb při selhání do konkrétnímu bodu obnovení. Tato možnost je užitečná při provádění testovacího převzetí služeb při selhání.

3. Vyberte možnost **Před spuštěním převzetí služeb při selhání vypnout počítač**, pokud chcete, aby se služba Site Recovery pokusila před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Všimněte si, že Site Recovery nepodporuje vyčištění zdroj po převzetí služeb při selhání.

4. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

5. Po převzetí služeb při selhání ověřte virtuální počítač tím, že se k němu připojíte. Pokud chcete u virtuálního počítače přejít k jinému bodu obnovení, můžete použít možnost **Změnit bod obnovení**.

6. Jakmile budete spokojeni s virtuálním počítačem, u kterého došlo k převzetí služeb při selhání, můžete převzetí služeb při selhání **Potvrdit**.
   Potvrzením dojde k odstranění všech bodů obnovení, které jsou ve službě k dispozici. Možnost **Změnit bod obnovení** už nebude dostupná.

## <a name="reprotect-the-secondary-vm"></a>Znovunastavení ochrany sekundárního virtuálního počítače

Po převzetí služeb při selhání virtuálního počítače pro něj musíte znovu nastavit ochranu, aby se replikoval zpět do primární oblasti.

1. Ujistěte se, že je virtuální počítač ve stavu **Převzetí služeb při selhání potvrzeno**, zkontrolujte dostupnost primární oblasti a ověřte, že v ní můžete vytvářet nové prostředky a přistupovat k nim.
2. V části **Trezor** > **Replikované položky** klikněte pravým tlačítkem na virtuální počítač, u kterého došlo k převzetí služeb při selhání, a pak vyberte **Znovu nastavit ochranu**.

   ![Kliknutí pravým tlačítkem a znovunastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Všimněte si, že je již vybraný směr ochrany ze sekundární do primární oblasti.
3. Zkontrolujte informace o **skupině prostředků, síti, úložišti a skupinách dostupnosti**. Všechny prostředky označené jako (nové) se vytvoří v rámci operace znovunastavení ochrany.
4. Kliknutím na **OK** aktivujte úlohu znovunastavení ochrany. Tato úloha přidá do cílové lokality nejnovější data. Pak replikuje rozdíly do primární oblasti. Virtuální počítač je teď v chráněném stavu.

> [!NOTE]
> Zobrazit ["jak" část](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) další podrobnosti o pracovní postup opětovného nastavování ochrany a co se stane během opětovného nastavování ochrany.


## <a name="fail-back-to-the-primary-region"></a>Navrácení služeb po obnovení do primární oblasti

Po znovunastavení ochrany virtuálních počítačů, můžete navrátit služby zpět do primární oblasti pro potřeby. Provedete to tak, nastavení převzetí služeb při selhání ze sekundární oblasti do primární oblasti, jak je popsáno v tomto článku.

![Kliknutí pravým tlačítkem a znovunastavení ochrany](./media/azure-to-azure-tutorial-failover-failback/failback.png)

Pokud vidíte na předchozím snímku obrazovky "ContosoWin2016" virtuálního počítače při selhání z USA (střed) – Východ USA a se nezdařilo obnovení z USA – Východ USA (střed).

Převzetí služeb při vypnutí virtuálního počítače v sekundární oblasti, to znamená, oblasti pro zotavení po havárii a vytvoří a spustí virtuální počítač v primární oblasti. Prosím **Poznámka** , zotavení po Havárii virtuálních počítačů zůstanou ve stavu přidělení, jak je znázorněno výše. Toto chování je záměrné, protože Azure Site Recovery ukládá informace virtuálního počítače, které mohou být užitečné při převzetí služeb při selhání pro primární do sekundární oblasti později. Se vám neúčtují poplatky pro uvolnění virtuální počítače, abyste je měli uchovávat.
