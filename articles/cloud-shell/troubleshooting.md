---
title: Řešení potíží Azure Cloud Shell | Dokumentace Microsoftu
description: Řešení potíží s Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: eb7deacc068661ca9a4f473ee2d36b7d4464c81c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199439"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží s & omezení Azure Cloud Shell

Známá řešení pro řešení potíží ve službě Azure Cloud Shell patří:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="early-timeouts-in-firefox"></a>Časná vypršení časových limitů ve Firefoxu

- **Podrobnosti o**: Cloud Shell využívá otevřít pomocí protokolu websocket předat vstup/výstup do prohlížeče. FireFox má předvolby zásad, které můžete zavřít objekt websocket předčasně způsobí vypršení časového limitu pro dřívější ve službě Cloud Shell.
- **Rozlišení**: Otevřete FireFox a přejděte do "o: konfigurace" v poli Adresa URL. Vyhledejte "network.websocket.timeout.ping.request" a změňte hodnotu od 0 do 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Zakázání Cloud Shell v uzamčeném síťové prostředí

- **Podrobnosti o**: Správce může přát zakázat přístup ke službě Cloud Shell pro své uživatele. Využívá přístup ke službě cloud Shell `ux.console.azure.com` doménu, která může odepřen, všechny přístupy do Cloud Shellu entrypoints včetně portal.azure.com, shell.azure.com, rozšíření Azure účet služby Visual Studio Code a docs.microsoft.com se zastavuje.
- **Rozlišení**: Omezení přístupu k `ux.console.azure.com` pomocí nastavení sítě pro vaše prostředí. Ikona Cloud Shell zůstanou uchovány portal.azure.com, ale nebude úspěšné připojení ke službě.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialogové okno úložiště – Chyba: 403 RequestDisallowedByPolicy

- **Podrobnosti o**: Při vytváření účtu úložiště Cloud Shell neúspěšný kvůli zásady služby Azure umístěn váš správce. Chybová zpráva bude obsahovat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozlišení**: Obraťte se na správce Azure odebrat nebo aktualizovat Azure policy odepření vytvoření úložiště.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialogové okno úložiště – Chyba: 400 DisallowedOperation

- **Podrobnosti o**: Pokud používají předplatné Azure Active Directory, nelze vytvořit úložiště.
- **Rozlišení**: Pomocí předplatného Azure dokáže vytvořit prostředky úložiště. Předplatná Azure AD, nebudou se moct vytváření prostředků Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminál výstup – Chyba: Nepovedlo se připojit terminál: protokolu websocket nelze navázat. Stisknutím klávesy `Enter` znovu připojit.
- **Podrobnosti o**: Cloud Shell vyžaduje oprávnění k vytvoření objektu websocket na straně připojení k službě Cloud Shell infrastruktury.
- **Rozlišení**: Zkontrolujte jste nakonfigurovali nastavení sítě, abyste povolili odeslání žádosti o https a požadavky protokolu websocket na domén na *. console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Nastavit připojení k službě Cloud Shell pro podporu použití protokolu TLS 1.2
 - **Podrobnosti o**: Chcete-li definovat verze protokolu TLS pro připojení do služby Cloud Shell, musíte nastavit konkrétní nastavení prohlížeče.
 - **Rozlišení**: Přejděte do nastavení zabezpečení prohlížeče a zaškrtněte políčko vedle "Použití protokolu TLS 1.2".

## <a name="bash-troubleshooting"></a>Řešení potíží s bash

### <a name="cannot-run-the-docker-daemon"></a>Nelze spustit démona dockeru

- **Podrobnosti o**: Cloud Shell využívá kontejner pro hostování vašeho prostředí, v důsledku spuštění démona se nepovoluje.
- **Rozlišení**: Využívat [docker-machine](https://docs.docker.com/machine/overview/), která se instaluje standardně Spravovat kontejnery dockeru ze vzdáleného hostitele Docker.

## <a name="powershell-troubleshooting"></a>Řešení potíží s Powershellu

### <a name="gui-applications-are-not-supported"></a>Aplikace grafického uživatelského rozhraní se nepodporují.

- **Podrobnosti o**: Pokud uživatel spustí aplikaci grafického uživatelského rozhraní, řádku tento příkaz nevrací. Například při klonování jeden privátní úložiště GitHub, který má dvoufaktorové ověřování povoleno, se zobrazí dialogové okno pro dokončení dvoufaktorového ověřování.
- **Rozlišení**: Zavřete a znovu otevřete prostředí.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Řešení potíží s Vzdálená správa virtuálních počítačů Azure
> [!NOTE]
> Virtuální počítače Azure musí mít veřejnou směřující IP adresu.

- **Podrobnosti o**: Z důvodu výchozí nastavení brány Windows Firewall pro WinRM může uživatel zobrazit následující chybová zpráva: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozlišení**:  Spustit `Enable-AzVMPSRemoting` povolit všechny aspekty vzdálené komunikace Powershellu na cílovém počítači.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` výsledek v Azure disk se neaktualizuje

- **Podrobnosti o**: Ve výchozím nastavení, pokud chcete optimalizovat uživatelské prostředí, výsledky `dir` se uloží do mezipaměti v Azure disk.
- **Rozlišení**: Po vytvoření, aktualizace nebo odebrání prostředku Azure, spusťte `dir -force` výsledky v Azure disk se nepodařilo aktualizovat.

## <a name="general-limitations"></a>Obecná omezení

Azure Cloud Shell má tato známá omezení:

### <a name="system-state-and-persistence"></a>Stav systému a trvalosti

Počítač, který poskytuje relace prostředí Cloud Shell je dočasný a bude recyklována po relaci je neaktivní po dobu 20 minut. Cloud Shell vyžaduje sdílenou složku Azure připojit. Předplatné musí být v důsledku toho nám nastavit prostředky úložiště pro přístup k službě Cloud Shell. Mezi další aspekty patří:

- Připojené úložiště, pouze změny v rámci `clouddrive` adresáře jsou trvalé. V prostředí Bash vaše `$HOME` adresáře je také zachována.
- Sdílené složky Azure je možné připojit pouze v rámci vaší [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  - V prostředí Bash, spusťte `env` najít vaši oblast nastavit jako `ACC_LOCATION`.
- Služba soubory Azure podporuje jen místně redundantní úložiště a účtů geograficky redundantního úložiště.

### <a name="browser-support"></a>Podpora prohlížeče

Cloud Shell podporuje nejnovější verze následujících prohlížečů:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari v privátním režimu není podporováno.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limity využití

Cloud Shell je určen pro případy použití interaktivní. V důsledku toho se dlouho probíhající relace jako neinteraktivní skončilo bez upozornění.

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění se nastavují jako běžní uživatelé bez přístupu sudo. Všechny instalace mimo váš `$Home` adresář není trvalý.

## <a name="bash-limitations"></a>Omezení bash

### <a name="editing-bashrc"></a>Úpravy .bashrc

Provést upozornění, když úpravy .bashrc, to může způsobit neočekávané chyby ve službě Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="preview-version-of-azuread-module"></a>Ve verzi Preview verzi modulu Azure AD

V současné době `AzureAD.Standard.Preview`, je k dispozici ve verzi preview verze modulu .NET Standard přihlašování. Tento modul poskytuje stejné funkce jako `AzureAD`.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modul funkce

`SqlServer` Ve službě Cloud Shell zahrnuje modul má pouze předprodejní podpora pro PowerShell Core. Zejména `Invoke-SqlCmd` ještě není k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souborů, když se vytvoří jednotka Azure

Pomocí rutin prostředí PowerShell, uživatelé nemůžou vytvářet soubory v Azure disk. Když uživatelé vytvářejí nové soubory pomocí jiných nástrojů, jako je například vim nebo nano, soubory se uloží do `$HOME` ve výchozím nastavení.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Dokončování pomocí tabulátoru můžete vyvolat výjimku PSReadline

Pokud PSReadline EditMode daného uživatele nastavená na (emacs), uživatel se pokusí zobrazit všechny možnosti prostřednictvím tab k dokončování příkazů a velikost okna je příliš malá, zobrazíte všechny možnosti, PSReadline vyvolá neošetřenou výjimku.

### <a name="large-gap-after-displaying-progress-bar"></a>Velké mezeru po zobrazuje indikátor průběhu

Pokud příkaz nebo uživatelské akce zobrazí indikátor, tato karta při dokončení v `Azure:` jednotky, pak je možné, že kurzor nejsou správně nastaveny a mezera se zobrazí, pokud byla dříve indikátor průběhu.

### <a name="random-characters-appear-inline"></a>Zobrazí vložený náhodných znaků

Například sekvence pozice kurzoru kódů `5;13R`, se mohou objevit v vstupu uživatele. Znaky lze ručně odebrat.

## <a name="personal-data-in-cloud-shell"></a>Osobní údaje ve službě Cloud Shell

Azure Cloud Shell používá vaše osobní údaje vážně, data, zachytí a uložené ve službě Azure Cloud Shell se používají k zajištění výchozí hodnoty pro vaše prostředí, jako je vaše naposledy použité prostředí, upřednostňované písmo, upřednostňované písmo a sdílených složek podrobnosti který zpět cloudové jednotky. Pokud, budete chtít exportovat nebo odstraňovat data, použijte následující pokyny.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Za účelem **exportovat** uživatelská nastavení uloží Cloud Shell za vás jako upřednostňované prostředí a velikost písma a typ písma, spusťte následující příkazy.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)
2. V prostředí Bash nebo PowerShell spusťte následující příkazy:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Odstranění
Za účelem **odstranit** uživatelských nastavení uloží Cloud Shell za vás jako upřednostňované prostředí a velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění Cloud Shell zobrazí se výzva k připojení sdílené složky znovu. 

>[!Note]
> Pokud odstraníte uživatelská nastavení, skutečné sdílenou složku služby soubory Azure se neodstraní. Přejděte do soubory Azure k dokončení této akce.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)
2. V prostředí Bash nebo PowerShell spusťte následující příkazy:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
