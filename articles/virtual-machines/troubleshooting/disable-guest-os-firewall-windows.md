---
title: Zakázání brány firewall hostovaného operačního systému ve virtuálním počítači Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 9ae8620b803fa9a911f44840a5fff5d190a316a1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086538"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Zakázání brány firewall hostovaného operačního systému na virtuálním počítači Azure

Tento článek poskytuje referenční informace o situacích, ve kterých máte podezření, že brána firewall hostovaného operačního systému filtruje částečný nebo kompletní provoz do virtuálního počítače. Tato situace může nastat, pokud byly provedeny změny v bráně firewall, která způsobila selhání připojení RDP.

## <a name="solution"></a>Řešení

Postup, který je popsaný v tomto článku, je určený k použití jako alternativní řešení, abyste se mohli soustředit na řešení reálného problému, který je správně nastavený na pravidla brány firewall. It\rquote s osvědčeným postupem Microsoftu je povolit součást brány Windows Firewall. Způsob konfigurace pravidel brány firewall \cf3 závisí na úrovni přístupu k virtuálnímu počítači that\rquote, který je povinný.

### <a name="online-solutions"></a>Online řešení 

Pokud je virtuální počítač online a lze k němu přistupovat na jiném virtuálním počítači ve stejné virtuální síti, můžete tato omezení udělat pomocí jiného virtuálního počítače.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Zmírnění 1: Rozšíření vlastních skriptů nebo funkce příkazu Spustit

Pokud máte funkčního agenta Azure, můžete použít [rozšíření vlastních skriptů](../extensions/custom-script-windows.md) nebo funkce [Run Commands](../windows/run-command.md) (jenom správce prostředků virtuální počítače) a vzdáleně spouštět následující skripty.

> [!Note]
> * Pokud je brána firewall nastavená místně, spusťte následující skript:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Pokud je brána firewall nastavená prostřednictvím zásad služby Active Directory, můžete pro dočasný přístup použít příkaz Spustit následující skript. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Jakmile se ale zásada znovu použije, stane se Vzdálená relace. Trvalá oprava tohoto problému je úprava zásad, které se na tomto počítači aplikují.

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění 2: Vzdálené prostředí PowerShell

1.  Připojte se k virtuálnímu počítači, který se nachází ve stejné virtuální síti jako virtuální počítač, ke kterému se nemůžete připojit pomocí připojení RDP.

2.  Otevřete okno konzole Powershellu.

3.  Spusťte následující příkazy:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Pokud je brána firewall nastavená prostřednictvím objektu Zásady skupiny, tato metoda nemusí fungovat, protože tento příkaz změní jenom místní položky registru. Pokud je zásada nastavená, přepíše se tato změna. 

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: Příkazy nástroj PsTools

1.  Na virtuálním počítači pro řešení potíží stáhněte [Nástroj PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otevřete instanci CMD a potom přejděte k virtuálnímu počítači přes jeho DIP.

3.  Spusťte následující příkazy:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění omezení 4: Vzdálený registr 

Při použití [vzdáleného registru](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)použijte následující postup.

1.  Na virtuálním počítači pro řešení potíží spusťte Editor registru a pak klikněte na **soubor** > **připojit k síťovému registru**.

2.  Otevřete cílovou větev *počítače*\System a zadejte následující hodnoty:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Restartujte službu. Vzhledem k tomu, že to nelze provést pomocí vzdáleného registru, je nutné použít příkaz odebrat konzolu služby.

4.  Otevřete instanci **služby Services. msc**.

5.  Klikněte na **služby (místní)** .

6.  Vyberte **připojit k jinému počítači**.

7.  Zadejte **privátní IP adresu (DIP)**  problému virtuálního počítače.

8.  Restartujte místní zásady brány firewall.

9.  Zkuste se připojit k VIRTUÁLNÍmu počítači pomocí protokolu RDP znovu z místního počítače.

### <a name="offline-solutions"></a>Řešení offline 

Pokud máte situaci, kdy se k virtuálnímu počítači nemůžete dostat pomocí žádné metody, rozšíření vlastních skriptů se nezdaří a bude nutné pracovat v OFFLINE režimu tak, že přímo na systémovém disku pracujete. Provedete to podle těchto kroků:

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Ujistěte se, že je disk označen jako online v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému systémovému disku.

4.  Než provedete jakékoli změny, vytvořte kopii složky \Windows\System32\Config v případě, že je nutné vrátit zpět změny.

5.  Na virtuálním počítači pro řešení potíží spusťte Editor registru (Regedit. exe). 

6.  Pro tento postup řešení potíží Namontujte podregistry jako BROKENSYSTEM a BROKENSOFTWARE.

7.  Zvýrazněte klíč HKEY_LOCAL_MACHINE a v nabídce vyberte soubor > Načíst podregistr.

8.  Vyhledejte soubor \windows\system32\config\SYSTEM na připojeném systémovém disku.

9.  Otevřete instanci PowerShellu se zvýšenými oprávněními a pak spusťte následující příkazy:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Odpojení disku a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.
