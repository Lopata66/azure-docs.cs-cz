---
title: Vytvoření a nahrání Red Hat Enterprise Linux VHD pro použití v Azure | Microsoft Docs
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: aef25e79d99c6c7434123df76e85e605b22fde51
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082249"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na Red Hat pro Azure
V tomto článku se dozvíte, jak připravit virtuální počítač s Red Hat Enterprise Linux (RHEL) pro použití v Azure. Verze RHEL, které jsou pokryté v tomto článku, jsou 6.7 + a 7.1 +. Hypervisory pro přípravu, které jsou pokryté v tomto článku, jsou Hyper-V, virtuální počítač založený na jádrech (KVM) a VMware. Další informace o požadavcích na způsobilost pro účast v programu cloudového přístupu Red Hat najdete na [webu Cloud Access Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) a [na platformě Azure s RHEL](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Způsob automatizace vytváření RHEL imagí najdete v tématu [Azure image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače založeného na Red Hat pomocí Správce technologie Hyper-V

### <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že už jste získali soubor ISO z webu Red Hat a nainstalovali jste image RHEL na virtuální pevný disk (VHD). Další podrobnosti o tom, jak použít Správce technologie Hyper-V k instalaci image operačního systému, najdete v tématu [instalace role Hyper-v a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci RHEL**

* Azure nepodporuje formát VHDX. Azure podporuje jenom pevný virtuální pevný disk. Správce technologie Hyper-V můžete použít k převedení disku na formát VHD, nebo můžete použít rutinu Convert-VHD. Pokud používáte VirtualBox, při vytváření disku vyberte **pevnou velikost** na rozdíl od výchozí dynamicky přidělené možnosti.
* Azure podporuje jenom virtuální počítače 1. generace. Virtuální počítač 1. generace můžete převést z VHDX na formát souboru VHD a z dynamického rozšiřování na disk s pevnou velikostí. Nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maximální velikost povolená pro virtuální pevný disk je 1 023 GB.
* Správce logických svazků (LVM) je podporován a lze jej použít na disku s operačním systémem nebo na datových discích ve virtuálních počítačích Azure. Obecně se ale doporučuje místo LVM použít standardní oddíly na disku s operačním systémem. Tento postup se vyhne konfliktu LVM názvů s klonovanými virtuálními počítači, zejména pokud někdy budete potřebovat k řešení potíží disk s operačním systémem připojit k jinému stejnému virtuálnímu počítači. Viz také dokumentace k [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
* Podpora jádra pro připojení systémů souborů formátu Universal Disk Format (UDF) je povinná. Při prvním spuštění v Azure, médium ve formátu UDF, které je připojené k hostu, předává konfiguraci zřizování virtuálnímu počítači se systémem Linux. Agent Azure Linux musí být schopný připojit systém souborů UDF ke čtení konfigurace a zřídit virtuální počítač.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor.  Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další podrobnosti najdete v následujících krocích. Další informace najdete v poznámkách k [instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače s RHEL 6 pomocí Správce technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

1. V RHEL 6 může NetworkManager kolidovat s agentem Azure Linux. Tento balíček odinstalujte spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. Vytvořte nebo upravte `/etc/sysconfig/network` soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla způsobují problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # sudo chkconfig network on

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Tuto úpravu provedete tak `/boot/grub/menu.lst` , že otevřete v textovém editoru a zajistěte, aby výchozí jádro zahrnovalo následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.
    
    Kromě toho doporučujeme odebrat následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port.  Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více. Tato konfigurace může být problematická na menších velikostech virtuálních počítačů.


1. Ujistěte se, že je server Secure Shell (SSH) nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte/etc/ssh/sshd_config tak, aby zahrnoval následující řádek:

        ClientAliveInterval 180

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Při instalaci balíčku WALinuxAgent se odstraní balíčky NetworkManager a NetworkManager-GNOME, pokud už se v kroku 3 neodebraly.

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a je možné ho vyprázdnit, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte odpovídajícím způsobem následující parametry v/etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klikněte na **Akce** > **vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače s RHEL 7 pomocí Správce technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

1. Vytvořte nebo upravte `/etc/sysconfig/network` soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li provést tuto úpravu `/etc/default/grub` , otevřete v textovém editoru a `GRUB_CMDLINE_LINUX` upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Tato konfigurace také vypne nové zásady vytváření názvů v RHEL 7 pro síťové karty. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte `/etc/ssh/sshd_config` , aby obsahovala následující řádek:

        ClientAliveInterval 180

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a může být vyprázdněný, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte `/etc/waagent.conf` správným způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klikněte na **Akce** > **vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Příprava virtuálního počítače založeného na Red Hat z KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Příprava virtuálního počítače s RHEL 6 z KVM

1. Stáhněte si image RHEL 6 na webu Red Hat.

1. Nastavte kořenové heslo.

    Vygenerujte šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo pomocí guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změňte druhé pole uživatele root z "!!" do šifrovaného hesla.

1. Z image QCOW2 vytvořte virtuální počítač v KVM. Nastavte typ disku na **QCOW2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Pak spusťte virtuální počítač a přihlaste se jako kořenový adresář.

1. Vytvořte nebo upravte `/etc/sysconfig/network` soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla způsobují problémy při klonování virtuálního počítače v Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # chkconfig network on

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Tuto konfiguraci provedete tak `/boot/grub/menu.lst` , že otevřete v textovém editoru a zajistěte, aby výchozí jádro zahrnovalo následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.
    
    Kromě toho doporučujeme odebrat následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.


1. Přidat moduly technologie Hyper-V do initramfs:  

    Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Odinstalace cloudu – Init:

        # yum remove cloud-init

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění:

        # chkconfig sshd on

    Upravte/etc/ssh/sshd_config tak, aby zahrnoval následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a může být vyprázdněný, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte odpovídajícím způsobem následující parametry v **/etc/waagent.conf** :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač v KVM.

1. Převeďte image QCOW2 na formát VHD.

> [!NOTE]
> Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Příprava virtuálního počítače s RHEL 7 z KVM

1. Stáhněte si obrázek RHEL 7 na webu Red Hat. Tento postup jako příklad používá RHEL 7.

1. Nastavte kořenové heslo.

    Vygenerujte šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo pomocí guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změnit druhé pole kořenového uživatele z "!!" do šifrovaného hesla.

1. Z image QCOW2 vytvořte virtuální počítač v KVM. Nastavte typ disku na **QCOW2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Pak spusťte virtuální počítač a přihlaste se jako kořenový adresář.

1. Vytvořte nebo upravte `/etc/sysconfig/network` soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat, abyste povolili instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li tuto konfiguraci provést `/etc/default/grub` , otevřete v textovém editoru a `GRUB_CMDLINE_LINUX` upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tento příkaz také zajistí, aby byly všechny zprávy konzoly odesílány do prvního sériového portu, což může pomoct podpoře Azure s problémy ladění. Příkaz také vypne nové zásady vytváření názvů pro síťové adaptéry v RHEL 7. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte moduly technologie Hyper-V do initramfs.

    Upravit `/etc/dracut.conf` a přidat obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Odinstalace cloudu – Init:

        # yum remove cloud-init

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění:

        # systemctl enable sshd

    Upravte/etc/ssh/sshd_config tak, aby zahrnoval následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

    Povolit službu waagent:

        # systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a může být vyprázdněný, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte `/etc/waagent.conf` správným způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač v KVM.

1. Převeďte image QCOW2 na formát VHD.

> [!NOTE]
> Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Příprava virtuálního počítače založeného na Red Hat z VMware
### <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že jste už nainstalovali virtuální počítač s RHEL ve VMware. Podrobnosti o tom, jak nainstalovat operační systém ve VMware, najdete v tématu [Instalační příručka k hostovanému operačnímu systému VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Při instalaci operačního systému Linux doporučujeme místo LVM použít standardní oddíly, což je často výchozí nastavení pro mnoho instalací. Tím se zabrání konfliktům LVM názvů s klonováným virtuálním počítačem, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači, aby se mohl problém vyřešit. LVM nebo RAID se dá použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agenta pro Linux můžete nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor. Další informace o tomto postupu najdete v následujících krocích.
* Při vytváření virtuálního pevného disku vyberte možnost **Uložit virtuální disk jako jeden soubor**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Příprava virtuálního počítače s RHEL 6 z VMware
1. V RHEL 6 může NetworkManager kolidovat s agentem Azure Linux. Tento balíček odinstalujte spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. V adresáři/etc/sysconfig/vytvořte soubor s názvem **Network** , který obsahuje následující text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla způsobují problémy při klonování virtuálního počítače v Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # sudo chkconfig network on

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak `/etc/default/grub` , že otevřete v textovém editoru a `GRUB_CMDLINE_LINUX` upravíte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Přidat moduly technologie Hyper-V do initramfs:

    Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte `/etc/ssh/sshd_config` , aby obsahovala následující řádek:

    ClientAliveInterval 180

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a může být vyprázdněný, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte `/etc/waagent.conf` správným způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač a převeďte soubor VMDK na soubor. VHD.

> [!NOTE]
> Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Příprava virtuálního počítače s RHEL 7 z VMware
1. Vytvořte nebo upravte `/etc/sysconfig/network` soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo upravte `/etc/sysconfig/network-scripts/ifcfg-eth0` soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li provést tuto úpravu `/etc/default/grub` , otevřete v textovém editoru a `GRUB_CMDLINE_LINUX` upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tato konfigurace taky zajišťuje, že se všechny zprávy konzoly odesílají na první sériový port, což může pomoct podpoře Azure s problémy ladění. Také vypne nové zásady vytváření názvů pro síťové karty v RHEL 7. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost můžete nechat nakonfigurovanou v `crashkernel` případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte moduly technologie Hyper-V do initramfs.

    Upravit `/etc/dracut.conf`, přidat obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění. Toto nastavení je obvykle výchozí. Upravte `/etc/ssh/sshd_config` , aby obsahovala následující řádek:

        ClientAliveInterval 180

1. Balíček `WALinuxAgent-<version>`WALinuxAgent byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací místo pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a může být vyprázdněný, pokud dojde k zrušení zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku upravte `/etc/waagent.conf` správným způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač a převeďte soubor VMDK na formát VHD.

> [!NOTE]
> Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Příprava virtuálního počítače založeného na Red Hat z ISO pomocí souboru Kickstart automaticky
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Příprava virtuálního počítače s RHEL 7 ze souboru Kickstart

1.  Vytvořte soubor Kickstart, který obsahuje následující obsah, a uložte soubor. Podrobnosti o instalaci Kickstart najdete v [Průvodci instalací Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Umístěte soubor Kickstart, ke kterému má instalační systém přístup.

1. Ve Správci technologie Hyper-V vytvořte nový virtuální počítač. Na stránce **připojit virtuální pevný disk** vyberte možnost **připojit virtuální pevný disk později**a dokončete Průvodce novým virtuálním počítačem.

1. Otevřete nastavení virtuálního počítače:

    a.  Připojte k virtuálnímu počítači nový virtuální pevný disk. Ujistěte se, že jste vybrali **formát VHD** a **pevnou velikost**.

    b.  Připojte k jednotce DVD instalaci ISO.

    c.  Nastavte systém BIOS na spouštění z disku CD-ROM.

1. Spusťte virtuální počítač. Po zobrazení Průvodce instalací stiskněte klávesu **TAB** a nakonfigurujte možnosti spuštění.

1. Zadejte `inst.ks=<the location of the kickstart file>` na konci možností spuštění a stiskněte klávesu **ENTER**.

1. Počkejte na dokončení instalace. Po dokončení se virtuální počítač automaticky vypne. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="known-issues"></a>Známé problémy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Při použití hypervisoru, který není Hyper-V, nejde ovladač Hyper-V zahrnout do počátečního disku RAM.

V některých případech nemusí instalační programy pro Linux zahrnovat ovladače pro Hyper-V na počátečním disku RAM (Image initrd nebo initramfs), pokud Linux nezjistí, že je spuštěný v prostředí Hyper-V.

Pokud používáte jiný systém virtualizace (tj. VirtualBox, Xen atd.) k přípravě image Linux, možná budete muset znovu sestavit image initrd a zajistit, aby byly na počátečním disku RAM k dispozici alespoň moduly jádra hv_vmbus a hv_storvsc. Jedná se o známý problém minimálně v systémech, které jsou založené na nadřazené distribuci Red Hat.

Pokud chcete tento problém vyřešit, přidejte moduly technologie Hyper-V tak, aby se initramfs a znovu sestavily:

Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Znovu sestavte initramfs:

        # dracut -f -v

Další podrobnosti najdete v informacích o opakovaném sestavování [initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Další postup
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí svého Red Hat Enterprise Linuxho virtuálního pevného disku. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

Další informace o hypervisorech, které jsou certifikované pro spouštění Red Hat Enterprise Linux, najdete [na webu Red Hat](https://access.redhat.com/certified-hypervisors).
