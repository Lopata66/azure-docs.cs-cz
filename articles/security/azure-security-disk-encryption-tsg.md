---
title: Řešení potíží – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek poskytuje tipy pro řešení potíží pro Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/04/2019
ms.custom: seodec18
ms.openlocfilehash: 7e2fa8c526a1016e5b8157f5f8b3ecb38bf8ef15
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779959"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží Azure Disk Encryption

Tento průvodce je pro odborníky v oblasti IT, analytikům zabezpečení informací a správcům cloudů, jejichž organizace používat Azure Disk Encryption. Tento článek je pomoci při řešení problémů souvisejících s disk encryption.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Řešení potíží s šifrování disku operačního systému Linux

Šifrování disku operačního systému (OS) Linux musí před spuštěním procesu šifrování celého disku odpojit jednotku operačního systému. Pokud ho nelze odpojit jednotku, chybová zpráva o "se nepodařilo odpojit po..." by mohla nastat.

Této chybě může dojít, když zkusí se šifrování disku operačního systému v prostředí cílového virtuálního počítače, který se změnil z podporovaných uložených Galerie obrázků. Odchylky od podporované image může narušovat tohoto rozšíření možnost odpojit jednotku operačního systému. Příklady odchylky může obsahovat následující položky:
- Přizpůsobené Image už neodpovídá podporovaném systému souborů nebo schéma vytváření oddílů.
- Velké aplikace, jako je například SAP, MongoDB, Apache Cassandra a Docker nejsou podporovány, když budou nainstalovány a spuštěny v operačním systému před šifrování. Azure Disk Encryption se nepodařilo vypnout tyto procesy bezpečně podle potřeby při přípravě jednotky operačního systému pro šifrování disku. Pokud stále aktivní procesy, které drží otevřené popisovače souborů na jednotce operačního systému, jednotka operačního systému nemůže být odpojeny, což vede k selhání při šifrování jednotky operačního systému. 
- Vlastní skripty, která spustí v blízkosti čas uzavření šifrování povolené, nebo pokud jakýkoli jiný nemění na virtuálním počítači během procesu šifrování. Tomuto konfliktu může dojít, pokud šablonu Azure Resource Manager definuje několik rozšíření současného provádění nebo pokud se rozšíření vlastních skriptů nebo jiné akce je spouštěný souběžně k šifrování disku. Serializace a izolaci těchto kroků mohou problém vyřešit.
- Před povolením šifrování, tak selhání kroku odpojení bylo nezakázala Linux rozšířeného zabezpečení (SELinux). Po dokončení šifrování může SELinux opětovně povolena.
- Disk s operačním systémem používá schéma Správce logických svazků (LVM). I když omezenou podporu pro disky data LVM je k dispozici, není disk s operačním systémem LVM.
- Nejsou splněny požadavky na minimální velikost paměti (7 GB je určeno pro šifrování disku operačního systému).
- Datové jednotky jsou rekurzivně připojený v adresáři /mnt/ nebo mezi sebou (například /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Další Azure Disk Encryption [požadavky](azure-security-disk-encryption-prerequisites.md) nejsou splněny pro Linux.

## <a name="bkmk_Ubuntu14"></a> Aktualizovat výchozí jádra pro Ubuntu 14.04 LTS

Ubuntu 14.04 LTS image se dodává s výchozí verze jádra 4.4. Tato verze jádra se o známý problém, ve kterém typu nedostatek paměti Killer nesprávně ukončuje příkaz dd během procesu šifrování operačního systému. Tato chyba byla opravena v nejnovější Azure vyladěný jádro Linuxu. Lze vyvarovat této chyby, před povolením šifrování v imagi, aktualizovat [Azure vyladěný jádra 4.15](https://packages.ubuntu.com/trusty/linux-azure) nebo později pomocí následujících příkazů:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po restartování virtuálního počítače do nového jádra může být nová verze jádra potvrzena pomocí:

```
uname -a
```

## <a name="unable-to-encrypt-linux-disks"></a>Nelze zašifrovat disky platformy Linux

V některých případech je zakázána Linux šifrování disku se zdá být zaseknuto v "Zahájeno šifrování disku operačního systému" a SSH. Proces šifrování může trvat přibližně 3 – 16 hodin na základní Galerie obrázků. Pokud přidáte více terabajt velikosti datových disků, tento proces může trvat dny.

Pořadí šifrování disku operačního systému Linux dočasně odpojí jednotky operačního systému. Potom provede blok po bloku šifrování celého disku s operačním systémem, než ji opět připojí ji ve stavu zašifrované. Na rozdíl od Azure Disk Encryption ve Windows Linux Disk Encryption neumožňuje souběžné použití objektu virtuálního počítače probíhá šifrování. Výkonové charakteristiky virtuálního počítače může být velký rozdíl čas potřebný k dokončení šifrování. Tyto vlastnosti obsahovat velikost disku a zda je standardní účet úložiště nebo úložiště úrovně premium (SSD).

Pokud chcete zkontrolovat stav šifrování, dotazovat **zpráva o průběhu** pole vrácená [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) příkaz. Při šifrované jednotky operačního systému virtuálního počítače přejde do stavu údržby a zakáže abychom zabránili případnému přerušení k aktuálnímu procesu SSH. **EncryptionInProgress** zprávy sestavy pro většinu času, zatímco probíhá šifrování. Několik hodin, **VMRestartPending** zobrazí se výzva k restartování virtuálního počítače. Příklad:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Poté, co se zobrazí výzva k restartování virtuálního počítače a po restartování virtuálního počítače, musíte počkat, 2 až 3 minuty, pro restartování a poslední postup provést v cíli. Zpráva stav se změní při šifrování je nakonec dokončí. Poté, co tato zpráva je k dispozici, až bude připravená k použití se očekává šifrované jednotky operačního systému a virtuálního počítače je připravená k použití znovu.

V těchto případech doporučujeme obnovení virtuálního počítače do snímku nebo zálohy provedené bezprostředně před šifrování:
   - Pokud tomu tak není pořadí restartování, je popsáno výše.
   - Pokud se informace o spuštění, zpráva o průběhu nebo jiných ukazatelů výkonu chyba hlásit šifrování operačního systému se nezdařilo během tohoto procesu. Příklad zprávy se chyba "se nepodařilo odpojit", který je popsaný v tomto průvodci.

Před dalším pokusem přehodnotit vlastnosti virtuálního počítače a ujistěte se, že jsou splněné všechny požadavky.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Řešení potíží s Azure Disk Encryption za bránou firewall
Při připojení je omezena bránu firewall, proxy požadavek nebo nastavení skupinu zabezpečení sítě, může být narušeno možnost rozšíření a provádět požadované úlohy. Toto narušení může vést k stavové zprávy, jako je například "Stav rozšíření ve virtuálním počítači není k dispozici." V očekávaných případech nepovede dokončit šifrování. Následující části mají některé běžné problémy brány firewall, které může prozkoumat.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna nastavení skupiny zabezpečení sítě, které jsou použity musí umožňovat stále koncového bodu podle zdokumentovaných síťovou konfiguraci [požadavky](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) pro šifrování disku.

### <a name="azure-key-vault-behind-a-firewall"></a>Služba Azure Key Vault za bránou firewall

Když je šifrování povoleno s [přihlašovacích údajů Azure AD.](azure-security-disk-encryption-prerequisites-aad.md), cílový virtuální počítač musí umožňovat připojení ke koncovým bodům Azure Active Directory a koncové body služby Key Vault. V části 56 a 59 se udržuje aktuální koncové body ověřování Azure Active Directory [Office 365 – adresy URL a rozsahy IP adres](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentaci. Key Vault pokyny najdete v dokumentaci o tom, jak [přístup k Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
Virtuální počítač musí mít přístup k [služby Azure Instance Metadata](../virtual-machines/windows/instance-metadata-service.md) koncový bod, který používá známá nesměrovatelných adres IP (`169.254.169.254`), který je přístupný pouze z v rámci virtuálního počítače.  Konfigurace proxy serveru, kterými nastavíte jinou místní provoz protokolu HTTP na tuto adresu (například přidání hlavičky X-předané-pro) nejsou podporovány.

### <a name="linux-package-management-behind-a-firewall"></a>Správa balíčků Linux za bránou firewall

Za běhu Azure Disk Encryption pro Linux spoléhá na systém správy balíčků distribuce cíl pro nainstalování nezbytných součástí potřebné před povolením šifrování. Pokud na nastavení brány firewall brání tomu nebudou moct stáhnout a nainstalovat tyto součásti virtuálního počítače, se očekává následující chyby. Postupy konfigurace pro tento systém správy balíčků můžou lišit podle distribuce. V systému Red Hat pokud proxy server je vyžadována, je třeba jistotu, že správce předplatného a yumu jsou nastavena správně. Další informace najdete v tématu [postupy řešení potíží Správce předplatného a yumu](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Řešení potíží s Windows Server 2016 Server Core

V systému Windows Server 2016 Server Core není k dispozici ve výchozím nastavení komponenta bdehdcfg. Tato součást vyžaduje Azure Disk Encryption. Používá se k rozdělení systémový svazek ze svazku operačního systému, která se provádí jenom jednou, dobu života virtuálního počítače. Tyto binární soubory se nevyžadují během novější operace šifrování.

Chcete-li tento problém obejít, zkopírujte následující čtyři soubory z virtuálního počítače dat Windows serveru 2016 System Center do stejného umístění na jádru serveru:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Zadejte následující příkaz:

   ```
   bdehdcfg.exe -target default
   ```

   3. Tento příkaz vytvoří 550 MB systémový oddíl. Restartování systému.

   4. Pomocí nástroje DiskPart zkontrolujte svazky a pak pokračujte.  

Příklad:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>Řešení potíží s stav šifrování 

Na portálu může zobrazit na disk jako šifrovaný i po jejím nešifrované ve virtuálním počítači.  Tato situace může nastat v případě nízké úrovně příkazy používají šifrování přímo z disku na virtuálním počítači, namísto použití vyšší úrovni Azure Disk Encryption příkazy pro správu.  Vyšší úroveň příkazy jenom obnovením z disku na virtuálním počítači, ale mimo virtuální počítač se také neaktualizují nastavení šifrování na úrovni důležité platformy a nastavení rozšíření spojená s virtuálním Počítačem.  Pokud tyto nejsou uloženy v zarovnání, platformu nebude možné nahlásit stav šifrování nebo zřídit virtuální počítač správně.   

Správně zakázat Azure Disk Encryption, spusťte ze známého funkčního stavu s povoleným šifrováním a pak použít [zakázat AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) a [odebrat AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) prostředí Powershell příkazy, nebo [az vm encryption zakázat](/cli/azure/vm/encryption) příkazu rozhraní příkazového řádku. 

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se dozvěděli informace o některé běžné problémy v Azure Disk Encryption a řešení těchto potíží. Další informace o této službě a její možnosti najdete v následujících článcích:

- [Použít šifrování disku ve službě Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Azure data šifrování v klidovém stavu](azure-security-encryption-atrest.md)
