---
title: Azure Disk Encryption ukázkové skripty
description: Tento článek je dodatek pro Microsoft Azure šifrování disku pro virtuální počítače se systémem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b034bad8661e93cbf5797c93739f1db3a64626f0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748902"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption ukázkové skripty 

Tento článek poskytuje ukázkové skripty pro přípravu předem šifrovaných virtuálních pevných disků a dalších úloh.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Ukázkové skripty PowerShellu pro Azure Disk Encryption 

- **Zobrazit seznam všech šifrovaných virtuálních počítačů ve vašem předplatném**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Vypíše všechny tajné klíče pro šifrování disků používané k šifrování virtuálních počítačů v trezoru klíčů.** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Použití skriptu PowerShellu pro Azure Disk Encryption předpoklady
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption, můžete použít [skript PowerShellu pro Azure Disk Encryption požadavků](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tohoto skriptu PowerShellu najdete v tématu [rychlý Start k šifrování virtuálního počítače](disk-encryption-powershell-quickstart.md). Komentáře můžete z části skriptu odebrat, od řádku 211 až po zašifrování všech disků pro existující virtuální počítače v existující skupině prostředků. 

Následující tabulka ukazuje, které parametry lze použít ve skriptu prostředí PowerShell: 


|Parametr|Popis|Závaznou?|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, do které patří Trezor klíčů.  Pokud neexistuje, vytvoří se nová skupina prostředků s tímto názvem.| True|
|$keyVaultName|Název trezoru klíčů, do kterého se mají umístit šifrovací klíče Pokud jeden z nich neexistuje, vytvoří se nový trezor s tímto názvem.| True|
|$location|Umístění trezoru klíčů. Zajistěte, aby byl Trezor klíčů a virtuální počítače zašifrované ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, který se má použít  Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.|True|
|$aadAppName|Název aplikace služby Azure AD, která bude použita k zápisu tajných kódů do trezoru klíčů. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace již existuje, předejte do skriptu parametr aadClientSecret.|False|
|$aadClientSecret|Tajný kód klienta aplikace Azure AD, který byl vytvořen dříve.|False|
|$keyEncryptionKeyName|Název volitelného šifrovacího klíče klíče v trezoru klíčů. Pokud neexistuje, vytvoří se nový klíč s tímto názvem.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD

- [Povolení šifrování disku na existujícím nebo běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Zakázat šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolení šifrování disku na existujícím nebo běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Zakázat šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux. 


- [Vytvoření nového šifrovaného spravovaného disku z předem šifrovaného objektu VHD nebo úložiště objektů BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nový zašifrovaný spravovaný disk, který poskytl předem zašifrovaný virtuální pevný disk a jeho odpovídající nastavení šifrování.





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Šifrování jednotky operačního systému na běžícím virtuálním počítači se systémem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Předpoklady pro šifrování disků s operačním systémem

* Virtuální počítač musí používat distribuci kompatibilní s nástrojem pro šifrování disků s operačním systémem, jak je uvedeno v části [podporované operační systémy Azure Disk Encryption](disk-encryption-overview.md#supported-vm-sizes) 
* Virtuální počítač musí být vytvořený z image Marketplace v Azure Resource Manager.
* Virtuální počítač Azure s minimálně 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakáže SELinux. Pokud chcete zakázat SELinux, přečtěte si téma "4.4.2". Zakázání SELinux "v [Průvodci uživatele SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na virtuálním počítači.
* Po zakázání SELinux restartujte virtuální počítač aspoň jednou.

### <a name="steps"></a>Kroky
1. Vytvořte virtuální počítač pomocí jedné z výše uvedených distribucí.

   Pro CentOS 7,2 se šifrování disku s operačním systémem podporuje přes speciální image. Pokud chcete použít tento obrázek, jako SKU při vytváření virtuálního počítače zadejte "7.2 n":

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Nakonfigurujte virtuální počítač podle svých potřeb. Pokud chcete zašifrovat všechny jednotky (OS + data), musíte zadat a připojit datové jednotky z adresáři/etc/fstab.

   > [!NOTE]
   > Použít UUID =... Chcete-li zadat datové jednotky v/etc/fstab namísto zadání názvu blokového zařízení (například adresář/dev/sdb1). Během šifrování se pořadí jednotek změní na virtuálním počítači. Pokud váš virtuální počítač spoléhá na konkrétní pořadí blokovaných zařízení, připojení se po jeho šifrování nepodaří připojit.

3. Odhlaste se z relací SSH.

4. Chcete-li zašifrovat operační systém, zadejte volumeType jako **všechny** nebo **operační systém** , pokud povolíte šifrování.

   > [!NOTE]
   > Všechny procesy uživatelského prostoru, které nejsou spuštěny jako `systemd` služby by měly být ukončeny pomocí `SIGKILL`. Restartujte virtuální počítač. Když na běžícím virtuálním počítači povolíte šifrování disků s operačním systémem, naplánujte výpadky virtuálního počítače.

5. Pravidelně Sledujte průběh šifrování pomocí pokynů v [následující části](#monitoring-os-encryption-progress).

6. Po Get-AzVmDiskEncryptionStatus se zobrazí "VMRestartPending", restartujte virtuální počítač, a to tak, že se přihlásíte k tomuto počítači nebo pomocí portálu, PowerShellu nebo rozhraní příkazového řádku.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Před restartováním doporučujeme uložit [diagnostiku spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

## <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Průběh šifrování operačního systému můžete monitorovat třemi způsoby:

* Použijte rutinu `Get-AzVmDiskEncryptionStatus` a zkontrolujte pole ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Jakmile virtuální počítač dosáhne "spuštěného" šifrování disku s operačním systémem, bude trvat přibližně 40 až 50 minut na virtuálním počítači s podporou Premium Storage.

  Kvůli [potížím #388](https://github.com/Azure/WALinuxAgent/issues/388) v WALinuxAgent `OsVolumeEncrypted` a `DataVolumesEncrypted` se v některých distribucích zobrazovaly jako `Unknown`. U WALinuxAgent verze 2.1.5 a novější je tento problém vyřešen automaticky. Pokud se ve výstupu zobrazí `Unknown`, můžete ověřit stav šifrování disku pomocí Azure Resource Explorer.

  Přejít na [Azure Resource Explorer](https://resources.azure.com/)a potom rozbalte tuto hierarchii na panelu výběru vlevo:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  V InstanceView se posuňte dolů a zobrazte stav šifrování vašich jednotek.

  ![Zobrazení instance virtuálního počítače](./media/disk-encryption/vm-instanceview.png)

* Podívejte se na [diagnostiku spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE by měly být s předponou `[AzureDiskEncryption]`.

* Přihlaste se k virtuálnímu počítači přes SSH a získejte protokol rozšíření z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Doporučujeme, abyste se k virtuálnímu počítači přihlásili, zatímco probíhá šifrování operačního systému. Protokoly zkopírujte pouze v případě, že se nezdařily jiné dvě metody.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux
Příprava pro předem zašifrované virtuální pevné disky se může lišit v závislosti na distribuci. Příklady pro přípravu Ubuntu 16, openSUSE 13,2 a CentOS 7 jsou k dispozici. 

### <a name="ubuntu-16"></a>Ubuntu 16
Pomocí následujících kroků nakonfigurujte šifrování při instalaci distribuce:

1. Při vytváření oddílů disků vyberte **Konfigurovat šifrované svazky** .

   ![Instalace Ubuntu 16,04 – konfigurace šifrovaných svazků](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvořte samostatnou spouštěcí jednotku, která nesmí být zašifrovaná. Zašifrujte kořenovou jednotku.

   ![Nastavení Ubuntu 16,04 – vyberte zařízení, která chcete zašifrovat.](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte heslo. Toto je přístupové heslo, které jste nahráli do trezoru klíčů.

   ![Nastavení Ubuntu 16,04 – poskytnutí hesla](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončete dělení.

   ![Nastavení Ubuntu 16,04 – dokončení dělení](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Když spustíte virtuální počítač a budete požádáni o zadání hesla, použijte heslo, které jste zadali v kroku 3.

   ![Nastavení Ubuntu 16,04 – poskytnutí hesla při spuštění](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Pomocí [těchto pokynů](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)Připravte virtuální počítač pro nahrání do Azure. Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

Nakonfigurujte šifrování pro práci s Azure pomocí následujících kroků:

1. V části/usr/local/sbin/azure_crypt_key.sh vytvořte soubor s obsahem v následujícím skriptu. Věnujte pozornost na název souboru, protože se jedná o název souboru hesla, který používá Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Změňte konfiguraci nešifrovaných oznámení v */etc/crypttab*. Mělo by to vypadat takto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Přidejte do skriptu oprávnění ke spustitelnému souboru:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Upravte */etc/initramfs-tools/modules* přidáním řádků:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Spusťte `update-initramfs -u -k all` pro aktualizaci initramfs, aby se `keyscript` projevil.

7. Nyní můžete zrušit zřízení virtuálního počítače.

   ![Instalace Ubuntu 16,04 – aktualizace – initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračujte dalším krokem a nahrajte virtuální pevný disk do Azure.

### <a name="opensuse-132"></a>openSUSE 13,2
Chcete-li konfigurovat šifrování při instalaci distribuce, proveďte následující kroky:
1. Při vytváření oddílů disku vyberte možnost **Šifrovat skupinu svazků**a pak zadejte heslo. Toto je heslo, které nahrajete do svého trezoru klíčů.

   ![Nastavení openSUSE 13,2-šifrovat skupinu svazků](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Pomocí hesla spusťte virtuální počítač.

   ![Nastavení openSUSE 13,2 – poskytnutí hesla při spuštění](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Připravte virtuální počítač na nahrávání do Azure podle pokynů v tématu [Příprava virtuálního počítače s SLES nebo openSUSE pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

Pokud chcete nakonfigurovat šifrování pro práci s Azure, proveďte následující kroky:
1. Upravte/etc/Dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Odkomentujte tyto řádky na konci souboru/usr/lib/Dracut/Modules.d/90crypt/Module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Na začátek souboru/usr/lib/Dracut/Modules.d/90crypt/Parse-crypt.sh přidejte následující řádek:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změňte všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Upravit/usr/lib/Dracut/Modules.d/90crypt/cryptroot-Ask.sh a připojit ho k "# Open LUKS zařízení":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Spusťte `/usr/sbin/dracut -f -v` pro aktualizaci image initrd.

6. Teď můžete zrušit zřízení virtuálního počítače a nahrát virtuální pevný disk do Azure.

### <a name="centos-7"></a>CentOS 7
Chcete-li konfigurovat šifrování při instalaci distribuce, proveďte následující kroky:
1. Pokud chcete rozdělit disky na oddíly, vyberte možnost **Šifrovat moje data** .

   ![Instalace CentOS 7 – cíl instalace](./media/disk-encryption/centos-encrypt-fig1.png)

2. Ujistěte se, že je pro kořenový oddíl vybráno **šifrování** .

   ![Instalace CentOS 7 – vyberte možnost šifrovat pro kořenový oddíl](./media/disk-encryption/centos-encrypt-fig2.png)

3. Zadejte heslo. Toto je přístupové heslo, které nahrajete do svého trezoru klíčů.

   ![Nastavení pro CentOS 7 – poskytnutí hesla](./media/disk-encryption/centos-encrypt-fig3.png)

4. Když spustíte virtuální počítač a budete požádáni o zadání hesla, použijte heslo, které jste zadali v kroku 3.

   ![Instalace CentOS 7 – zadejte přístupové heslo na spouštění](./media/disk-encryption/centos-encrypt-fig4.png)

5. Připravte virtuální počítač pro nahrání do Azure pomocí instrukcí "CentOS 7.0 +" v tématu [Příprava virtuálního počítače založeného na CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

6. Teď můžete zrušit zřízení virtuálního počítače a nahrát virtuální pevný disk do Azure.

Pokud chcete nakonfigurovat šifrování pro práci s Azure, proveďte následující kroky:

1. Upravte/etc/Dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Odkomentujte tyto řádky na konci souboru/usr/lib/Dracut/Modules.d/90crypt/Module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Na začátek souboru/usr/lib/Dracut/Modules.d/90crypt/Parse-crypt.sh přidejte následující řádek:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změňte všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Po "# Open LUKS zařízení" upravte/usr/lib/Dracut/Modules.d/90crypt/cryptroot-Ask.sh a přidejte následující:
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Spusťte rutinu "/usr/sbin/Dracut-f-v" a aktualizujte image initrd.

    ![Instalace CentOS 7 – Spuštění/usr/sbin/Dracut-f-v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrání šifrovaného virtuálního pevného disku do účtu služby Azure Storage
Po povolení šifrování DM-crypt je potřeba odeslat místní zašifrovaný virtuální pevný disk do svého účtu úložiště.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrajte tajný klíč pro předem zašifrovaný virtuální počítač do trezoru klíčů.
Když šifrujete pomocí aplikace Azure AD (předchozí verze), musí se šifrovací klíč šifrování disku, který jste dříve získali, nahrát jako tajný klíč do trezoru klíčů. Trezor klíčů musí mít pro klienta Azure AD povolený šifrování disku a oprávnění.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Tajný klíč šifrování disku není zašifrovaný pomocí KEK.
K nastavení tajného klíče v trezoru klíčů použijte [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Přístupové heslo se zakóduje jako řetězec Base64 a pak se nahraje do trezoru klíčů. Kromě toho se ujistěte, že jsou při vytváření tajného klíče v trezoru klíčů nastavené následující značky.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


K [připojení disku s operačním systémem bez použití KEK](#without-using-a-kek)použijte `$secretUrl` v dalším kroku.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný kód šifrování disku zašifrovaný pomocí KEK
Než nahrajete tajný klíč do trezoru klíčů, můžete ho případně zašifrovat pomocí klíčového šifrovacího klíče. Použijte [rozhraní API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pro zabalení k prvnímu šifrování tajného klíče pomocí klíčového šifrovacího klíče. Výstupem této operace zalamování je řetězec kódovaný v adrese URL Base64, který lze poté odeslat jako tajný klíč pomocí rutiny [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

V dalším kroku použijte `$KeyEncryptionKey` a `$secretUrl` [Připojte disk s operačním systémem pomocí KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Zadejte adresu URL tajného kódu při připojení disku s operačním systémem.

###  <a name="without-using-a-kek"></a>Bez použití KEK
Když připojujete disk s operačním systémem, musíte předat `$secretUrl`. Adresa URL byla vygenerována v části "tajný klíč šifrování disku není zašifrovaný pomocí KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Použití KEK
Když připojíte disk s operačním systémem, předejte `$KeyEncryptionKey` a `$secretUrl`. Adresa URL se vygenerovala v části tajný klíč šifrování disku zašifrovaný pomocí KEK.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
