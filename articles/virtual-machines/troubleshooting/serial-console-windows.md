---
title: Azure konzoly sériového portu pro Windows | Dokumentace Microsoftu
description: Obousměrné sériové konzoly pro virtuální počítače Azure a Škálovací sady virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: harijay
ms.openlocfilehash: c6611c75e61f7e381efd2e437b8281cc70601215
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141058"
---
# <a name="azure-serial-console-for-windows"></a>Azure konzoly sériového portu pro Windows

Konzole sériového portu na webu Azure Portal poskytuje přístup ke konzole založený na textu pro Windows virtual machines (VM) a škálovací sady virtuálních počítačů instance (škálovací sady virtuálních počítačů). Toto sériové připojení připojí k COM1 sériového portu virtuálního počítače nebo instanci virtuálního počítače škálovací sady, díky přístupu k němu nezávisle na stav sítě nebo operačního systému. Konzole sériového portu je přístupný pouze pomocí webu Azure portal a je povolený jenom pro uživatele, kteří mají roli přístup Přispěvatel nebo vyšší do škálovací sady virtuálního počítače nebo virtuálního počítače.

Konzola sériového portu funguje stejným způsobem pro virtuální počítače a škálovací sady virtuálních počítačů instancí. V tomto dokumentu bude všech zmínky k virtuálním počítačům implicitně obsahovat instancí škálovací sady virtuálních počítačů, pokud není uvedeno jinak.

Konzola sériového portu dokumentaci pro Linuxové virtuální počítače a škálovací sady virtuálních počítačů najdete v tématu [konzoly sériového portu Azure pro Linux](serial-console-linux.md).

> [!NOTE]
> Konzole sériového portu je obecně dostupná v globálními oblastmi Azure. Zatím není k dispozici v Azure government nebo Azure China cloudy.


## <a name="prerequisites"></a>Požadavky

* Váš virtuální počítač nebo virtuální počítač instanci škálovací sady používaly model nasazení správy prostředků. Klasická nasazení nejsou podporovány.

- Musí mít váš účet, který používá konzoly sériového portu [role Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a [Diagnostika spouštění](boot-diagnostics.md) účtu úložiště

- Váš virtuální počítač nebo virtuální počítač instance škálovací sadě musí mít uživatel založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů. Vyberte **resetovat heslo** z **podpora a řešení potíží** oddílu.

* Virtuální počítač, ve kterém přistupujete konzoly sériového portu musí mít [Diagnostika spouštění](boot-diagnostics.md) povolena.

    ![Nastavení diagnostiky spouštění](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Začínáme s konzole sériového portu
Konzole sériového portu pro virtuální počítače a škálovací sady virtuálních počítačů je přístupný pouze prostřednictvím webu Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Konzola sériového portu pro virtuální počítače
Konzola sériového portu pro virtuální počítače je stejně jednoduché jako při kliknutí na **konzoly sériového portu** v rámci **podpora a řešení potíží** části webu Azure Portal.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte do **všechny prostředky** a vyberte virtuální počítač. Otevře se stránka s přehledem pro virtuální počítač.

  1. Přejděte dolů k položce **podpora a řešení potíží** a vyberte **konzoly sériového portu**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konzola sériového portu pro Škálovací sady virtuálních počítačů
Konzola sériového portu je k dispozici na základě jednotlivé instance pro škálovací sady virtuálních počítačů. Budete muset přejít na jednotlivé instance škálovací sady virtuálních počítačů, než **konzoly sériového portu** tlačítko. Pokud vaše škálovací sada virtuálních počítačů nemá povolenou diagnostikou spuštění, zajistěte, aby že aktualizace vašeho modelu škálovací sady virtuálního počítače povolte diagnostiku spouštění, a pak upgradovat všechny instance na nový model pro přístup ke konzole sériového portu.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte do **všechny prostředky** a vyberte Škálovací sady virtuálních počítačů. Stránka s přehledem pro škálování virtuálního počítače nastavte otevře.

  1. Přejděte na **instancí**

  1. Vyberte instanci virtuálního počítače škálovací sady

  1. Z **podpora a řešení potíží** vyberte **konzoly sériového portu**. Nové podokno v konzole sériového portu se otevře a aktivuje připojení.

## <a name="enable-serial-console-functionality"></a>Povolení funkce konzoly sériového portu

> [!NOTE]
> Pokud se nezobrazují v konzole sériového portu nic, ujistěte se, že Diagnostika spouštění je zapnutá ve škálovací sadě virtuálního počítače nebo virtuálního počítače.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Povolit konzole sériového portu v obrázcích vlastní nebo starší
Novější Image Windows serveru v Azure mají [Speciální konzoly pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ve výchozím nastavení povolená. SAC je podporováno ve verzích Windows na serveru ale není k dispozici ve verzích klienta (například Windows 10, Windows 8 nebo Windows 7).

Pro starší Image Windows serveru (vytvořeny před únorem 2018) můžete automaticky povolí konzole sériového portu pomocí webu Azure portal spusťte příkaz funkce. Na webu Azure Portal, vyberte **spusťte příkaz**, vyberte příkaz s názvem **EnableEMS** ze seznamu.

![Spuštění seznamu příkazů](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Můžete také povolit ručně konzole sériového portu pro Windows virtuální počítače nebo virtuální počítače škálovací sady vytvořené před únorem 2018, postupujte takto:

1. Připojení k virtuálnímu počítači Windows pomocí vzdálené plochy
1. Z příkazového řádku pro správu, spusťte následující příkazy:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Restartujte systém pro konzolu SAC povolit.

    ![SAC konzoly](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

V případě potřeby SAC se dá nastavit v offline režimu také:

1. Připojte disk systému windows, pro které chcete SAC nakonfigurovaný jako datového disku k existujícímu virtuálnímu počítači.

1. Z příkazového řádku pro správu, spusťte následující příkazy:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak poznám, jestli je povolené SAC?

Pokud [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) není povolený, nezobrazí konzole sériového portu SAC řádku. V některých případech se zobrazí informace o stavu virtuálních počítačů a v ostatních případech je prázdný. Pokud používáte image Windows serveru vytvořeny před únorem 2018, SAC pravděpodobně nebude povolena.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Povolit spouštěcí nabídky Windows v konzole sériového portu

Pokud je potřeba povolit Windows spouštěcí zavaděč výzvy k zobrazení v konzole sériového portu, můžete přidat následující další možnosti do konfiguračních dat spouštění. Další informace najdete v tématu [nástroje bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Připojte se k virtuálnímu počítači s Windows nebo instance virtuálního počítače škálovací sady pomocí vzdálené plochy.

1. Z příkazového řádku pro správu, spusťte následující příkazy:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Restartování systému pro spouštěcí nabídky, aby byl povolen

> [!NOTE]
> Časový limit, který jste nastavili pro spouštěcí nabídky Správce pro zobrazení bude mít vliv na váš čas spuštění operačního systému. Pokud se domníváte, že hodnota 10 sekund časového limitu je příliš krátký nebo příliš dlouhý, nastavte ji na jinou hodnotu.

## <a name="use-serial-console"></a>Použití konzoly sériového portu

### <a name="use-cmd-or-powershell-in-serial-console"></a>Pomocí příkazového řádku nebo Powershellu v konzole sériového portu

1. Připojte se ke konzole sériového portu. Pokud se úspěšně připojíte, je na řádku **SAC >**:

    ![Připojte se k SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Zadejte `cmd` k vytvoření kanálu, který má CMD instance.

1.  Zadejte `ch -si 1` přejděte do kanálu, na kterém běží instance příkazového řádku.

1.  Stisknutím klávesy **Enter**a pak zadejte přihlašovací údaje s oprávněními správce.

1.  Po zadání platné přihlašovací údaje, se otevře CMD instance.

1.  Chcete-li spustit instance prostředí PowerShell, zadejte `PowerShell` CMD instance a stiskněte klávesu **Enter**.

    ![Otevřené instance prostředí PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Použít pro volání NMI konzole sériového portu
Maskable přerušení (NMI) slouží k vytvoření signál, který nebude ignorovat softwaru na virtuálním počítači. V minulosti NMIs se používají ke sledování hardwarových problémů v systémech, které vyžaduje konkrétní odezvy. Dnes, programátoři a správce systému často používají NMI jako mechanismus pro ladění nebo řešení potíží s systémy, které nereagují.

Konzole sériového portu je možné odeslat NMI na virtuálním počítači Azure s využitím klávesnice ikonu na panelu příkazů. Jakmile se doručí NMI, konfigurace virtuálního počítače bude řídit, jak systém reaguje při. Windows by šlo o chybách a vytvořte soubor s výpisem paměti při příjmu NMI.

![Poslat nemaskovatelné přerušení](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Informace o konfiguraci Windows vytvořte soubor s výpisem paměti při selhání při přijetí NMI najdete v tématu [jak vygenerovat soubor s výpisem paměti při selhání s použitím NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Použít funkční klávesy v konzole sériového portu
Funkční klávesy jsou povolené pro využití konzoly sériového portu ve virtuálních počítačích Windows. F8 v rozevírací nabídce konzoly sériového portu zvyšuje pohodlí snadné zadávání nabídky Rozšířené nastavení spouštění, ale je kompatibilní se všemi klíči jiné funkce konzoly sériového portu. Budete muset stiskněte **Fn** + **F1** (nebo F2 a F3, atd.) na klávesnici, v závislosti na počítači konzoly sériového portu z používáte.

### <a name="use-wsl-in-serial-console"></a>Použití WSL v konzole sériového portu
Subsystém Windows pro Linux (WSL) bylo povoleno pro Windows Server 2019 nebo novější, takže je také možné povolit WSL pro použití v rámci konzoly sériového portu, pokud používáte systém Windows Server 2019 nebo novější. To může být výhodné pro uživatele, kteří mají také znalost Linuxové příkazy. Pokyny k povolení WSL pro systém Windows Server najdete v tématu [Průvodce instalací](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Restartování vaší Windows virtuálního počítače nebo instanci počítače škálovací sady v rámci konzoly sériového portu
Restartování v rámci konzoly sériového portu můžete zahájit tak, že přejdete na tlačítku napájení a kliknutím na "Restartování virtuálního počítače". Tím zahájíte restartování virtuálního počítače a zobrazí se oznámení na webu Azure portal o restartování.

To je užitečné v situacích, kde můžete chtít přístup k spouštěcí nabídky, aniž byste museli opustit prostředí konzoly sériového portu.

![Restartování konzoly sériového portu Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Zakázat konzoly sériového portu
Všechna předplatná mají ve výchozím přístupem ke konzole sériového portu pro všechny virtuální počítače. Můžete zakázat konzole sériového portu na úrovni předplatného nebo na úrovni virtuálního počítače.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Zakázat úrovni sady škálování virtuálního počítače nebo virtuální počítač
Konzole sériového portu je možné zakázat pro konkrétní virtuální počítač nebo virtuální počítač škálovací sady s tím, že zakážete nastavení diagnostiky spouštění. Vypnutí diagnostiky spouštění na webu Azure Portal se zakázat konzole sériového portu pro virtuální počítač nebo škálovací sady virtuálních počítačů. Pokud používáte konzoly sériového portu na škálovací sadu virtuálních počítačů, zajistěte, aby že vaše instance škálovací sady virtuálních počítačů upgradujete na nejnovější model.

> [!NOTE]
> K povolení nebo zakázání konzole sériového portu k předplatnému, musíte mít oprávnění k zápisu do předplatného. Tato oprávnění patří, ale nejsou omezeny rolí správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

### <a name="subscription-level-disable"></a>Zakázat úroveň předplatného
Konzole sériového portu se dají zakázat pro celé předplatné prostřednictvím [volání rozhraní API REST zakázat konzoly](/rest/api/serialconsole/console/disableconsole). Můžete použít **vyzkoušet** funkce, které jsou k dispozici na této stránce dokumentace k rozhraní API zakázání a povolení konzole sériového portu pro odběr. Zadejte ID svého předplatného pro **subscriptionId**, zadejte "Výchozí" **výchozí**a pak vyberte **spustit**. Příkazy Azure CLI ještě nejsou k dispozici.

![Vyzkoušet rozhraní REST API](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternativně můžete použít následující sady příkazů prostředí bash ve službě Cloud Shell k zakázání, povolení a zobrazení zakázané konzole sériového portu pro předplatné:

* Pokud chcete získat zakázané konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Chcete-li zakázat konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pokud chcete povolit konzole sériového portu pro předplatné:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají roli přístup z [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší, k virtuálnímu počítači. Pokud váš tenant Azure Active Directory vyžaduje vícefaktorové ověřování (MFA), je přístup ke konzole sériového portu bude také nutné vícefaktorové ověřování, protože přístup ke konzole sériového portu, je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášen [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.

> [!CAUTION]
> Žádná hesla přístup pro konzolu jsou protokolovány. Nicméně pokud příkazy se spouští v rámci konzoly obsahovat nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formu identifikovatelné osobní údaje (PII), ty se zapíšou do protokolů diagnostiky spouštění virtuálního počítače. Se zapíšou spolu s všechny ostatní viditelného textu, jako součást provádění konzoly sériového portu přejděte zpět funkce. Tyto protokoly jsou cyklické a přístup k nim mají pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky. Však doporučujeme osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajné kódy a/nebo identifikovatelné osobní údaje.

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil do stejné relace.

> [!CAUTION]
> To znamená, že uživatel, který je odpojen nebude odhlášeni. Schopnost Vynutit odhlášení při odpojení (pomocí SIGHUP nebo mechanismus podobný) je stále v se plánuje. Pro Windows je automatické vypršení časového limitu povolené v SAC; pro Linux můžete nakonfigurovat nastavení terminálu vypršení časového limitu.

## <a name="accessibility"></a>Přístupnost
Klíče se pro Azure konzoly sériového portu se usnadnění přístupu. Za tímto účelem jsme zajistili, že je přístupný pro vizuál a vaše zasažená, jakož i uživatelů, kteří nebudou moct používat myš konzole sériového portu.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití **kartu** kláves na klávesnici a přejděte v konzole sériového portu rozhraní na webu Azure Portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu **Ctrl**+**F6** na klávesnici.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Použití konzole sériového portu se čtečkou obrazovky
Konzole sériového portu je integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Pravidla brány firewall na nesprávný | Přístup k sériové konzoly a opravte Windows pravidla brány firewall.
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a proveďte obnovení ze systému souborů.
Problémy s konfigurací protokolu RDP | Přístup ke konzole sériového portu a změnit nastavení. Další informace najdete v tématu [RDP dokumentaci](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Uzamknutí sítě v systému | Na webu Azure Portal ke správě systému přístup ke konzole sériového portu. Některé příkazy sítě jsou uvedeny v [příkazy Windows: Příkaz CMD a prostředí PowerShell](serial-console-cmd-ps-commands.md).
Interakce s zaváděcího programu pro spouštění | Přístup k BCD prostřednictvím konzoly sériového portu. Informace najdete v tématu [povolit Windows spouštěcí nabídky v konzole sériového portu](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Chyby
Protože většina chyb jsou přechodné, opakování pokusu o připojení je často opravit. Následující tabulka uvádí seznam chyb a způsoby zmírnění rizik pro oba virtuální počítače a škálovací sady virtuálních počítačů instancí.

Chyba                            |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Nepovedlo se načíst nastavení diagnostiky spouštění pro  *&lt;VMNAME&gt;*. Použití konzole sériového portu, zajistěte, že Diagnostika spouštění je povolená pro tento virtuální počítač. | Ujistěte se, že má virtuální počítač [Diagnostika spouštění](boot-diagnostics.md) povolena.
Virtuální počítač je v zastaveném stavu Uvolněno. Spusťte virtuální počítač a pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve spuštěném stavu pro přístup ke konzole sériového portu
Nemáte požadovaná oprávnění k použití této konzoly sériového portu virtuálního počítače. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění  *&lt;STORAGEACCOUNTNAME&gt;*. Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění. Další informace najdete v tématu [požadavky](#prerequisites).
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Zajistěte, aby že tuto diagnostiku spouštění nemá žádné brány firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzole sériového portu na funkci.
Webové sokety je uzavřený nebo nelze otevřít. | Možná budete muset povolit `*.console.azure.com`. Podrobnější ale delší přístup je na seznamu povolených IP adres [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), které mění poměrně.
Pouze informace o stavu se zobrazuje při připojení k virtuálnímu počítači s Windows| K této chybě dochází, pokud Speciální konzoly pro správu nebyl povolen pro vaši image Windows. Zobrazit [povolit konzole sériového portu v obrázcích vlastní nebo starší](#enable-the-serial-console-in-custom-or-older-images) pokyny o tom, jak ručně povolit SAC na vašem virtuálním počítači Windows. Další informace najdete v tématu [signálů stavu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Známé problémy
Jsme si vědomi některé problémy s konzole sériového portu. Tady je seznam těchto problémů a kroky pro omezení rizik. Tyto problémy a jejich zmírnění platí pro oba virtuální počítače a škálovací sady virtuálních počítačů instancí.

Problém                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutím klávesy **Enter** po připojení banner nezpůsobí výzvě přihlášení má být zobrazen. | Další informace najdete v tématu [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K této chybě může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo spouštěcí konfigurace, který způsobí, že Windows nepodaří správně připojit do sériového portu. Tato chyba se vrátí taky používáte klienta Windows 10 virtuálních počítačů, protože jsou nakonfigurovaní jenom virtuální počítače Windows serveru povoleno EMS.
Nelze zadat v SAC řádku, pokud je povoleno ladění jádra. | Připojení RDP k virtuálnímu počítači a spusťte `bcdedit /debug {current} off` z příkazového řádku se zvýšenými oprávněními. Pokud to není možné protokol RDP, můžete místo toho připojit disk s operačním systémem k jinému virtuálnímu počítači Azure a upravit ho během připojený jako datový disk spuštěním `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, pak Výměna disku zpět.
Pokud původní obsah měli opakující se znak, vkládání do prostředí PowerShell ve výsledcích SAC ve třetí znaku. | Alternativní řešení spustit `Remove-Module PSReadLine` uvolnění modulu PSReadLine z aktuální relace. Tato akce neodstraní ani odinstalace modulu.
Některé klávesnice vstupy generovat výstup strangeová SAC (například **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) řídicí sekvence nejsou podporovány SAC řádku.
Vkládání dlouhé řetězce nebude fungovat. | Konzole sériového portu omezení délky řetězce do terminálu na 2 048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.
Konzola sériového portu nefunguje s bránou firewall účtu úložiště. | Konzoly sériového portu záměrné nemůže pracovat s branami firewall účet úložiště na účet úložiště diagnostiky spouštění povolené.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Q. Jak můžu poslat svůj názor?**

A. Poskytnout zpětnou vazbu tak, že vytvoříte problém Githubu v https://aka.ms/serialconsolefeedback. Můžete také (méně upřednostňované), můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuální počítač https://feedback.azure.com.

**Q. Podporuje konzole sériového portu, kopírování a vkládání?**

A. Ano. Použití **Ctrl**+**Shift**+**C** a **Ctrl**+**Shift** + **V** zkopírovat a vložit do terminálu.

**Q. Kdo může povolit nebo zakázat konzole sériového portu pro Moje předplatné?**

A. K povolení nebo zakázání konzole sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

**Q. Kdo má přístup k konzole sériového portu pro virtuální počítač?**

A. Musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač pro přístup ke konzole sériového portu Virtuálního počítače.

**Q. Moje konzoly sériového portu se nezobrazuje nic, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Informace o konfiguraci bitové kopie umožňují konzole sériového portu najdete v tématu [povolit konzole sériového portu v obrázcích vlastní nebo starší](#enable-the-serial-console-in-custom-or-older-images).

**Q. Konzole sériového portu je dostupný pro škálovací sady virtuálních počítačů?**

A. V současné době se nepodporuje přístup ke konzole sériového portu pro instance škálovací sady virtuálních počítačů.

## <a name="next-steps"></a>Další postup
* Podrobné pokyny k příkazy CMD a prostředí PowerShell můžete použít v Windows SAC, naleznete v tématu [příkazy Windows: Příkaz CMD a prostředí PowerShell](serial-console-cmd-ps-commands.md).
* Je taky dostupná ke konzole sériového portu [Linux](serial-console-linux.md) virtuálních počítačů.
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).
