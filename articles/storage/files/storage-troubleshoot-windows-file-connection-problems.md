---
title: Řešení potíží se soubory Azure v systému Windows | Microsoft Docs
description: Řešení potíží se soubory Azure v systému Windows. Podívejte se na časté problémy související se soubory Azure při připojení z klientů Windows a podívejte se na možná řešení.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e9384dd3865b106488dc8ec303b060736f23ded7
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797781"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Řešení potíží se službou Azure Files ve Windows

V tomto článku jsou uvedené běžné problémy související se soubory Microsoft Azure, když se připojujete z klientů Windows. Poskytuje taky možné příčiny a řešení těchto problémů. Kromě kroků pro řešení potíží v tomto článku můžete také použít [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)   a zajistit, aby klientské prostředí systému Windows mělo správné požadavky. AzFileDiagnostics automatizuje detekci většiny příznaků uvedených v tomto článku a pomáhá nastavit vaše prostředí, aby dosáhlo optimálního výkonu. Tyto informace můžete najít také v [poradci při potížích se sdílenými složkami souborů Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , který obsahuje postup, který vám pomůže s problémy při připojování/mapování/připojování sdílených složek Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Chyba 5 při připojení sdílené složky Azure

Když se pokusíte připojit sdílenou složku, může se zobrazit následující chyba:

- Došlo k systémové chybě 5. Přístup byl zamítnut.

### <a name="cause-1-unencrypted-communication-channel"></a>Příčina 1: nešifrovaný komunikační kanál

Z bezpečnostních důvodů se připojení ke sdíleným složkám Azure blokují, když komunikační kanál není šifrovaný a když k pokusu o připojení nedošlo ze stejného datacentra, ve kterém se sdílená složka Azure nachází. Nešifrovaná připojení ze stejného datacentra se můžou blokovat také v případě, že je pro účet úložiště povolené nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Šifrovaný komunikační kanál je k dispozici pouze v případě, že klientský operační systém uživatele podporuje šifrování protokolu SMB.

Windows 8, Windows Server 2012 a novější verze těchto systémů vyjednávají požadavky zahrnující protokol SMB 3.0, který podporuje šifrování.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Připojte se z klienta, který podporuje šifrování SMB (Windows 8, Windows Server 2012 nebo novější), nebo se připojte z virtuálního počítače ve stejném datovém centru jako účet Azure Storage, který se používá pro sdílenou složku Azure.
2. Pokud klient nepodporuje šifrování protokolu SMB, ověřte, že je v účtu úložiště zakázané nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) .

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 2: v účtu úložiště jsou povolená pravidla virtuální sítě nebo brány firewall. 

Pokud jsou pro účet úložiště nakonfigurovaná pravidla virtuální sítě nebo brány firewall, síťovému provozu se odepře přístup, dokud se nepovolí přístup z virtuální sítě nebo IP adresy klienta.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Příčina 3: oprávnění na úrovni sdílené složky nejsou při použití ověřování založeného na identitě správná

Pokud uživatelé přistupují ke sdílené složce Azure pomocí ověřování služby Active Directory (AD) nebo Azure Active Directory Domain Services (Azure služba AD DS), přístup ke sdílené složce selže a dojde k chybě "přístup byl odepřen", pokud jsou nesprávná oprávnění na úrovni sdílení. 

### <a name="solution-for-cause-3"></a>Řešení pro příčinu 3

Pokud chcete aktualizovat oprávnění na úrovni sdílené složky, přečtěte si téma [Přiřazení přístupových oprávnění k identitě](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Při připojení nebo odpojení sdílené složky Azure došlo k chybě 53, chyba 67 nebo chyba 87.

Když se pokusíte připojit sdílenou složku z místního prostředí nebo z jiného datového centra, může dojít k následujícím chybám:

- Došlo k systémové chybě 53. Síťová cesta se nenašla.
- Došlo k systémové chybě 67. Název sítě se nenašel.
- Došlo k systémové chybě 87. Parametr je nesprávný.

### <a name="cause-1-port-445-is-blocked"></a>Příčina 1: port 445 je blokován

Systémová chyba 53 nebo systémová chyba 67 může nastat, 445 Pokud je zablokovaná odchozí komunikace se službou Azure Files Datacenter. Souhrn poskytovatelů internetových služeb, kteří umožňují nebo neumožňují přístup z portu 445, najdete na webu [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pokud chcete zjistit, jestli brána firewall nebo poskytovatel internetových služeb blokuje port 445 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) , použijte nástroj nebo `Test-NetConnection` rutinu AzFileDiagnostics. 

Pokud chcete použít `Test-NetConnection` rutinu, musí být nainstalovaný modul Azure PowerShell. Další informace najdete v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-Az-ps) . Nezapomeňte nahradit `<your-storage-account-name>` a `<your-resource-group-name>` odpovídajícími názvy pro váš účet úložiště.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Pokud připojení proběhne úspěšně, měl by se zobrazit následující výstup:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> Výše uvedený příkaz vrátí aktuální IP adresu účtu úložiště. Není zaručeno, že tato IP adresa zůstane stejná, a kdykoli se může změnit. Nekódujte pevně tuto IP adresu do skriptů ani do konfigurace brány firewall.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

#### <a name="solution-1---use-azure-file-sync"></a>1. řešení – Použití Synchronizace souborů Azure
Azure File Sync může transformovat místní Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru, včetně SMB, NFS a FTPS. Synchronizace souborů Azure funguje na portu 443 a proto ji můžou používat klienti se zablokovaným portem 445 jako alternativní způsob přístupu ke službě Azure Files. [Naučte se, jak nastavit Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>2. řešení – Použití sítě VPN
Když nastavíte síť VPN na konkrétní účet úložiště, přenos přes Internet prochází zabezpečeným tunelovým propojením na rozdíl od Internetu. Pokud chcete získat přístup ke službě Azure Files z Windows, postupujte podle [pokynů k nastavení sítě VPN](storage-files-configure-p2s-vpn-windows.md).

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3. řešení – Odblokování portu 445 s pomocí poskytovatele internetových služeb nebo správce IT
Pracujte s vaším IT oddělením nebo poskytovatelem internetových služeb a otevřete port 445 odchozí do [rozsahů IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4. řešení – Použití nástrojů založených na rozhraní REST API, jako jsou Průzkumník služby Storage nebo PowerShell
Soubory Azure také kromě protokolu SMB podporují i REST. Přístup přes rozhraní REST funguje na portu 443 (standardní port TCP). Rozhraní REST API využívají různé nástroje, které nabízejí bohaté uživatelské rozhraní. [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) je jeden z nich. [Stáhněte a nainstalujte si Průzkumníka služby Storage](https://azure.microsoft.com/features/storage-explorer/) a připojte se ke své sdílené složce využívající službu Azure Files. Můžete také použít [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) , který také REST API uživatel.

### <a name="cause-2-ntlmv1-is-enabled"></a>Příčina 2: ověřovací NTLMv1 je povolená.

Pokud je na klientovi povolená komunikace ověřovací NTLMv1, může dojít k systémové chybě 53 nebo k systémové chybě 87. Služba Azure Files podporuje pouze ověřování pomocí protokolu NTLM v2. Klient s povoleným protokolem NTLM v1 je méně zabezpečený. Proto se blokuje komunikace služby Azure Files. 

Pokud chcete určit, jestli je toto příčinou chyby, ověřte, že je následující podklíč registru nastavený na hodnotu 3.

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Další informace najdete v tématu [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) na webu TechNet.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

V následujícím podklíči registru vraťte hodnotu **LmCompatibilityLevel** na výchozí hodnotu 3:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Chyba 1816 – pro zpracování tohoto příkazu není k dispozici dostatečná kvóta.

### <a name="cause"></a>Příčina

K chybě 1816 dochází po dosažení horní meze souběžných otevřených popisovačů, které jsou povoleny pro soubor nebo adresář ve sdílené složce Azure. Další informace najdete v článku věnovaném [cílům škálování služby Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets).

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů uzavřením některých popisovačů a potom akci opakujte. Další informace najdete v tématu [Kontrolní seznam pro výkon a škálovatelnost Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Pokud chcete zobrazit otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu PowerShellu [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) .  

Chcete-li zavřít otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) prostředí PowerShell.

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí AZ PowerShell Module verze 2,4 nebo novější. Pokud chcete nainstalovat nejnovější modul AZ PowerShellu, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Chyba "bez přístupu" při pokusu o přístup k sdílené složce Azure nebo její odstranění  
Když se pokusíte o přístup k sdílené složce Azure na portálu nebo ji odstranit, může se zobrazit následující chyba:

Bez přístupu  
Kód chyby: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 1: v účtu úložiště jsou povolená pravidla virtuální sítě nebo brány firewall.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Příčina 2: váš uživatelský účet nemá přístup k účtu úložiště.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Přejděte k účtu úložiště, kde se nachází sdílená složka Azure, klikněte na **řízení přístupu (IAM)** a ověřte, jestli má uživatelský účet přístup k účtu úložiště. Další informace najdete v tématu [zabezpečení účtu úložiště pomocí Access Control na základě rolí (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nejde odstranit soubor nebo adresář ve sdílené složce Azure
Při pokusu o odstranění souboru se může zobrazit následující chyba:

Zadaný prostředek je označený k odstranění klientem SMB.

### <a name="cause"></a>Příčina
K tomuto problému obvykle dochází, pokud má soubor nebo adresář otevřený popisovač. 

### <a name="solution"></a>Řešení

Pokud klienti SMB zavřeli všechny otevřené popisovače a problém přetrvává, udělejte toto:

- K zobrazení otevřených popisovačů použijte rutinu prostředí PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) .

- K zavření otevřených popisovačů použijte rutinu [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) prostředí PowerShell. 

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí AZ PowerShell Module verze 2,4 nebo novější. Pokud chcete nainstalovat nejnovější modul AZ PowerShellu, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Pomalé kopírování souborů do služby Azure Files a z ní ve Windows

Při pokusu o přenos souborů do služby Azure File Service se může zobrazit pomalý výkon.

- Pokud nemáte konkrétní minimální požadavky na vstupně-výstupní operace, doporučujeme použít 1 MiB jako velikost vstupně-výstupních operací pro zajištění optimálního výkonu.
-   Pokud znáte konečnou velikost souboru, který rozšiřujete o zápisy, a váš software nemá problémy s kompatibilitou, pokud nezapsaný konec souboru obsahuje nuly, nastavte velikost souboru předem místo toho, aby každý zápis nastavil rozšíření zápisu.
-   Použijte pravou metodu kopírování:
    -   Použijte [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro jakýkoli přenos mezi dvěma sdílenými složkami souborů.
    -   Použijte příkaz [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) mezi sdílenými složkami na místním počítači.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Předpoklady pro Windows 8.1 nebo Windows Server 2012 R2

U klientů se systémem Windows 8.1 nebo Windows Server 2012 R2 se ujistěte, že je nainstalovaná oprava hotfix [KB3114025](https://support.microsoft.com/help/3114025) . Tato oprava hotfix vylepšuje výkon vytváření a zavírání popisovačů.

Spuštěním následujícího skriptu můžete ověřit, zda byla oprava hotfix nainstalována:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Pokud je nainstalována oprava hotfix, zobrazí se následující výstup:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Image Windows Serveru 2012 R2 ve Azure Marketplace mají ve výchozím nastavení nainstalovanou opravu hotfix KB3114025 od prosince 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>V části "můj počítač" nebo "Tento počítač" není žádná složka s písmenem jednotky.

Pokud namapujete sdílenou složku Azure jako správce pomocí příkazu net use, zdá se, že tato sdílená složka chybí.

### <a name="cause"></a>Příčina

Ve výchozím nastavení se Průzkumník souborů Windows nespouští jako správce. Pokud spouštíte NET USE z příkazového řádku pro správu, namapujete síťovou jednotku jako správce. Vzhledem k tomu, že mapované jednotky jsou orientované na uživatele, uživatelský účet, který je přihlášen, nezobrazí jednotky, pokud jsou připojeny pod jiným uživatelským účtem.

### <a name="solution"></a>Řešení
Připojte sdílenou složku z příkazového řádku bez oprávnění správce. Případně můžete postupovat podle [tohoto tématu na TechNetu](https://technet.microsoft.com/library/ee844140.aspx) a nakonfigurovat hodnotu registru **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Příkaz NET USE se nezdařil, pokud účet úložiště obsahuje lomítko.

### <a name="cause"></a>Příčina

Příkaz net use interpretuje lomítko (/) jako parametr příkazového řádku. Pokud název vašeho uživatelského účtu začíná lomítkem, mapování jednotky se nezdařilo.

### <a name="solution"></a>Řešení

K vyřešení problému můžete použít některý z následujících kroků:

- Spusťte následující příkaz PowerShellu:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Pomocí dávkového souboru můžete spustit příkaz tímto způsobem:

  `Echo new-smbMapping ... | powershell -command –`

- Pokud chcete tento problém obejít, vložte dvojité uvozovky do tohoto klíče – Pokud se před lomítkem nejedná o první znak. Pokud je, buď použijte interaktivní režim a zadejte heslo samostatně, nebo znovu vygenerujte klíče, abyste získali klíč, který nezačíná lomítkem.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikace nebo služba nemůže získat přístup k jednotce připojené služby soubory Azure.

### <a name="cause"></a>Příčina

Jednotky jsou připojeny na uživatele. Pokud je vaše aplikace nebo služba spuštěná pod jiným uživatelským účtem, než je ten, který jednotku připojil, aplikace tuto jednotku neuvidí.

### <a name="solution"></a>Řešení

Použijte jedno z následujících řešení:

-   Připojte jednotku ze stejného uživatelského účtu, který obsahuje aplikaci. Můžete použít nástroj, jako je PsExec.
- Název a klíč účtu úložiště předejte do parametrů uživatelské jméno a heslo pro příkaz net use.
- Pomocí příkazu cmdkey přidejte přihlašovací údaje do Správce přihlašovacích údajů. Tuto operaci proveďte z příkazového řádku v kontextu účtu služby, a to buď pomocí interaktivního přihlášení, nebo pomocí `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Namapujte sdílenou složku přímo bez použití mapovaného písmene jednotky. Některé aplikace se nemusí znovu připojit ke správnému písmenu jednotky, takže použití úplné cesty UNC může být spolehlivější. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po provedení těchto pokynů se může zobrazit následující chybová zpráva, když spustíte příkaz NET USE pro účet System/Network Service: došlo k systémové chybě 1312. Zadaná přihlašovací relace neexistuje. Je možné, že již byla ukončena. " Pokud k tomu dojde, ujistěte se, že uživatelské jméno předané do příkazu net use zahrnuje informace o doméně (například: "[název účtu úložiště]. soubor. Core. Windows. NET").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Chyba: kopírujete soubor do cílového umístění, které nepodporuje šifrování.

Když se soubor zkopíruje přes síť, dešifruje se na zdrojovém počítači, přenáší se do prostého textu a v cíli se znovu zašifruje. Při pokusu o zkopírování zašifrovaného souboru se ale může zobrazit následující chyba: kopírujete soubor do cílového umístění, které šifrování nepodporuje. "

### <a name="cause"></a>Příčina
K tomuto problému může dojít, pokud používáte systém souborů EFS (Encrypting File System) (EFS). Soubory šifrované BitLockerem je možné zkopírovat do souborů Azure. Soubory Azure ale nepodporují systém souborů NTFS.

### <a name="workaround"></a>Alternativní řešení
Chcete-li zkopírovat soubor přes síť, je nutné jej nejprve dešifrovat. Použijte jednu z následujících metod:

- Použijte příkaz **Kopírovat/d** . Povoluje ukládání šifrovaných souborů v cílovém umístění jako dešifrovaných souborů.
- Nastavíte následující klíč registru:
  - Cesta = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ hodnoty = DWORD
  - Název = CopyFileAllowDecryptedRemoteDestination
  - Hodnota = 1

Mějte na paměti, že nastavení klíče registru ovlivňuje všechny operace kopírování provedené ve sdílených síťových složkách.

## <a name="slow-enumeration-of-files-and-folders"></a>Pomalé vyčíslení výčtu souborů a složek

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud na klientském počítači není dost paměti pro velké adresáře.

### <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, upravte hodnotu registru **DirectoryCacheEntrySizeMax** tak, aby povolovala ukládání většího počtu výpisů adresářů na klientském počítači:

- Umístění: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Hodnota Mane: DirectoryCacheEntrySizeMax 
- Typ hodnoty: DWORD
 
 
Můžete ho například nastavit na 0x100000 a zjistit, jestli se výkon zlepšil.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Chyba AadDsTenantNotFound při povolování ověřování služby Azure Active Directory Domain Services (Azure služba AD DS) pro soubory Azure – nejde najít aktivní klienty s ID tenanta AAD-tenant-ID.

### <a name="cause"></a>Příčina

K chybě AadDsTenantNotFound dojde při pokusu o [Povolení ověřování Azure Active Directory Domain Services (azure služba AD DS) na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) v účtu úložiště, kde [Služba Azure AD Domain Service (Azure služba AD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) není vytvořená v tenantovi Azure AD přidruženého předplatného.  

### <a name="solution"></a>Řešení

Povolte Azure služba AD DS v tenantovi Azure AD předplatného, na které je váš účet úložiště nasazený. K vytvoření spravované domény potřebujete oprávnění správce pro tenanta Azure AD. Pokud nejste správcem tenanta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů, které vám [umožní Azure Active Directory Domain Services používání Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-azure-ad-ds-authentication-enabled"></a>Došlo k chybě "Systémová chyba 1359. Došlo k vnitřní chybě přijaté přes přístup SMB ke sdíleným složkám s povoleným ověřováním služby Azure Active Directory Domain Service (Azure služba AD DS).

### <a name="cause"></a>Příčina

Došlo k chybě "Systémová chyba 1359. K interní chybě dojde, když se pokusíte připojit ke sdílené složce s povoleným ověřováním Azure služba AD DS v Azure služba AD DS s názvem DNS domény počínaje číselným znakem. Pokud třeba název DNS pro doménu služba AD DS Azure je "1domain", tato chyba se zobrazí při pokusu o připojení sdílené složky pomocí přihlašovacích údajů Azure AD. 

### <a name="solution"></a>Řešení

V současné době můžete zvážit opětovné nasazení služba AD DS Azure pomocí nového názvu DNS domény, který platí pro následující pravidla:
- Názvy nesmí začínat číselným znakem.
- Název musí mít délku 3 až 63 znaků.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nepovedlo se připojit soubory Azure s přihlašovacími údaji služby AD 

### <a name="self-diagnostics-steps"></a>Kroky pro samočinnou diagnostiku
Nejdřív se ujistěte, že jste provedli všechny čtyři kroky, abyste [mohli povolit ověřování Azure Files AD](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Za druhé zkuste připojit [sdílenou složku Azure s klíčem účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Pokud se nepovedlo připojit, Stáhněte si [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) , abyste vám pomohli ověřit spuštěné prostředí klienta, zjistit nekompatibilní konfiguraci klienta, která by způsobila selhání přístupu pro soubory Azure, nabízí doporučené pokyny k automatickým opravám a shromažďování diagnostických trasování.

Třetí, můžete spustit rutinu Debug-AzStorageAccountAuth, která provede sadu základních kontrol konfigurace služby AD s přihlášeným uživatelem služby AD. Tuto rutinu podporuje [AzFilesHybrid verze 0.1.2 nebo novější](https://github.com/Azure-Samples/azure-files-samples/releases). Tuto rutinu je potřeba spustit pod uživatelem AD, který má oprávnění vlastníka k cílovému účtu úložiště.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Tato rutina provádí následující kontroly v posloupnosti a poskytuje pokyny k selhání:
1. CheckPort445Connectivity: Ověřte, že je pro připojení SMB otevřený port 445.
2. CheckDomainJoined: Ověřte, že je klientský počítač připojený k doméně služby Active Directory.
3. CheckADObject: potvrďte, že ve službě Active Directory existuje objekt, který představuje účet úložiště, a má správný název SPN (hlavní název služby).
4. CheckGetKerberosTicket: Pokuste se získat lístek protokolu Kerberos pro připojení k účtu úložiště. 
5. CheckADObjectPasswordIsCorrect: Ujistěte se, že heslo nakonfigurované na identitě AD, které představuje účet úložiště, odpovídá účtu úložiště kerb1 nebo kerb2 Key
6. CheckSidHasAadUser: Ověřte, že se přihlášený uživatel služby AD synchronizuje do Azure AD. Pokud chcete vyhledat konkrétního uživatele služby AD, který je synchronizovaný s Azure AD, můžete ve vstupních parametrech zadat-UserName a-Domain.
7. CheckAadUserHasSid: Ověřte, jestli má uživatel Azure AD ve službě AD identifikátor SID, tato akce vyžaduje, aby uživatel měl vstupní ID objektu uživatele Azure AD s parametrem-ObjectId. 
8. CheckStorageAccountDomainJoined: Zkontrolujte vlastnosti účtu úložiště, aby bylo možné zjistit, zda bylo povoleno ověřování AD a naplněny vlastnosti služby Active Directory účtu.

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Nejde nakonfigurovat oprávnění na úrovni adresáře nebo souboru (seznamy řízení přístupu systému Windows) pomocí Průzkumníka souborů Windows.

### <a name="symptom"></a>Příznak

Při pokusu o konfiguraci seznamů řízení přístupu k Windows pomocí Průzkumníka souborů v připojené sdílené složce se můžou vyskytnout příznaky popsané níže:
- Po kliknutí na oprávnění upravit na kartě zabezpečení se Průvodce oprávněními nenačte. 
- Když se pokusíte vybrat nového uživatele nebo skupinu, v umístění domény se nezobrazí správná služba AD DS doména. 

### <a name="solution"></a>Řešení

Doporučujeme použít [nástroj Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ke konfiguraci oprávnění na úrovni adresáře nebo souboru jako alternativní řešení. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Chyby při spuštění rutiny JOIN-AzStorageAccountForAuth

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Chyba: adresářové službě se nepodařilo přidělit relativní identifikátor.

K této chybě může dojít, pokud řadič domény, který obsahuje roli FSMO hlavního serveru RID, není k dispozici nebo byl odebrán z domény a obnoven ze zálohy.  Potvrďte, že jsou všechny řadiče domény spuštěné a dostupné.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Chyba: Nejde vytvořit vazbu pozičních parametrů, protože se nezadaly žádné názvy

Nejpravděpodobnější příčinou této chyby je chyba syntaxe v příkazu Join-AzStorageAccountforAuth.  V příkazu zkontrolujte chyby v pravopisu nebo chyby syntaxe a ověřte, jestli je nainstalovaná nejnovější verze modulu AzFilesHybrid ( https://github.com/Azure-Samples/azure-files-samples/releases) .  

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.
