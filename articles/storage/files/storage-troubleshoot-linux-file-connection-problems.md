---
title: Řešení potíží se soubory Azure v systému Linux | Microsoft Docs
description: Řešení potíží se soubory Azure v systému Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: cc0539462fad0a73d5fc7eb75d2078e513df4e5d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926540"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Řešení potíží se soubory Azure v systému Linux

V tomto článku jsou uvedené běžné problémy, které se týkají souborů Azure, když se připojujete z klientů se systémem Linux. Poskytuje taky možné příčiny a řešení těchto problémů. 

Kromě kroků pro řešení potíží v tomto článku můžete použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) a zajistit tak, že klient pro Linux bude mít správné požadavky. AzFileDiagnostics automatizuje detekci většiny příznaků uvedených v tomto článku. Pomáhá nastavit vaše prostředí, aby získalo optimální výkon. Tyto informace můžete najít také v [poradci při potížích se sdílenými složkami souborů Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Poradce při potížích obsahuje kroky, které vám pomůžou s problémy s připojením, mapováním a připojením sdílených složek Azure Files.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nelze se připojit ke sdílené složce Azure nebo ji připojit

### <a name="cause"></a>Příčina

Mezi běžné příčiny tohoto problému patří:

- Používáte nekompatibilního klienta pro distribuci Linux. Pro připojení ke sdílené složce Azure doporučujeme použít následující distribuce Linux:

|   | SMB 2.1 <br>(Připojení k virtuálním počítačům ve stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního prostředí a mezi oblastmi) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- V klientovi nejsou nainstalované nástroje CIFS (CIFS-util).
- Na klientovi není nainstalovaná minimální verze protokolu SMB/CIFS 2,1.
- Šifrování SMB 3,0 není na klientovi podporováno. Předchozí tabulka poskytuje seznam distribucí pro Linux, které podporují připojení z místního prostředí a mezi oblastmi pomocí šifrování. Ostatní distribuce vyžadují jádro verze 4.11 nebo novější.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, což není podporováno.
- Pokoušíte se připojit ke sdílené složce Azure z virtuálního počítače Azure a virtuální počítač není ve stejné oblasti jako účet úložiště.
- Pokud je v účtu úložiště povolené nastavení [vyžadováno zabezpečeným přenosem]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) , soubory Azure umožní jenom připojení, která používají protokol SMB 3,0 se šifrováním.

### <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, použijte [Nástroj pro řešení potíží s chybami při připojování souborů Azure pro Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj:

* Pomáhá ověřit spuštěné prostředí klienta.
* Zjistí nekompatibilní konfiguraci klienta, která by způsobila selhání přístupu pro soubory Azure.
* Poskytuje doporučené pokyny pro opravení sebe.
* Shromažďuje diagnostické trasování.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Chyba připojení (13): oprávnění odepřeno" při připojení sdílené složky Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Příčina 1: nešifrovaný komunikační kanál

Z bezpečnostních důvodů se připojení ke sdíleným složkám Azure blokují, když komunikační kanál není šifrovaný a když k pokusu o připojení nedošlo ze stejného datacentra, ve kterém se sdílená složka Azure nachází. Nešifrovaná připojení ze stejného datacentra se můžou blokovat také v případě, že je pro účet úložiště povolené nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Šifrovaný komunikační kanál je k dispozici pouze v případě, že klientský operační systém uživatele podporuje šifrování protokolu SMB.

Další informace najdete v části [Požadavky na připojení sdílené složky Azure v Linuxu s využitím balíčku cifs-utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Připojte se z klienta, který podporuje šifrování přes protokol SMB, nebo se připojte z virtuálního počítače ve stejném datovém centru jako účet úložiště Azure, který se používá pro sdílenou složku Azure.
2. Pokud klient nepodporuje šifrování protokolu SMB, ověřte, že je v účtu úložiště zakázané nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) .

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 2: v účtu úložiště jsou povolená pravidla virtuální sítě nebo brány firewall. 

Pokud jsou pro účet úložiště nakonfigurovaná pravidla virtuální sítě nebo brány firewall, síťovému provozu se odepře přístup, dokud se nepovolí přístup z virtuální sítě nebo IP adresy klienta.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>Při pokusu o otevření souboru se překročila kvóta disku [oprávnění byla odepřena].

V systému Linux se zobrazí chybová zpráva podobná následující:

**\<filename > [oprávnění odepřeno] překročena kvóta disku**

### <a name="cause"></a>Příčina

Dosáhli jste horního limitu souběžných otevřených popisovačů, které jsou povoleny pro určitý soubor.

V jednom souboru je kvóta 2 000 otevřených popisovačů. Když máte 2 000 otevřených popisovačů, zobrazí se chybová zpráva oznamující, že se dosáhlo kvóty.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů uzavřením některých popisovačů a potom operaci opakujte.

Pokud chcete zobrazit otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu PowerShellu [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) .  

Chcete-li zavřít otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) prostředí PowerShell.

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí AZ PowerShell Module verze 2,4 nebo novější. Pokud chcete nainstalovat nejnovější modul AZ PowerShellu, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Pomalé kopírování souborů do a ze souborů Azure v systému Linux

- Pokud nemáte konkrétní minimální požadavky na vstupně-výstupní operace, doporučujeme použít 1 MiB jako velikost vstupně-výstupních operací pro zajištění optimálního výkonu.
- Použijte pravou metodu kopírování:
    - Použijte [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro jakýkoli přenos mezi dvěma sdílenými složkami souborů.
    - Použití CP nebo DD s paralelním může zlepšit rychlost kopírování. počet vláken závisí na vašem případu použití a na zatížení. Následující příklady používají šest: 
    - CP – příklad (CP použije výchozí velikost bloku systému souborů jako velikost bloku): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - DD příklad (Tento příkaz explicitně nastaví velikost bloku na 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Open Source nástroje třetích stran, jako jsou:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) – seřadí soubory a zabalí je do oddílů.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) – používá Fpart a nástroj pro kopírování k vytvoření více instancí k migraci dat z src_dir na dst_url.
        - [Více](https://github.com/pkolano/mutil) vláken CP a md5sum s více vlákny založené na systému GNU coreutils.
- Nastavení velikosti souboru předem. místo toho, aby bylo možné zapisovat do zápisu, pomáhá vylepšit rychlost kopírování ve scénářích, kde je známá velikost souboru. Pokud se vyžaduje rozšíření zápisů, můžete nastavit velikost cílového souboru pomocí příkazu `truncate - size <size><file>`. Poté `dd if=<source> of=<target> bs=1M conv=notrunc`příkaz zkopíruje zdrojový soubor, aniž by bylo nutné opakovaně aktualizovat velikost cílového souboru. Můžete například nastavit velikost cílového souboru pro každý soubor, který chcete zkopírovat (předpokládá se, že je sdílená složka připojená pod/mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - a pak zkopírujte soubory bez rozšíření zápisu paralelně: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Chyba připojení (115): operace nyní probíhá" při připojování souborů Azure pomocí protokolu SMB 3,0

### <a name="cause"></a>Příčina

Některé distribuce Linuxu ještě nepodporují funkce šifrování v protokolu SMB 3.0. Uživatelům se při pokusu o připojení služby Azure Files pomocí protokolu SMB 3.0 může zobrazit chybová zpráva 115 kvůli chybějící funkci. Protokol SMB 3.0 s úplným šifrováním se podporuje pouze při použití Ubuntu 16.04 nebo novější verze.

### <a name="solution"></a>Řešení

Funkce šifrování pro protokol SMB 3.0 pro Linux se zavedla v jádru verze 4.11. Tato funkce umožňuje připojení sdílené složky Azure z místního prostředí nebo z jiné oblasti Azure. Tato funkce je součástí distribucí Linuxu uvedených v části [Minimální doporučené verze s odpovídajícími možnostmi připojení (protokol SMB verze 2.1 vs. protokol SMB verze 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Ostatní distribuce vyžadují jádro verze 4.11 nebo novější.

Pokud váš linuxový klient SMB nepodporuje šifrování, připojte službu Azure Files pomocí protokolu SMB 2.1 z virtuálního počítače Azure s Linuxem, který je ve stejném datacentru jako sdílená složka. Ověřte, že je pro účet úložiště zakázané nastavení [Vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Při procházení sdílené složky Azure na portálu došlo k chybě "Chyba autorizace"

Když přejdete na sdílenou složku Azure na portálu, může se zobrazit následující chyba:

Selhání autorizace  
Nemáte přístup.

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Příčina 1: váš uživatelský účet nemá přístup k účtu úložiště.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

Přejděte k účtu úložiště, kde se nachází sdílená složka Azure, klikněte na **řízení přístupu (IAM)** a ověřte, jestli má uživatelský účet přístup k účtu úložiště. Další informace najdete v tématu [zabezpečení účtu úložiště pomocí Access Control na základě rolí (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 2: v účtu úložiště jsou povolená pravidla virtuální sítě nebo brány firewall.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nepovedlo se odstranit soubor nebo adresář ve sdílené složce Azure.

### <a name="cause"></a>Příčina
K tomuto problému obvykle dochází, pokud má soubor nebo adresář otevřený popisovač. 

### <a name="solution"></a>Řešení

Pokud klienti SMB zavřeli všechny otevřené popisovače a problém přetrvává, udělejte toto:

- K zobrazení otevřených popisovačů použijte rutinu prostředí PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) .

- K zavření otevřených popisovačů použijte rutinu [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) prostředí PowerShell. 

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí AZ PowerShell Module verze 2,4 nebo novější. Pokud chcete nainstalovat nejnovější modul AZ PowerShellu, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Pomalý výkon sdílené složky Azure připojené k virtuálnímu počítači se systémem Linux

### <a name="cause-1-caching"></a>Příčina 1: ukládání do mezipaměti

Jednou z možných příčin pomalého výkonu je zakázané ukládání do mezipaměti. Ukládání do mezipaměti může být užitečné, pokud k souboru přistupujete opakovaně, jinak může to být režie. Ověřte, zda používáte mezipaměť, a poté ji zakazování.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

Pokud chcete zkontrolovat, jestli je ukládání do mezipaměti zakázané, vyhledejte položku **cache =** .

**Cache = None** znamená, že ukládání do mezipaměti je zakázáno. Opětovně připojte sdílenou složku pomocí příkazu výchozí připojení nebo explicitně přidáním možnosti **cache = Strict** do příkazu Mount, aby bylo zajištěno, že je povoleno ukládání do mezipaměti nebo striktní režim ukládání do mezipaměti.

V některých scénářích může možnost **serverino** Mount způsobit, že příkaz **ls** spustí příkaz stat pro každou položku adresáře. Výsledkem tohoto chování je snížení výkonu při výpisu velkého adresáře. V položce **/etc/fstab** můžete ověřit možnosti připojení:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Pomocí příkazu **sudo Mount | grep CIFS** a kontrolou jeho výstupu můžete taky zkontrolovat, jestli se používají správné možnosti. Následuje příklad výstupu:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Pokud možnost **cache = Strict** nebo **serverino** není k dispozici, odpojte a připojte soubory Azure znovu spuštěním příkazu Mount z [dokumentace](../storage-how-to-use-files-linux.md). Pak znovu zkontrolujte, jestli má položka **/etc/fstab** správné možnosti.

### <a name="cause-2-throttling"></a>Příčina 2: omezování

Je možné, že máte omezení a vaše požadavky jsou odesílány do fronty. To můžete ověřit využitím [Azure Storage metriky v Azure monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ujistěte se, že je vaše aplikace v rámci [cíle škálování souborů Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Při kopírování souborů z Windows do Linux se ztratila časová razítka.

Na platformách systému Linux/UNIX se příkaz **CP-p** nezdařil, pokud si jiný uživatel vlastní soubor 1 a soubor 2.

### <a name="cause"></a>Příčina

Příznak vynucení **f** ve výsledku příkazu COPYFILE spouští **CP-p-f** v systému UNIX. Tento příkaz také nedokáže zachovat časové razítko souboru, který nevlastníte.

### <a name="workaround"></a>Alternativní řešení

Pro kopírování souborů použijte uživatele účtu úložiště:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nejde získat přístup k&lt;cestě&gt;: Chyba vstupu/výstupu.

Když se pokusíte zobrazit seznam souborů ve sdílené složce Azure pomocí příkazu ls, příkaz se při výpisu souboru přestane reagovat. Zobrazí se následující chyba:

**ls: nejde získat přístup k&lt;cestě&gt;: Chyba vstupu/výstupu.**


### <a name="solution"></a>Řešení
Upgradujte jádro systému Linux na následující verze, které mají opravu tohoto problému:

- 4.4.87 +
- 4.9.48 +
- 4.12.11 +
- Všechny verze, které jsou větší nebo rovny 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nejde vytvořit symbolické odkazy – LN: nepovedlo se vytvořit symbolický odkaz t: operace není podporovaná.

### <a name="cause"></a>Příčina
Ve výchozím nastavení připojení sdílených složek Azure v systému Linux pomocí protokolu CIFS nepovoluje podporu pro symbolické odkazy (symbolických odkazů). Zobrazí se chyba podobná této:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Řešení
Klient CIFS systému Linux nepodporuje vytváření symbolických odkazů ve stylu Windows prostřednictvím protokolu SMB 2 nebo 3. V současné době podporuje klient Linux jiný styl symbolických odkazů s názvem [Minshall + francouzština symbolických odkazů](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) pro vytváření i následné operace. Zákazníci, kteří potřebují symbolické odkazy, mohou používat možnost připojení "mfsymlinks". Doporučujeme "mfsymlinks", protože se jedná také o formát, který Mac používá.

Pokud chcete používat symbolických odkazů, přidejte na konec příkazu CIFS Mount následující:

```
,mfsymlinks
```

Takže příkaz vypadá nějak takto:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Pak můžete vytvořit symbolických odkazů podle doporučení na [wikiwebu](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Chyba připojení (112): hostitel je mimo provoz, protože došlo k vypršení časového limitu připojení.

K chybě připojení 112 dochází v klientovi s Linuxem při dlouhodobé nečinnosti klienta. Po delší době nečinnosti se klient odpojí a vyprší platnost připojení.  

### <a name="cause"></a>Příčina

Připojení může být nečinné z následujících důvodů:

-   Selhání síťové komunikace, která brání opětovnému navázání připojení k serveru přes protokol TCP, při použití výchozí možnosti dočasného připojení
-   Nedávné opravy opětovného připojení, které nejsou ve starších jádrech

### <a name="solution"></a>Řešení

Tento problém s opětovným připojením v jádru Linuxu je teď opravený v rámci následujících změn:

- [Oprava opětovného připojení, aby se neodkládalo opětovné připojení k relaci protokolu SMB 3 dlouho po opětovném připojení k socketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Volání služby Echo okamžitě po opětovném připojení k socketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Oprava možného poškození paměti během opětovného připojení](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Oprava možného dvojitého zamykání mutex během opětovného připojení (pro jádro v 4.9 a novější)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Tyto změny se však ještě nemusely přenést do všech distribucí Linuxu. Tuto opravu a další opravy opětovného připojení najdete v části [Minimální doporučené verze s odpovídajícími možnostmi připojení (protokol SMB verze 2.1 vs. protokol SMB verze 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) článku [Použití služby Azure Files s Linuxem](storage-how-to-use-files-linux.md). Tuto opravu můžete získat upgradem na některou z těchto doporučených verzí jádra.

### <a name="workaround"></a>Alternativní řešení

Tento problém můžete obejít tím, že zadáte trvalé připojení. Trvalé připojení přinutí klienta, aby počkal na navázání nebo explicitní přerušení připojení. S jeho využitím můžete zabránit chybám způsobeným vypršením časového limitu sítě. Toto alternativní řešení však může způsobit nekonečné čekání. Buďte připraveni v případě potřeby zastavit připojení.

Pokud nemůžete upgradovat na nejnovější verze jádra, můžete tento problém obejít tím, že ve sdílené složce Azure budete uchovávat soubor, do kterého budete každých maximálně 30 sekund zapisovat. Musí se jednat o operaci zápisu, jako je přepsání vytvořeného nebo upraveného data souboru. Jinak můžete dostávat výsledky uložené v mezipaměti a vaše operace nemusí aktivovat opětovné připojení.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.
