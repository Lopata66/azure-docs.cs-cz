---
title: Kontroluje se systém souborů při spouštění virtuálního počítače Azure | Microsoft Docs
description: Přečtěte si, jak vyřešit problém, který virtuální počítač při spouštění spouští, a při spuštění systému souborů | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 196f49a72932906e0a21b3c6c534c79d291a845f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632989"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows při spuštění virtuálního počítače Azure zobrazuje "kontrolu systému souborů"

Tento článek popisuje chybu "Kontrola systému souborů", se kterou se můžete setkat při spuštění virtuálního počítače s Windows v Microsoft Azure.


## <a name="symptom"></a>Příznak 

Virtuální počítač s Windows se nespustí. Když zkontrolujete spouštěcí snímky obrazovky v [diagnostice spouštění](boot-diagnostics.md), zjistíte, že je spuštěný proces kontroly disku (chkdsk.exe) s jednou z následujících zpráv:

- Kontrola a oprava jednotky (C:)
- Kontroluje se systém souborů na jednotce C:

## <a name="cause"></a>Příčina

Pokud se v systému souborů najde chyba NTFS, systém Windows zkontroluje a opraví konzistenci disku při příštím restartování. K tomu obvykle dochází v případě, že virtuální počítač měl nějaké neočekávané restartování nebo pokud byl proces vypnutí virtuálního počítače prudkě přerušený.

## <a name="solution"></a>Řešení 

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Systém Windows se spustí normálně po dokončení procesu kontroly disku. Pokud je virtuální počítač zablokovaný v procesu kontroly disku, zkuste na virtuálním počítači spustit kontrolní disk offline:
1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).  
3. Na virtuálním počítači pro obnovení spusťte kontrolu disku na připojeném disku s operačním systémem. V následující ukázce je písmeno připojeného disku s operačním systémem E: 

    ```console
    chkdsk E: /f
    ```

4. Po dokončení kontroly disku odpojte disk od virtuálního počítače pro obnovení a pak tento disk znovu připojte k ovlivněnému virtuálnímu počítači jako disk s operačním systémem. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).
