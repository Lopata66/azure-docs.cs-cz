---
title: Vytvoření a nahrání virtuálního pevného disku se systémem Linux CentOS v Azure
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: szark
ms.openlocfilehash: 1ac2b24649363538d2728f302941b5a4bf5dd357
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534158"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na CentOS pro Azure

Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.

* [Příprava virtuálního počítače s CentOS 6. x pro Azure](#centos-6x)
* [Příprava virtuálního počítače s CentOS 7.0 pro Azure](#centos-70)


## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že jste už nainstalovali CentOS (nebo podobný odvozený) operační systém Linux na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci CentOS**

* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD. Pokud používáte VirtualBox, znamená to, že při vytváření disku vyberete **pevnou velikost** na rozdíl od výchozího dynamicky přiděleného disku.
* Při instalaci systému Linux *doporučujeme* místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete konfliktům LVM názvů s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému stejnému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se můžou používat na datových discích.
* Vyžaduje se podpora jádra pro připojení systémů souborů UDF. Při prvním spuštění v Azure se konfigurace zřizování předává virtuálnímu počítači Linux přes médium ve formátu UDF, které je připojené k hostu. Agent Azure Linux musí být schopný připojit systém souborů UDF ke čtení konfigurace a zřídit virtuální počítač.
* Verze jádra systému Linux pod 2.6.37 nepodporují architekturu NUMA na technologii Hyper-V s většími velikostmi virtuálních počítačů. Tento problém se týká především starších distribucí pomocí nadřazeného jádra Red Hat 2.6.32 a byl opraven v RHEL 6,6 (kernel-2.6.32-504). Systémy s vlastními jádry staršími než 2.6.37 nebo jádry založenými na RHEL, které jsou starší než 2.6.32-504, musí nastavit parametr spouštění `numa=off` v příkazovém řádku jádra v souboru GRUB. conf. Další informace najdete v článku Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor.  Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

3. V CentOS 6 může NetworkManager kolidovat s agentem Azure Linux. Tento balíček odinstalujte spuštěním následujícího příkazu:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

    ```bash
    sudo chkconfig network on
    ```

8. Pokud chcete použít OpenLogic zrcadly, které jsou hostovány v datacentrech Azure, nahraďte soubor `/etc/yum.repos.d/CentOS-Base.repo` následujícím úložištěm.  Tím se také přidá úložiště **[OpenLogic]** , které zahrnuje další balíčky, jako je například agent Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Zbývající část tohoto průvodce vám bude předpokládat, že používáte aspoň `[openlogic]` úložiště, které se použije k instalaci agenta Azure Linux níže.

9. Přidejte následující řádek do/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Spuštěním následujícího příkazu vymažete aktuální metadata Yumu a aktualizujte systém pomocí nejnovějších balíčků:

    ```bash
    yum clean all
    ```

    Pokud nevytváříte image pro starší verzi CentOS, doporučuje se aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu může být vyžadováno restartování počítače.

11. Volitelné Nainstalujte ovladače pro Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Tento krok je **vyžadován** pro CentOS 6,3 a starší a volitelný pro pozdější verze.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Případně můžete postupovat podle pokynů k ruční instalaci na [stránce pro stažení aplikace LIS](https://www.microsoft.com/download/details.aspx?id=51612) a nainstalovat na virtuální počítač službu ot./min.

12. Nainstalujte agenta a závislosti pro Azure Linux. Spusťte a povolte službu waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Balíček WALinuxAgent odstraní balíčky NetworkManager a NetworkManager-GNOME, pokud již nebyly odebrány, jak je popsáno v kroku 3.

13. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, že v textovém editoru otevřete `/boot/grub/menu.lst` a zajistěte, aby výchozí jádro zahrnovalo následující parametry:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.

    Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port.  V případě potřeby může být možnost `crashkernel` nakonfigurované, ale Všimněte si, že tento parametr sníží velikost dostupné paměti virtuálního počítače o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

    > [!Important]
    > CentOS 6,5 a starší musí také nastavit parametr jádra `numa=off`. Podívejte se na Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.

15. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte v `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.



## <a name="centos-70"></a>CentOS 7.0 +

**Změny v CentOS 7 (a podobné deriváty)**

Příprava virtuálního počítače s CentOS 7 pro Azure je velmi podobná CentOS 6, ale existuje několik důležitých rozdílů, které se zabývají:

* Balíček NetworkManager už není v konfliktu s agentem Azure Linux. Tento balíček se nainstaluje ve výchozím nastavení a doporučujeme, abyste ho neodebrali.
* GRUB2 se teď používá jako výchozí zaváděcí program pro spouštění, takže se změnil postup pro úpravu parametrů jádra (viz níže).
* XFS je teď výchozím systémem souborů. Systém souborů ext4 se může v případě potřeby i nadále použít.

**Kroky konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

3. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Pokud chcete použít OpenLogic zrcadly, které jsou hostovány v datacentrech Azure, nahraďte soubor `/etc/yum.repos.d/CentOS-Base.repo` následujícím úložištěm.  Tím se také přidá úložiště **[OpenLogic]** zahrnující balíčky pro agenta Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Zbývající část tohoto průvodce vám bude předpokládat, že používáte aspoň `[openlogic]` úložiště, které se použije k instalaci agenta Azure Linux níže.

7. Spusťte následující příkaz, který vymaže aktuální metadata Yumu a nainstaluje aktualizace:

    ```bash
    sudo yum clean all
    ```

    Pokud nevytváříte image pro starší verzi CentOS, doporučuje se aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu se možná vyžaduje restart.

8. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, že v textovém editoru otevřete `/etc/default/grub` a upravíte parametr `GRUB_CMDLINE_LINUX`, například:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Také vypne nové zásady vytváření názvů pro síťové karty v CentOS 7. Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. V případě potřeby může být možnost `crashkernel` nakonfigurované, ale Všimněte si, že tento parametr sníží velikost dostupné paměti virtuálního počítače o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

9. Po dokončení úprav `/etc/default/grub` na výše spusťte následující příkaz, který znovu sestaví konfiguraci grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Při sestavování image z **VMware, VirtualBox nebo KVM:** zajistěte, aby byly ovladače Hyper-V součástí initramfs:

    Upravit `/etc/dracut.conf`, přidat obsah:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Znovu sestavte initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Nainstalujte agenta a závislosti pro Azure Linux:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte v `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí virtuálního pevného disku s CentOS Linux. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
