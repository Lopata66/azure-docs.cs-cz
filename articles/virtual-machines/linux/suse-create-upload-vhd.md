---
title: Vytvoření a nahrání virtuálního pevného disku s SUSE Linux v Azure
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém SUSE Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 032b49631c6adb30d4b25f8b82d35dab49ffd3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81757669"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Příprava virtuálního počítače se SLES nebo openSUSE pro Azure


V tomto článku se předpokládá, že jste už nainstalovali operační systém SUSE nebo openSUSE Linux na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>Poznámky k instalaci SLES/openSUSE
* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD.
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se můžou použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor.  Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>Použití SUSE studia
[SUSE Studio](http://www.susestudio.com) může snadno vytvářet a spravovat vaše SLES a openSUSE image pro Azure a Hyper-V. Toto je doporučený postup pro přizpůsobení vlastních imagí SLES a openSUSE.

Jako alternativu k vytváření vlastního virtuálního pevného disku SUSE také publikuje image BYOS (Přineste si vlastní odběr) pro SLES v [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Příprava SUSE Linux Enterprise Server 11 SP4
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.
3. Zaregistrujte systém SUSE Linux Enterprise, aby mohl stahovat aktualizace a instalovat balíčky.
4. Aktualizujte systém pomocí nejnovějších oprav:
   
        # sudo zypper update
5. Nainstalujte agenta Azure Linux z úložiště SLES:
   
        # sudo zypper install python-azure-agent
6. V chkconfig ověřte, zda je waagent nastaven na zapnuto, a pokud ne, povolte ho pro automatické spuštění:
   
        # sudo chkconfig waagent on
7. Ověřte, jestli je spuštěná služba waagent, a pokud ne, spusťte ji: 
   
        # sudo service waagent start
8. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Uděláte to tak, že v textovém editoru otevřete "/boot/grub/menu.lst" a zajistěte, aby výchozí jádro zahrnovalo následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.
9. Potvrďte, že/boot/grub/menu.lst a/etc/fstab odkazují na disk pomocí jeho UUID (by UUID) místo ID disku (podle ID). 
   
    Získat UUID disku
   
        # ls /dev/disk/by-uuid/
   
    Pokud se používá/dev/disk/by-ID/, aktualizujte/boot/grub/menu.lst i/etc/fstab tak, že hodnota je správná hodnotou identifikátoru UUID.
   
    Před změnou
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po změně
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Doporučuje se upravit soubor "/etc/sysconfig/Network/DHCP" a změnit `DHCLIENT_SET_HOSTNAME` parametr na následující:
    
     DHCLIENT_SET_HOSTNAME = "ne"
12. V části "/etc/sudoers", odkomentujte nebo odeberte následující řádky, pokud existují:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.
14. Nevytvářejte odkládací místo na disku s operačním systémem.
    
    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte odpovídajícím způsobem následující parametry v/etc/waagent.conf:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. přípojný bod =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Poznámka: nastavte tuto hodnotu na vše, co potřebujete.
15. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

---
## <a name="prepare-opensuse-131"></a>Příprava openSUSE 13.1 +
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.
3. V prostředí spusťte příkaz`zypper lr`. Pokud tento příkaz vrátí výstup podobný následujícímu, pak jsou úložiště konfigurována podle očekávání – nejsou nutné žádné úpravy (Všimněte si, že čísla verzí se mohou lišit):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Pokud příkaz vrátí "žádná úložiště nebyla definována..." pak pomocí následujících příkazů přidejte tato úložišť:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Pak můžete ověřit, že úložiště byla přidána, spuštěním příkazu '`zypper lr`'. V případě, že některá z příslušných úložišť aktualizací není povolená, povolte ji pomocí následujícího příkazu:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualizujte jádro na nejnovější dostupnou verzi:
   
        # sudo zypper up kernel-default
   
    Nebo pokud chcete aktualizovat systém se všemi nejnovějšími opravami:
   
        # sudo zypper update
5. Nainstalujte agenta Azure Linux.
   
        # sudo zypper install WALinuxAgent
6. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, že v textovém editoru otevřete "/boot/grub/menu.lst" a zajistěte, aby výchozí jádro zahrnovalo následující parametry:
   
     Konzola = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Kromě toho odeberte následující parametry z spouštěcího řádku jádra, pokud existují:
   
     libata. atapi_enabled = 0 Reserve = 0x1f0, 0x8
7. Doporučuje se upravit soubor "/etc/sysconfig/Network/DHCP" a změnit `DHCLIENT_SET_HOSTNAME` parametr na následující:
   
     DHCLIENT_SET_HOSTNAME = "ne"
8. **Důležité informace:** V části "/etc/sudoers", odkomentujte nebo odeberte následující řádky, pokud existují:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.
10. Nevytvářejte odkládací místo na disku s operačním systémem.
    
    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte odpovídajícím způsobem následující parametry v/etc/waagent.conf:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. přípojný bod =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Poznámka: nastavte tuto hodnotu na vše, co potřebujete.
11. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Zajistěte, aby byl agent Azure Linux spuštěn při spuštění:
    
        # sudo systemctl enable waagent.service
13. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí virtuálního pevného disku s SUSE Linux. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
