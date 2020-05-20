---
title: Povolení služby Azure Disk Encryption pro virtuální počítače s Linuxem
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače se systémem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5c227c6ab24d6b71445354d1b17d238e80bf6313
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655843"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption pro virtuální počítače se systémem Linux 

Azure Disk Encryption přispívá k zabezpečení a ochraně vašich dat, aby byly splněny závazky organizace související se zabezpečením a dodržováním předpisů. Používá funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytování šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a je integrována s [Azure Key Vault](../../key-vault/index.yml) , která vám pomůžou řídit a spravovat klíče šifrování disku a tajné kódy. 

Pokud používáte [Azure Security Center](../../security-center/index.yml), budete upozorněni v případě, že máte virtuální počítače, které nejsou šifrované. Výstrahy se zobrazují jako Vysoká závažnost a doporučení slouží k šifrování těchto virtuálních počítačů.

![Výstraha Azure Security Center šifrování disku](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
> - Některá doporučení můžou zvýšit využití dat, sítě nebo výpočetních prostředků, což má za následek další licence nebo náklady na předplatné. Abyste mohli vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.
> - V současné době generace 2 virtuální počítače nepodporují Azure Disk Encryption. Podrobnosti najdete v tématu [Podpora pro virtuální počítače 2. generace v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) .

Základní informace o nástroji Azure Disk Encryption pro Linux najdete během několika minut pomocí [virtuálního počítače se systémem Linux pomocí Azure CLI](disk-encryption-cli-quickstart.md) nebo [Vytvoření a šifrování virtuálního počítače se systémem linux pomocí nástroje Azure PowerShell rychlý Start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Podporované virtuální počítače a operační systémy

### <a name="supported-vms"></a>Podporované virtuální počítače

Virtuální počítače se systémem Linux jsou k dispozici v [různých velikostech](sizes.md). Azure Disk Encryption není k dispozici na virtuálních počítačích [Basic, a-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ani na virtuálních počítačích, které nesplňují tyto minimální požadavky na paměť:

| Virtuální počítač | Minimální požadavek na paměť |
|--|--|
| Virtuální počítače se systémem Linux jenom při šifrování datových svazků| 2 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a v případě použití systému souborů root (/) je 4 GB nebo méně | 8 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a využití systému souborů root (/) je větší než 4 GB | Použití kořenového souborového systému * 2. Například 16 GB použití kořenového systému souborů vyžaduje aspoň 32 GB paměti RAM. |

Po dokončení procesu šifrování disku s operačním systémem u virtuálních počítačů se systémem Linux lze virtuální počítač nakonfigurovat tak, aby běžel s méně paměti. 

Azure Disk Encryption je k dispozici také pro virtuální počítače s Premium Storage.

Azure Disk Encryption není k dispozici pro [virtuální počítače 2. generace](generation-2.md#generation-1-vs-generation-2-capabilities)a [virtuální počítače řady Lsv2-Series](../lsv2-series.md)). Další výjimky naleznete v tématu [Azure Disk Encryption: nepodporované scénáře](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Podporované operační systémy

Azure Disk Encryption je podporovaná u podmnožiny [distribucí systému Linux schváleného službou Azure](endorsed-distros.md), což je podmnožina všech možných distribucí serveru se systémem Linux.

![Vennův diagram distribucí serveru Linux, které podporují Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Distribuce serverů pro Linux, které nejsou schváleny v Azure, nepodporují Azure Disk Encryption; z těch, které jsou schváleny, podporuje pouze následující distribuce a verze Azure Disk Encryption:

| Publisher | Nabídka | Skladová jednotka (SKU) | NÁZVEM | Typ svazku podporovaný pro šifrování |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18,04 – LTS | Kanonický: UbuntuServer: 18.04-LTS: nejnovější | Operační systém a datový disk |
| Canonical | Ubuntu 18.04 | 18,04-DENNĚ – LTS | Kanonický: UbuntuServer: 18.04-DAILY-LTS: nejnovější | Operační systém a datový disk |
| Canonical | Ubuntu 16.04 | 16,04-DENNĚ – LTS | Kanonický: UbuntuServer: 16.04-DAILY-LTS: nejnovější | Operační systém a datový disk |
| Canonical | Ubuntu 14.04.5</br>[s vyladěným jádrem Azure se aktualizovala na 4,15 nebo novější.](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Kanonický: UbuntuServer: 14.04.5-LTS: nejnovější | Operační systém a datový disk |
| Canonical | Ubuntu 14.04.5</br>[s vyladěným jádrem Azure se aktualizovala na 4,15 nebo novější.](disk-encryption-troubleshooting.md) | 14.04.5 – DENNĚ – LTS | Kanonický: UbuntuServer: 14.04.5-DAILY-LTS: nejnovější | Operační systém a datový disk |
| RedHat | RHEL 7,7 | 7.7 | RedHat: RHEL: 7.7: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,7 | 7 – RAW | RedHat: RHEL: 7-RAW: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,7 | 7 – LVM | RedHat: RHEL: 7-LVM: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7.5 | 7,5 | RedHat: RHEL: 7.5: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,4 | 7.4 | RedHat: RHEL: 7.4: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: nejnovější | Operační systém a datový disk (viz poznámka níže) |
| RedHat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6.8: nejnovější | Datový disk (viz poznámka níže) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6.7: nejnovější | Datový disk (viz poznámka níže) |
| OpenLogic | CentOS 7,7 | 7.7 | OpenLogic: CentOS: 7.7: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7,7 | 7 – LVM | OpenLogic: CentOS: 7-LVM: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic: CentOS: 7.5: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic: CentOS: 7.4: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7,3 | 7.3 | OpenLogic: CentOS: 7.3: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7.2 n | 7.2 n | OpenLogic: CentOS: 7.2 n: nejnovější | Operační systém a datový disk |
| OpenLogic | CentOS 7,1 | 7.1 | OpenLogic: CentOS: 7.1: nejnovější | Pouze datový disk |
| OpenLogic | CentOS 7,0 | 7.0 | OpenLogic: CentOS: 7.0: nejnovější | Pouze datový disk |
| OpenLogic | CentOS 6,8 | 6.8 | OpenLogic: CentOS: 6.8: nejnovější | Pouze datový disk |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE-přestupnost: 42.3: nejnovější | Pouze datový disk |
| SUSE | SLES 12 – SP4 | 12. SP4 | SUSE: SLES: 12-SP4: nejnovější | Pouze datový disk |
| SUSE | SLES HPC 12 – SP3 | 12. SP3 | SUSE: SLES-HPC: 12-SP3: nejnovější | Pouze datový disk |

> [!NOTE]
> Nová implementace Azure Disk Encryption je podporovaná pro RHEL operační systém a datový disk pro Image RHEL7 s průběžnými platbami.  
>
> ADE se podporuje taky pro zlaté RHELy s využitím vlastního předplatného, ale až **po** registraci předplatného. Další informace najdete v tématu [Red Hat Enterprise Linux na základě vlastních předplatných Gold v Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) .

## <a name="additional-vm-requirements"></a>Další požadavky na virtuální počítače

Azure Disk Encryption vyžaduje, aby byly v systému přítomné moduly dm-crypt a vfat. Odebráním nebo zakázáním VFAT z výchozí image znemožníte systému číst klíč a získat klíč potřebný k odemknutí disků při dalším restartování. Kroky pro posílení zabezpečení systému, které odebírají modul VFAT ze systému, nejsou kompatibilní s Azure Disk Encryption. 

Než povolíte šifrování, datové disky, které mají být zašifrované, musí být správně uvedené v adresáři/etc/fstab.. Při vytváření položek použít možnost "neúspěch" a zvolit název trvalého blokového zařízení (jako názvy zařízení ve formátu "/dev/sdX" nemusí být přidružen ke stejnému disku během restartování, zejména po šifrování; Další informace o tomto chování najdete v tématu: [řešení potíží se změnami názvů zařízení s platformou Linux VM](troubleshoot-device-names-problems.md)).

Ujistěte se, že nastavení/etc/fstab jsou správně nakonfigurovaná pro připojení. Chcete-li nakonfigurovat tato nastavení, spusťte příkaz Mount-a, restartujte virtuální počítač a aktivujte znovu připojení tímto způsobem. Až to bude hotové, zkontrolujte výstup příkazu lsblk a ověřte, jestli je jednotka pořád připojená. 

- Pokud soubor/etc/fstab před povolením šifrování nepřipojí jednotku správně, Azure Disk Encryption nebude moci správně připojit.
- Proces Azure Disk Encryption přesune informace o připojení z/etc/fstab a do vlastního konfiguračního souboru jako součást procesu šifrování. Nezobrazují se upozornění, aby se po dokončení šifrování datové jednotky v/etc/fstab zobrazila položka chybějící.
- Před zahájením šifrování nezapomeňte zastavit všechny služby a procesy, které by mohly být zapsány do připojených datových disků, a zakázat je, aby se po restartování nerestartoval automaticky. V těchto oddílech můžou být soubory otevřené a brání tak postupu šifrování je znovu připojit, což způsobí selhání šifrování. 
- Po restartování bude trvat čas, než proces Azure Disk Encryption připojí nově zašifrované disky. Po restartování počítače nebudou okamžitě k dispozici. Proces potřebuje čas ke spuštění, odemčení a připojení šifrovaných jednotek, než je k dispozici pro přístup k ostatním procesům. Tento proces může trvat déle než minutu po restartování v závislosti na charakteristikách systému.

Tady je příklad příkazů použitých k připojení datových disků a vytvoření nezbytných/etc/fstabch položek:

```bash
UUID0="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun0)"
UUID1="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun1)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Požadavky na síť

Aby bylo možné povolit funkci Azure Disk Encryption, musí virtuální počítače se systémem Linux splňovat následující požadavky konfigurace koncového bodu sítě:
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač se systémem Linux schopný připojit se k Azure Active Directory koncovému bodu \[ Login.microsoftonline.com \] .
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač se systémem Linux schopný se připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač se systémem Linux musí být schopný připojit se ke koncovému bodu Azure Storage, který je hostitelem úložiště rozšíření Azure a účtu úložiště Azure, který je hostitelem souborů VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Požadavky na úložiště šifrovacího klíče  

Azure Disk Encryption vyžaduje Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku. Váš Trezor klíčů a virtuální počítače se musí nacházet ve stejné oblasti a předplatném Azure.

Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologie
Následující tabulka popisuje některé běžné výrazy používané v dokumentaci ke službě Azure Disk Encryption:

| Terminologie | Definice |
| --- | --- |
| Azure Key Vault | Key Vault je kryptografická služba pro správu klíčů založená na modulech zabezpečení hardwaru FIPS (Federal Information Processing Standards) ověřené. Tyto standardy usnadňují ochranu kryptografických klíčů a citlivých tajných klíčů. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) je optimalizováno pro správu a správu prostředků Azure z příkazového řádku.|
| DM-crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) je transparentní podsystém šifrování disku založený na systému Linux, který slouží k povolení šifrování disku na virtuálních počítačích se systémem Linux. |
| Klíč šifrování klíče (KEK) | Asymetrický klíč (RSA 2048), který můžete použít k ochraně nebo zabalení tajného klíče. Můžete poskytnout klíč chráněný modulem hardwarového zabezpečení (HSM) nebo klíč chráněný softwarem. Další informace najdete v dokumentaci [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a [vytváření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md). |
| Rutiny prostředí PowerShell | Další informace najdete v tématu [rutiny Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Další kroky

- [Rychlý Start – vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure CLI](disk-encryption-cli-quickstart.md)
- [Rychlý Start – vytvoření a šifrování virtuálního počítače se systémem Linux pomocí prostředí Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem](disk-encryption-linux.md)
- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](disk-encryption-key-vault.md)


