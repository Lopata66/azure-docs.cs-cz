---
title: Konzola sériového portu virtuálního počítače Azure | Dokumentace Microsoftu
description: Obousměrné sériové konzoly pro virtuální počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: harijay
ms.openlocfilehash: facd9be037894932e516e8294e36b6b0e55374c8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024407"
---
# <a name="virtual-machine-serial-console"></a>Konzola sériového portu virtuálního počítače


Konzole sériového portu virtuálního počítače v Azure poskytuje přístup ke konzole založený na textu pro virtuální počítače s Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače, poskytování přístupu k virtuálnímu počítači, který je nezávislý na síti nebo stav operačního systému virtuálního počítače. Přístup ke konzole sériového portu pro virtuální počítač momentálně můžete pouze to udělat pomocí webu Azure portal a je povolená jenom pro uživatele, kteří mají Přispěvatel virtuálních počítačů nebo vyšší než přístup k virtuálnímu počítači. 

Dokumentaci konzoly sériového portu pro virtuální počítače s Linuxem [kliknutím sem](serial-console-linux.md).

> [!NOTE] 
> Konzola sériového portu u virtuálních počítačů je obecně dostupná v globálními oblastmi Azure. V tomto okamžiku konzoly sériového portu ještě není k dispozici v cloudu Azure Government nebo Azure China.

 

## <a name="prerequisites"></a>Požadavky 

* Musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány. 
* Virtuální počítač musí mít [Diagnostika spouštění](boot-diagnostics.md) povoleno 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště. 
* Virtuální počítač, pro kterou jsou přístup ke konzole sériového portu musí mít také účet založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů – snímek najdete níže.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Začínáme s konzoly sériového portu
Konzola sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [webu Azure portal](https://portal.azure.com). Níže je uvedený postup pro přístup ke konzole sériového portu pro virtuální počítače prostřednictvím portálu.

  1. Otevřete na webu Azure portal
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka s přehledem pro virtuální počítač.
  4. Posuňte se dolů části Podpora a řešení potíží s části a klikněte na možnost "Konzoly sériového portu". Otevře se nové podokno s sériové konzoly a spustit připojení.

## <a name="enable-serial-console-in-custom-or-older-images"></a>Povolit konzoly sériového portu v obrázcích vlastní nebo starší
Novější Image Windows serveru v Azure budou mít [Speciální konzoly pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ve výchozím nastavení povolená. SAC je podporováno ve verzích Windows na serveru, ale není k dispozici ve verzích klienta (například Windows 10, Windows 8 nebo Windows 7). Pokud chcete povolit konzoly sériového portu pro virtuální počítače s Windows, které jsou vytvořeny před únorem 2018, použijte následující kroky: 

1. Připojte se k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Restartujte systém pro konzolu SAC povolení

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

V případě potřeby SAC se dá nastavit v offline režimu také:

1. Připojte disk systému windows chcete SAC nakonfigurovaný jako datového disku k existujícímu virtuálnímu počítači. 
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak poznám, jestli je povolené SAC?

Pokud [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) není povoleno konzole sériového portu se nezobrazí SAC řádku. V některých případech se zobrazí informace o stavu virtuálního počítače a v ostatních případech bude prázdné. Pokud používáte image Windows serveru vytvořeny před únorem 2018, SAC nebude pravděpodobně možné.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Povolit spouštěcí nabídky Windows v konzole sériového portu 

Pokud je potřeba povolit Windows spouštěcí zavaděč zobrazí výzvu k zobrazení v konzole sériového portu, přidáte následující další možnosti pro konfigurační data spouštění. Zobrazit [nástroje bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) další podrobnosti

1. Připojte se k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Restartování systému pro spouštěcí nabídky, aby byl povolen

> [!NOTE] 
> Časový limit, který jste nastavili pro spouštěcí nabídky Správce zobrazení bude mít vliv na váš operační systém spouštění čas v budoucnosti. Může být přijatelný pro některá přidání 10 druhý časový limit, který zajišťuje správce spouštění viditelné prostřednictvím konzoly sériového portu, ostatní může být vhodné kratší nebo delší časový limit. Nastavte hodnotu časového limitu na hodnotu, která jste obeznámeni.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Použití konzoly sériového portu pro volání NMI ve virtuálních počítačích Windows
Maskable přerušení (NMI) slouží k vytvoření signál, který nebude ignorovat softwaru na virtuálním počítači. V minulosti NMIs se používají ke sledování hardwarových problémů v systémech, které vyžaduje konkrétní odezvy.  Dnes, programátoři a správce systému často používají NMI jako mechanismus pro ladění nebo řešení potíží s systémy, které jsou zablokovaná.

Konzole sériového portu je možné odeslat NMI na virtuálním počítači Azure pomocí klávesnice ikony na příkazovém řádku je uvedeno níže. Jakmile se doručí NMI, konfigurace virtuálního počítače bude řídit, jak systém reaguje. Windows by šlo o chybách a vytvořit výpis paměti při příjmu NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Informace o konfiguraci Windows vytvořit výpis stavu systému, když dostane NMI najdete v tématu: [jak vygenerovat soubor s výpisem paměti kompletní selhání nebo soubor s výpisem paměti jádra s použitím NMI v systému Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="open-cmd-or-powershell-in-serial-console"></a>Otevření příkazového řádku nebo Powershellu v konzole sériového portu

1. Připojte se ke konzole sériového portu. Pokud se úspěšně připojíte ke konzole sériového portu, zobrazí se **SAC >** jako na následujícím snímku obrazovky se zobrazí:

    ![Připojte se k SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

3.  Typ `cmd` k vytvoření kanálu, který má CMD instance. 
4.  Typ `ch -si 1` přejděte do kanálu, na kterém běží instance příkazového řádku. 
5.  Stiskněte klávesu Enter a pak zadejte své přihlašovací údaje, které mají oprávnění pro správu.
6.  Po zadání platné přihlašovací údaje se otevře CMD instance.
7.  Chcete-li spustit instance prostředí PowerShell, zadejte `PowerShell` instance v CMD a stiskněte klávesu Enter. 

    ![Otevřené instance prostředí PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)


## <a name="disable-serial-console"></a>Zakázat konzoly sériového portu
Všechna předplatná mají ve výchozím přístupem ke konzole sériového portu pro všechny virtuální počítače. Konzola sériového portu na úrovni předplatného nebo na úrovni virtuálního počítače můžete kdykoli deaktivovat.

> [!NOTE]       
> Pokud chcete povolit nebo zakázat konzoly sériového portu k předplatnému, musíte mít oprávnění k zápisu do předplatného. To zahrnuje, ale není omezena pouze na role správce nebo vlastníka. Vlastní role může mít také oprávnění k zápisu.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzola sériového portu se dají zakázat pro celé předplatné podle prostřednictvím [volání rozhraní API REST zakázat konzoly](https://aka.ms/disableserialconsoleapi). "Vyzkoušet" funkce k dispozici na stránce dokumentace k rozhraní API můžete samozřejmě využít zakázání a povolení konzoly sériového portu pro odběr. Zadejte vaše `subscriptionId`, "Výchozí" v `default` pole a klikněte na tlačítko spustit. Příkazy Azure CLI ještě nejsou k dispozici a budou doručeny později. [Zkuste volání rozhraní REST API zde](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternativně můžete použít sadu příkazů níže ve službě Cloud Shell (uvedené příkazy bash) zakázání, povolení a zobrazení zakázané konzoly sériového portu pro odběr. 

* Pokud chcete získat zakázané konzoly sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Chcete-li zakázat konzoly sériového portu pro předplatné:
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pokud chcete povolit konzoly sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Zakázat úrovni virtuálního počítače
Konzoly sériového portu je možné zakázat konkrétní virtuální počítače tím, že zakážete nastavení diagnostiky spouštění Virtuálního počítače. Stačí vypnout nastavení diagnostiky spouštění na webu Azure Portal a konzoly sériového portu se deaktivuje pro virtuální počítač.

## <a name="serial-console-security"></a>Sériové konzoly zabezpečení 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají [VM přispěvatelé](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší než přístup k virtuálnímu počítači. Pokud váš tenant AAD vyžaduje Vícefaktorové ověřování, přístup ke konzole sériového portu bude také nutné MFA, jeho přístup je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášen [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.  

>[!CAUTION] 
Při přihlášení bez hesla přístup pro konzolu, pokud obsahují příkazy se spouští v rámci konzoly nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formou z identifikovatelné osobní údaje (PII), ty se zapíšou do Diagnostika spouštění virtuálních počítačů protokoly, spolu s všechny ostatní viditelného textu, jako součást provádění konzoly sériového portu přejděte zpět funkce. Tyto protokoly jsou cyklické a pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky k nim měli přístup, ale doporučujeme osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajné kódy a/nebo identifikovatelné osobní údaje. 

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojený k sériové konzoly a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil způsobem podobají první uživatel, sestavení a opuštění fyzické konzole a nový uživatel sedí.

>[!CAUTION] 
To znamená, že uživatel, který odpojí nebude odhlášeni! Schopnost Vynutit odhlášení při odpojení (prostřednictvím SIGHUP nebo mechanismus podobný) je stále v se plánuje. Pro Windows je automatické vypršení časového limitu povolené v SAC, ale pro Linux můžete nakonfigurovat nastavení terminálu vypršení časového limitu. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu 
Scénář          | Akce v konzole sériového portu                
:------------------|:-----------------------------------------
Pravidla brány firewall na nesprávný | Přístup k sériové konzoly a opravte Windows pravidla brány firewall. 
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a obnovení systému souborů. 
Problémy s konfigurací protokolu RDP | Přístup ke konzole sériového portu a změňte nastavení. Přejděte [RDP dokumentaci](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) začít.
Uzamknutí sítě v systému| Konzoly sériového portu přístup prostřednictvím portálu pro správu systému. Některé příkazy sítě jsou uvedeny v [sériové konzoly příkazového řádku a Powershellu dokumentaci](serial-console-cmd-ps-commands.md). 
Interakce s zaváděcího programu pro spouštění | Přístup BCD prostřednictvím konzoly sériového portu. Přejděte na [povolení spouštěcí nabídky pro zobrazení v konzole sériového portu](#enabling-boot-menu-to-show-in-the-serial-console) začít. 

## <a name="accessibility"></a>Přístupnost
Klíče se pro Azure konzoly sériového portu se usnadnění přístupu. Za tímto účelem jsme zajistíte, že je přístupný pro ty, které mají vizuál a poškozením sluchu, jakož i uživatelů, kteří nebudou moct používat myš konzole sériového portu.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití `tab` kláves na klávesnici pro navigaci v konzole sériového portu rozhraní na webu Azure portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu `Ctrl + F6` na klávesnici.

### <a name="use-serial-console-with-a-screen-reader"></a>Použití konzoly sériového portu se čtečkou obrazovky
Konzola sériového portu se dodává s integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="errors"></a>Chyby
Většina chyb jsou přechodné povahy a opakováním tyto adresy připojení. Následující tabulka uvádí seznam chyb a způsoby zmírnění rizik

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro "<VMNAME>". Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu
Nemáte požadovaná oprávnění k použití této konzoly sériového portu virtuálního počítače. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění '<STORAGEACCOUNTNAME>". Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. Zobrazit [požadavky na přístup](#prerequisites) podrobnosti
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Zajistěte, aby že tuto diagnostiku spouštění nemá žádné brány firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzoly sériového portu funkce.
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), které mění poměrně.
Pouze informace o stavu se zobrazuje při připojení k virtuálnímu počítači s Windows| Tím se zobrazí-li Speciální konzoly pro správu nebyl povolen pro vaši image Windows. Zobrazit [přístup sériové konzoly pro Windows](#access-serial-console-for-windows) pokyny o tom, jak ručně povolit SAC na vašem virtuálním počítači Windows. Další podrobnosti najdete v [signálů stavu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Známé problémy 
Víme o některé problémy s konzole sériového portu. Tady je seznam těchto problémů a kroky pro omezení rizik.

Problém                             |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Dosažení zadejte po banner připojení není uveden do protokolu v řádku | najdete na této stránce: [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomu může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo spouštěcí konfigurace této causers Windows nepodaří správně připojit do sériového portu. Také k tomu dochází Pokud používáte klienta Windows 10 virtuálních počítačů, jako jsou nakonfigurovaní jenom virtuální počítače Windows serveru povoleno EMS.
Nelze zadat v SAC řádku, pokud je povoleno ladění jádra | Připojení RDP k virtuálnímu počítači a spusťte `bcdedit /debug {current} off` z příkazového řádku se zvýšenými oprávněními. Pokud nelze pomocí protokolu RDP můžete místo toho připojit disk s operačním systémem k jinému virtuálnímu počítači Azure a upravit ho během připojený jako datový disk pomocí `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, pak Prohodit zpět na disku.
Vložení do prostředí PowerShell ve výsledcích SAC ve třetí znaku, pokud původní obsah měli opakující se znak | Alternativní řešení se po uvolnění modulu PSReadLine z aktuální relace. Spustit `Remove-Module PSReadLine` uvolnění modulu PSReadLine z aktuální relace – tím neodstraní ani odinstalace modulu.
Některé klávesnice vstupy generovat výstup strangeová SAC (třeba `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) řídicí sekvence nejsou podporovány SAC řádku.
Vkládání velmi dlouhých řetězců nefunguje | Konzola sériového portu omezení délky řetězce do terminálu na 2 048 znaků. Toto je zabránit zahlcení šířky pásma sériového portu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu jako problém tak, že přejdete do https://aka.ms/serialconsolefeedback. Můžete také menší (upřednostňované) pošlete zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com

**Q. Podporuje konzoly sériového portu, kopírování a vkládání?**

A. Ano, co dělá. Zkopírujte a vložte do terminálu pomocí kombinace kláves Ctrl + Shift + C a Ctrl + Shift + V.

**Q. Kdo může povolit nebo zakázat konzoly sériového portu pro Moje předplatné?**

A. Pokud chcete povolit nebo zakázat konzoly sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu patří, ale nejsou omezeny rolí správce nebo vlastníka. Vlastní role může mít také oprávnění k zápisu.

**Q. Kdo má přístup k konzoly sériového portu pro virtuální počítač?**

A. Musíte mít přístup úrovně Přispěvatel nebo vyšší do virtuálního počítače pro přístup ke konzole sériového portu Virtuálního počítače. 

**Q. Moje konzoly sériového portu se nezobrazuje nic, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Zobrazit [povolit konzoly sériového portu v obrázcích vlastní nebo starší](#enable-serial-console-in-custom-or-older-images) podrobné informace o konfiguraci bitové kopie umožňují konzoly sériového portu.

**Q. Je k dispozici konzoly sériového portu pro Virtual Machine Scale Sets?**

A. V současné době se nepodporuje přístup ke konzole sériového portu pro instance škálovací sady virtuálních počítačů.

## <a name="next-steps"></a>Další postup
* Podrobné pokyny k příkazy CMD a prostředí PowerShell můžete použít v Windows SAC, klikněte na tlačítko [tady](serial-console-cmd-ps-commands.md).
* Je taky dostupná ke konzole sériového portu [Linux](serial-console-linux.md) virtuálních počítačů.
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).