---
title: Řešení potíží s Azure Files v Linuxu | Dokumentace Microsoftu
description: Řešení potíží se soubory Azure v Linuxu
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 97f737c8d1228bd03baf59f2ebe830f715241299
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449844"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Řešení potíží s Azure Files v Linuxu

Tento článek uvádí běžné problémy, které se vztahují k Azure Files při připojení klientů systému Linux. Poskytuje také možné příčiny a řešení těchto problémů. 

Kromě použijte kroky v tomto článku, můžete použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) zajistit správné požadavky klienta pro Linux. AzFileDiagnostics automatizuje detekce většinu z příznaků uvedených v tomto článku. Umožňuje nastavit prostředí, abyste získali optimální výkon. Můžete také najít tyto informace [Azure sdíleným složkám Poradce při potížích](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Poradce při potížích se vysvětluje, jak vám pomůžou se potíže s připojením, mapování a připojování sdílených složek Azure.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nelze se připojit k nebo připojení sdílené složky Azure

### <a name="cause"></a>Příčina

Mezi běžné příčiny tohoto problému patří:

- Používáte klientem nekompatibilní distribuce Linuxu. Doporučujeme použít následující Linuxových distribucí pro připojení sdílené složky Azure:

|   | SMB 2.1 <br>(Připojení na virtuálních počítačích v rámci stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního a mezi oblastmi) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- V klientském počítači nejsou nainstalované nástroje CIFS (cfs utils).
- Minimální verze protokolu SMB/CIFS, 2.1, není nainstalována na straně klienta.
- Šifrování SMB 3.0 se nepodporuje na straně klienta. Předchozí tabulka obsahuje seznam distribucí systému Linux tuto podporu připojení z místního a mezi oblastmi pomocí šifrování. Ostatní distribuce vyžadují jádra 4.11 a novějších verzích.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, což není podporováno.
- Pokoušíte se připojit ke sdílené složky Azure z virtuálního počítače Azure a virtuální počítač není ve stejné oblasti jako účet úložiště.
- Pokud [vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pro účet úložiště je povolené nastavení, soubory Azure vám umožní pouze připojení využívající šifrování protokolu SMB 3.0.

### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, použijte [řešení potíží s nástrojem pro soubory Azure chyby připojení v Linuxu](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj:

* Umožňuje ověření klienta, spouštění prostředí.
* Zjistí nekompatibilní klienta konfigurace, která může způsobit selhání přístupu pro soubory Azure.
* Poskytuje doporučený postup na místním řešení.
* Shromažďuje trasování diagnostiky.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Připojit error(13): Přístup byl odepřen"po připojení sdílené složky Azure

### <a name="cause-1-unencrypted-communication-channel"></a>1\. příčina: Nešifrovaná komunikace kanálu

Z bezpečnostních důvodů připojení sdílených složek Azure jsou blokovány, pokud není šifrovaný komunikační kanál, a pokud se pokus o připojení není proveden ze stejné datové centrum, kde jsou umístěné sdílených složek Azure. Nezašifrované připojení ve stejném datacentru je taky možné zablokovat Pokud [vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pro účet úložiště je povolené nastavení. Šifrovaný komunikační kanál je k dispozici pouze v případě, že uživatele klientský operační systém podporuje šifrování protokolu SMB.

Další informace najdete v tématu [požadavky pro připojení Azure file sdílet se systémy Linux a balíček cifs utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Řešení příčiny 1

1. Připojení z klienta, který podporuje šifrování protokolu SMB nebo připojte z virtuálního počítače ve stejném datacentru jako účet služby Azure storage, který se používá pro sdílené složky Azure.
2. Ověřte, [vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) nastavení je zakázaný v účtu úložiště, pokud klient nepodporuje šifrování protokolu SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2\. příčina: Pravidla virtuální sítě nebo brány firewall jsou povolené v účtu úložiště 

Pokud virtuální síť (VNET) a pravidla brány firewall jsou nakonfigurované v účtu úložiště, síťový provoz bude odepřen přístup Pokud IP adresa klienta nebo virtuální sítě je povolený přístup.

### <a name="solution-for-cause-2"></a>Řešení příčiny 2

Ověřte, že virtuální sítě a pravidel brány firewall jsou správně nakonfigurovány v účtu úložiště. Pokud chcete otestovat, pokud virtuální síť nebo brána firewall pravidla je příčinou problému, dočasně změnit nastavení pro účet úložiště na **povolit přístup ze všech sítí**. Další informace najdete v tématu [virtuální sítí a bran firewall nakonfigurovat služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[bylo odepřeno oprávnění]: překročena kvóta disku" při pokusu o otevření souboru

V systému Linux obdržíte chybovou zprávu, která vypadá přibližně takto:

**\<Název souboru > byla překročena kvóta disku [oprávnění byla odepřena.]**

### <a name="cause"></a>Příčina

Bylo dosaženo horní limit počtu souběžných otevřených popisovačů, které jsou povoleny pro soubor.

Používá se kvóta 2 000 otevřenými popisovači v jednom souboru. Až budete mít 2 000 otevřené popisovače, se zobrazí chybová zpráva s upozorněním, že je dosaženo kvóty.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů ukončením některé obslužné rutiny a pak zkuste operaci zopakovat.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Zpomalit kopírování souborů do a z Azure Files v Linuxu

- Pokud nemáte konkrétní požadavek minimální velikost vstupně-výstupních operací, doporučujeme použít 1 MiB jako velikost vstupně-výstupní operace pro zajištění optimálního výkonu.
- Pokud znáte konečné velikosti souboru, který jste rozšířit pomocí zápisy a software nebude vyzkoušejte problémy s kompatibilitou při nepsaná tail na tento soubor obsahuje nulami, nastavte velikost souboru předem místo provedení při každém zápisu rozšiřování zápisu.
- Použijte metodu pravé kopie:
    - Použití [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro všechny přenosy mezi dvěma sdílenými složkami souborů.
    - Pomocí paralelní cp může zlepšit rychlost kopírování, počet vláken, závisí na případu použití a úloh. Tento příklad používá šest: `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - Open source nástroje třetí strany, jako:
        - [Paralelní GNU](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) – seřadí soubory a balíčky do oddílů.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -využívá Fpart a nástroj pro kopírování spustit víc instancí k migraci dat z src_dir do dst_url.
        - [Více](https://github.com/pkolano/mutil) -vícevláknové cp a md5sum podle GNU coreutils.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Připojit error(115): Nyní probíhá operace"Když připojíte soubory Azure pomocí protokolu SMB 3.0

### <a name="cause"></a>Příčina

Některých Linuxových distribucích zatím nepodporují funkce šifrování v SMB 3.0. Uživatelům může zobrazit zpráva "115" Chyba, pokud pokusu o připojení služby soubory Azure pomocí protokolu SMB 3.0 z důvodu chybějící funkce. Úplné šifrování protokolu SMB 3.0 je podporována pouze v případě, že používáte Ubuntu 16.04 nebo novější.

### <a name="solution"></a>Řešení

Funkce šifrování protokolu SMB 3.0 pro Linux byla zavedena v 4.11 jádra. Tato funkce umožňuje připojení sdílené složky Azure v místním nebo z jiné oblasti Azure. Tato funkce je zahrnutá v Linuxových distribucích uvedené v [minimální doporučené verze s odpovídající možností připojení (SMB verze 2.1 nebo SMB verze 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Ostatní distribuce vyžadují jádra 4.11 a novějších verzích.

Pokud klient Linux SMB nepodporuje šifrování, připojení Azure souborů pomocí protokolu SMB 2.1 z virtuálního počítače Linux Azure, který je ve stejném datacentru jako sdílené. Ověřte, že [vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) je zakázáno v účtu úložiště. 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Chyba "Ověření se nezdařilo" při přechodu na sdílené složky Azure na portálu

Při procházení sdílené složky Azure na portálu může zobrazit následující chyba:

Selhání autorizace  
Nemáte přístup

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>1\. příčina: Váš uživatelský účet nemá přístup k účtu úložiště

### <a name="solution-for-cause-1"></a>Řešení příčiny 1

Přejděte do účtu úložiště, kde se nachází sdílená složka Azure, klikněte na tlačítko **řízení přístupu (IAM)** a ověřte váš uživatelský účet má přístup k účtu úložiště. Další informace najdete v tématu [jak zabezpečit svůj účet úložiště pomocí řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2\. příčina: Pravidla virtuální sítě nebo brány firewall jsou povolené v účtu úložiště

### <a name="solution-for-cause-2"></a>Řešení příčiny 2

Ověřte, že virtuální sítě a pravidel brány firewall jsou správně nakonfigurovány v účtu úložiště. Pokud chcete otestovat, pokud virtuální síť nebo brána firewall pravidla je příčinou problému, dočasně změnit nastavení pro účet úložiště na **povolit přístup ze všech sítí**. Další informace najdete v tématu [virtuální sítí a bran firewall nakonfigurovat služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Nízký výkon na sdílenou složku Azure připojit na virtuálním počítači s Linuxem

### <a name="cause-1-caching"></a>1\. příčina: Caching

Jednou možnou příčinou nízkého výkonu je zakázáno ukládání do mezipaměti. Ukládání do mezipaměti může být užitečné že při přístupu k souboru opakovaně, v opačném případě může být další režií. Zaškrtněte, pokud používáte mezipaměti před jeho zakázání.

### <a name="solution-for-cause-1"></a>Řešení příčiny 1

Chcete-li zkontrolovat, zda je zakázáno ukládání do mezipaměti, vyhledejte **mezipaměti =** položka.

**Mezipaměti = none** označuje, že je zakázáno ukládání do mezipaměti. Připojte sdílenou složku s použitím výchozí příkaz připojení nebo tak, že explicitně přidáte **mezipaměti = striktní** je povolena možnost připojení příkaz, kterým zajistíte, že ukládání do mezipaměti výchozí nebo "přísné" režim ukládání do mezipaměti.

V některých případech **serverino** možností připojení může způsobit, že **ls** příkaz ke spuštění stat – pro každý záznam adresáře. Toto chování způsobí snížení výkonu už nabízíte velké adresáře. Možnosti připojení můžete zkontrolovat ve vaší **/etc/fstab** položky:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Můžete také zkontrolovat, zda se právě využívají správné možnosti pro spuštění **sudo připojení | grep cifs** příkazu a zkontrolujete jeho výstup. Následuje příklad výstupu:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Pokud **mezipaměti = striktní** nebo **serverino** možnost je k dispozici, odpojte a znovu připojit soubory Azure pomocí příkazu připojení z [dokumentaci](../storage-how-to-use-files-linux.md). Potom spusťte opětovnou kontrolu, která **/etc/fstab** položka má správné možnosti.

### <a name="cause-2-throttling"></a>2\. příčina: Throttling

Je možné, dochází k omezení šířky pásma a žádostí jsou odesílány do fronty. Můžete to ověřit tak využití [metrik Azure Storage ve službě Azure Monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Řešení příčiny 2

Ujistěte se vaše aplikace je v rámci [soubory Azure škálovat cíle](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Časová razítka bylo ztraceno v kopírování souborů z Windows do systému Linux

Na platformách Linux/Unix **cp -p** příkaz selže, pokud různí uživatelé vlastní soubor 1 a 2 souboru.

### <a name="cause"></a>Příčina

Příznak force **f** v COPYFILE výsledkem provádění **cp -p -f** v systému Unix. Tento příkaz také selže zachovat časové razítko souboru, kterou nevlastníte.

### <a name="workaround"></a>Alternativní řešení:

Použijte uživatelský účet úložiště pro kopírování souborů:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nelze se připojit k nebo připojení sdílené složky Azure

### <a name="cause"></a>Příčina

Mezi běžné příčiny tohoto problému patří:

- Používáte klientem nekompatibilní distribuce Linuxu. Doporučujeme použít následující Linuxových distribucí pro připojení sdílené složky Azure:

    |   | SMB 2.1 <br>(Připojení na virtuálních počítačích v rámci stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního a mezi oblastmi) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- V klientském počítači nejsou nainstalované nástroje CIFS (cifs utils).
- Minimální verze protokolu SMB/CIFS, 2.1, není nainstalována na straně klienta.
- Šifrování SMB 3.0 se nepodporuje na straně klienta. Šifrování SMB 3.0 je k dispozici v Ubuntu 16.4 a novějších verzích, společně s operačním systémem SUSE 12.3 a novějších verzích. Ostatní distribuce vyžadují jádra 4.11 a novějších verzích.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, což není podporováno.
- Pokoušíte se připojit ke sdílené složky Azure z virtuálního počítače Azure a virtuální počítač není ve stejné oblasti jako účet úložiště.
- Pokud [vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pro účet úložiště je povolené nastavení, soubory Azure vám umožní pouze připojení využívající šifrování protokolu SMB 3.0.

### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, použijte [řešení potíží s nástrojem pro soubory Azure chyby připojení v Linuxu](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj:

* Umožňuje ověření klienta, spouštění prostředí.
* Zjistí nekompatibilní klienta konfigurace, která může způsobit selhání přístupu pro soubory Azure.
* Poskytuje doporučený postup na místním řešení.
* Shromažďuje trasování diagnostiky.


## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: Nelze získat přístup k '&lt;cesta&gt;": Chyba vstupu/výstupu

Při pokusu o seznam souborů ve sdílené složky Azure pomocí příkazu ls, přestane reagovat při výpisu souborů příkazu. Zobrazí následující chyba:

**ls: Nelze získat přístup k '&lt;cesta&gt;": Chyba vstupu/výstupu**


### <a name="solution"></a>Řešení
Jádro Linuxu upgradujte následující verze, které mají opravu tohoto problému:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Všechny verze, které jsou větší než nebo rovna hodnotě 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nelze vytvořit symbolické odkazy - ln: se nepovedlo vytvořit symbolický odkaz, 't': Tato operace není podporována

### <a name="cause"></a>Příčina
Ve výchozím nastavení nemá připojení sdílené složky Azure v Linuxu pomocí CIFS povolení podpory pro symbolické odkazy (symbolických odkazů). Zobrazí se chyba takto:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Řešení
Klient Linux CIFS nepodporuje vytváření stylu Windows symbolické odkazy přes protokol SMB 2 nebo 3 protokolu. V současné době podporuje klient Linux jiný styl symbolické odkazy, které volá [Minshall + francouzština symbolických odkazů](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) pro obě vytvořit a sledovat operace. Zákazníci, kteří potřebují symbolické odkazy můžete použít možnost "mfsymlinks" připojení. Doporučujeme "mfsymlinks", protože je také formátu, který Mac používat.

Pokud chcete použít symbolických odkazů, přidejte následující na konec příkazu CIFS připojení:

```
,mfsymlinks
```

Takže příkaz vypadá přibližně takto:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Potom můžete vytvořit symbolických odkazů na jako navrhované [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Připojit error(112): Hostitel je mimo provoz"z důvodu vypršení časového limitu opětovné připojení

"112" připojení dojde k chybě na straně klienta pro Linux klienta byl nečinný po dlouhou dobu. Po delší dobu nečinné klient neodpojí a připojení vyprší časový limit.  

### <a name="cause"></a>Příčina

Z následujících důvodů může být připojení nečinné:

-   Chybám v komunikaci sítě, které brání obnovujete připojení protokolu TCP serveru při použití možnosti výchozí "text soft" připojení
-   Nejnovější opravy opětovné připojení, které nejsou k dispozici ve starší jádra

### <a name="solution"></a>Řešení

Tento problém opětovným připojením v linuxového jádra je opravená jako součást následující změny:

- [Oprava znovu připojit k není odložit smb3 relace znovu připojit po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Volání služby echo okamžitě po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Oprava poškození možné paměti při volání metody reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Opravit je to možné double uzamčení vzájemně vyloučeného přístupu při volání metody reconnect (pro jádra v4.9 a novější)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Však nemusí být tyto změny přenést ještě do Linuxových distribucí. Tato oprava a ostatní opravy opětovné připojení najdete v [minimální doporučené verze s odpovídající možností připojení (SMB verze 2.1 nebo SMB verze 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) část [použít soubory Azure s Linuxem](storage-how-to-use-files-linux.md)článku. Tato oprava můžete získat díky upgradu na některou z těchto verzí doporučené jádra.

### <a name="workaround"></a>Alternativní řešení:

Tento problém můžete vyřešit tak, že zadáte pevné připojení. Pevné připojení vynutí klient čekat, dokud se naváže spojení nebo dokud explicitně je přerušeno. Můžete ji chcete-li zabránit chybám z důvodu vypršení časových limitů sítě. Toto řešení však může způsobit neomezené čekání. Buďte připraveni zastavit připojení podle potřeby.

Pokud nelze upgradovat na nejnovější verze jádra, můžete tento problém vyřešit tím, že soubor do sdílené složky Azure, který píšete na každých 30 sekund nebo méně. Toto musí být operace zápisu, jako je například přepisování vytvořené nebo upravené datum na tento soubor. V opačném případě se mohou zobrazovat výsledky uložené v mezipaměti, a operace nemusí aktivovat obnovení připojení.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.
