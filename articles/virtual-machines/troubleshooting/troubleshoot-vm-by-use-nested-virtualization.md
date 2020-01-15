---
title: Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure | Microsoft Docs
description: Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: 87faea5385f5b0fd2c481c6aa7c45a9867b34163
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942184"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Řešení potíží s virtuálním počítačem Azure pomocí vnořené virtualizace v Azure

V tomto článku se dozvíte, jak vytvořit vnořené prostředí virtualizace v nástroji Microsoft Azure, abyste mohli připojit disk problémového virtuálního počítače na hostiteli Hyper-V (záchranný virtuální počítač) pro účely řešení potíží.

## <a name="prerequisites"></a>Požadavky

Chcete-li připojit problémový virtuální počítač, musí záchranný virtuální počítač splňovat následující požadavky:

-   Záchranný virtuální počítač musí být ve stejném umístění jako virtuální počítač problému.

-   Záchranný virtuální počítač musí používat stejný typ účtu úložiště (Standard nebo Premium) jako virtuální počítač problému.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: vytvoření záchranného virtuálního počítače a instalace role Hyper-V

1.  Vytvořte nový záchranný virtuální počítač:

    -  Operační systém: Windows Server 2016 Datacenter

    -  Velikost: všechny řady v3 s alespoň dvěma jádry, které podporují vnořenou virtualizaci. Další informace najdete v tématu [Představujeme nové velikosti virtuálních počítačů s Dv3 a Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Stejné umístění, účet úložiště a skupina prostředků jako virtuální počítač problému.

    -  Vyberte stejný typ úložiště jako virtuální počítač problému (Standard nebo Premium).

2.  Po vytvoření záchranného virtuálního počítače se jedná o vzdálenou plochu na záchranný virtuální počítač.

3.  V Správce serveru vyberte **spravovat** > **Přidat role a funkce**.

4.  V části **typ instalace** vyberte instalace na základě **rolí nebo na základě funkcí**.

5.  V části **Vybrat cílový server** se ujistěte, že je vybraný záchranný virtuální počítač.

6.  Vyberte **roli Hyper-V** > **Přidat funkce**.

7.  V části **funkce** vyberte **Další** .

8.  Pokud je k dispozici virtuální přepínač, vyberte ho. V opačném případě vyberte **Další**.

9.  V části **migrace** vyberte **Další** .

10. V části **výchozí úložiště** vyberte **Další**.

11. V případě potřeby zaškrtněte políčko pro restartování serveru.

12. Vyberte **Install** (Nainstalovat).

13. Povolí serveru instalaci role Hyper-V. Tato akce trvá několik minut a server se automaticky restartuje.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Vytvoření virtuálního počítače s problémem na serveru Hyper-V záchranného virtuálního počítače

1.  [Vytvořte snímek disku](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) pro disk s operačním systémem virtuálního počítače, který má problém, a pak připojte disk snímku k virtuálnímu počítači recuse.

2.  Vzdálená plocha do záchranného virtuálního počítače.

3.  Spusťte správu disků (diskmgmt. msc). Ujistěte se, že je disk virtuálního počítače problému nastavený na **offline**.

4.  Otevřete Správce technologie Hyper-V: v **Správce serveru**vyberte **roli technologie Hyper-v**. Pravým tlačítkem myši klikněte na server a pak vyberte **Správce technologie Hyper-V**.

5.  Ve Správci technologie Hyper-V klikněte pravým tlačítkem na záchranný virtuální počítač a vyberte **nový** > **virtuální počítač** > **Další**.

6.  Zadejte název virtuálního počítače a pak vyberte **Další**.

7.  Vyberte možnost **generace 1**.

8.  Nastavte spouštěcí paměť v 1024 MB nebo více.

9. Pokud je to možné, vyberte přepínač sítě Hyper-V, který byl vytvořen. V opačném případě přejděte na další stránku.

10. Vyberte **připojit virtuální pevný disk později**.

    ![Obrázek možnosti připojit virtuální pevný disk později](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Po vytvoření virtuálního počítače vyberte **Dokončit** .

12. Klikněte pravým tlačítkem na virtuální počítač, který jste vytvořili, a pak vyberte **Nastavení**.

13. Vyberte možnost **řadič IDE 0**, vyberte možnost **pevný disk**a klikněte na tlačítko **Přidat**.

    ![Obrázek týkající se přidání nového pevného disku](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Na stránce **fyzický pevný disk**vyberte disk s PROBLEMATICKým virtuálním počítačem, který jste připojili k virtuálnímu počítači Azure. Pokud nevidíte žádné disky uvedené na seznamu, zkontrolujte, jestli je disk nastavený na offline pomocí správy disků.

    ![obrázek o připojení disku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Vyberte **Apply** (Použít) a pak vyberte **OK**.

16. Dvakrát klikněte na virtuální počítač a potom ho spusťte.

17. Nyní můžete pracovat na VIRTUÁLNÍm počítači jako místní virtuální počítač. Můžete postupovat podle všech potřebných kroků pro řešení potíží.

## <a name="step-3-replace-the-os-disk-used-by-the-problem-vm"></a>Krok 3: Výměna disku s operačním systémem, který používá virtuální počítač pro problémy

1.  Po opětovném obnovení virtuálního počítače do režimu online vypněte virtuální počítač ve Správci technologie Hyper-V.

2.  [Odpojte a odpojte opravený disk s operačním systémem](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Nahraďte disk s operačním systémem používaný virtuálním počítačem opraveným diskem s operačním systémem](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači s Windows](troubleshoot-app-connection.md).
