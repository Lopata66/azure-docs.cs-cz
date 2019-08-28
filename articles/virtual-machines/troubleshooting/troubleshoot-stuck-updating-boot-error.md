---
title: Spuštění virtuálního počítače Azure je zablokované na web Windows Update | Microsoft Docs
description: Naučte se, jak tento problém vyřešit při zablokování spuštění virtuálního počítače Azure ve službě Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: b9a93448e084a42a935a8281ccd069e3604b1f18
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089618"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Spuštění virtuálního počítače Azure je zablokované na Windows Update.

Tento článek pomáhá vyřešit problém, když se váš virtuální počítač zablokuje ve fázi web Windows Update během spouštění. 

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje použití modelu nasazení Správce prostředků. Tento model doporučujeme použít pro nová nasazení namísto použití modelu nasazení Classic.

## <a name="symptom"></a>Příznak

 Virtuální počítač s Windows se nespustí. Když zkontrolujete snímky obrazovky v okně [diagnostiky spouštění](../troubleshooting/boot-diagnostics.md) , uvidíte, že spuštění je zablokované v procesu aktualizace. Následují příklady zpráv, které můžete obdržet:

- Instalace Windows # #% vypne počítač. Tím dojde k tomu, že se Váš počítač několikrát restartuje.
- Nechte počítač zapnutý, dokud neproběhne. Probíhá instalace aktualizace #... 
- Nepovedlo se nám dokončit aktualizace, protože se změny nevypnou z počítače.
- Chyba při konfiguraci vrácení změn aktualizací Windows nevypne počítač
- Chyba < kód chyby > provádění operací aktualizace # # # # # z # # # # # (\Regist...)
- Závažná chyba < kód chyby > provádění operací aktualizace # # # # # z # # # # # ($ $...)


## <a name="solution"></a>Řešení

V závislosti na počtu aktualizací, které jsou instalovány nebo vraceny zpět, může proces aktualizace chvíli trvat. Ponechte virtuální počítač v tomto stavu po dobu 8 hodin. Pokud je virtuální počítač v tomto období stále v tomto stavu, restartujte virtuální počítač z Azure Portal a podívejte se, jestli se může normálně spustit. Pokud tento krok nefunguje, vyzkoušejte následující řešení.

### <a name="remove-the-update-that-causes-the-problem"></a>Odebrání aktualizace, která způsobuje problém

1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md). 
2. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).
3. Jakmile je disk s operačním systémem připojený k virtuálnímu počítači pro obnovení, spusťte **diskmgmt. msc** a spusťte správu disků a ujistěte se, že připojený disk je **online**. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem, ve kterém je umístěná složka \Windows. Pokud je disk zašifrovaný, před pokračováním v dalším postupu v tomto dokumentu disk dešifrujte.

4. Otevřete instanci příkazového řádku se zvýšenými oprávněními (Spustit jako správce). Spuštěním následujícího příkazu Získejte seznam balíčků aktualizací, které jsou na připojeném disku s operačním systémem:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Pokud je například připojený disk s operačním systémem jednotka F, spusťte následující příkaz:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otevřete soubor C:\temp\Patch_level.txt a pak ho z dolní části si přečtěte. Vyhledejte aktualizaci, která čeká na **instalaci** nebo **čeká** na odinstalaci.  Následuje ukázka stavu aktualizace:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Odeberte aktualizaci, která způsobila problém:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Příklad: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > V závislosti na velikosti balíčku zabere nástroj DISM během procesu zrušení instalace nějakou dobu. Normálně se proces dokončí do 16 minut.

7. [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Potom zkontrolujte, zda byl problém vyřešen.
