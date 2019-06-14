---
title: Licenční server vzdálené plochy není k dispozici, když se připojíte k virtuálnímu počítači Azure | Dokumentace Microsoftu
description: Informace o řešení potíží s RDP selhání protože není k dispozici žádné licenční server vzdálené plochy | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318948"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Licenčního serveru vzdálené plochy není k dispozici, když se připojíte k virtuálnímu počítači Azure

Tento článek pomůže problém vyřešit, když nemůžete připojit na virtuálním počítači Azure (VM), protože je mohly poskytnout licenci k dispozici žádné licenční server vzdálené plochy.

## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači (VM), vyzkoušejte následující scénáře:

- Snímek obrazovky virtuálního počítače ukazuje, že je operační systém plně načtený a čeká se přihlašovací údaje.
- Zobrazí se následující chybové zprávy při pokusu o vytvoření připojení Microsoft protokolu RDP (Remote Desktop):

  - Vzdálená relace byla odpojena, protože neexistují žádné licenční servery vzdálené plochy k dispozici mohly poskytnout licenci.

  - Je k dispozici žádné licenční server vzdálené plochy. Vzdálená plocha přestane pracovat, protože tento počítač po jeho období odkladu a nekontaktovalo alespoň platný licenční server Windows Server 2008. Vyberte tuto zprávu otevřete konfiguraci serveru hostitele relace VP používat Diagnostika licencí.

Ale můžete připojit k virtuálnímu počítači obvykle s využitím relaci pro správu:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud není k dispozici mohly poskytnout licenci ke spuštění vzdálené relace licenční server vzdálené plochy. I když na virtuálním počítači nastavil roli hostitel relace vzdálené plochy se může být způsobeno několika situacích:

- V prostředí se nikdy role Licencování vzdálené plochy a období odkladu skončí 180 dnů.
- Licence vzdálené plochy byla nainstalována v prostředí, ale nikdy se aktivuje.
- Licence vzdálené plochy v prostředí nemá licencí pro klientský přístup (CAL) vloží k nastavení připojení.
- Licence vzdálené plochy se nainstaloval v prostředí. Nejsou dostupné volné licence CAL, ale nebyly nakonfigurovány správně.
- Licence vzdálené plochy má licence pro klientský přístup a byla aktivována. Nicméně některé další problémy na licenční server vzdálené plochy nechcete, aby se z poskytování licencí v prostředí.

## <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém [zálohování disku s operačním systémem](../windows/snapshot-copy-managed-disk.md) a postupujte podle těchto kroků:

1. Připojení k virtuálnímu počítači s použitím relaci pro správu:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Pokud se nemůžete připojit k virtuálnímu počítači pomocí relaci pro správu, můžete použít [konzoly sériového portu virtuálního počítače v Azure](serial-console-windows.md) pro přístup k virtuálnímu počítači následujícím způsobem:

    1. Přístup ke konzole sériového portu tak, že vyberete **podpora a řešení potíží** > **sériová konzola (Preview)** . Pokud je povolená funkce, na virtuálním počítači, může úspěšně připojit virtuální počítač.

    2. Vytvořte nový kanál pro instanci CMD. Zadejte **CMD** spuštění kanálu a získat název kanálu.

    3. Přepnout na kanál, který spouští instanci CMD. V takovém případě bude kanál 1:

       ```
       ch -si 1
       ```

    4. Vyberte **Enter** znovu a zadejte platné uživatelské jméno a heslo, místní nebo doménový ID pro virtuální počítač.

2. Zkontrolujte, zda má virtuální počítač povolenou roli hostitel relace vzdálené plochy. Pokud role je povolena, ujistěte se, že funguje správně. Spusťte instanci se zvýšenými oprávněními příkaz CMD a postupujte podle těchto kroků:

    1. Použijte následující příkaz a zkontrolujte stav role Hostitel relace vzdálené plochy:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Pokud tento příkaz vrátí hodnotu 0, znamená to, že role je zakázaná, a můžete přejít ke kroku 3.

    2. Použijte následující příkaz a zkontrolujte tyto zásady znovu nakonfigurujte podle potřeby:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Pokud **LicensingMode** hodnota je nastavena na jakoukoli jinou hodnotu než 4 na uživatele, pak nastavte hodnotu na 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Pokud **SpecifiedLicenseServers** hodnota neexistuje, nebo má nesprávný licenční informace o serveru, pak ji změňte takto:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Když provedete změny do registru, restartujte virtuální počítač.

    4. Pokud nemáte licence pro klientský přístup, odeberte roli hostitel relace vzdálené plochy. Pak se protokol RDP nastaví zpět na normální. Umožňuje pouze dvě souběžná připojení RDP k virtuálnímu počítači:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Pokud virtuální počítač má role Licencování vzdálené plochy a se nepoužívá, můžete také odebrat tuto roli:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Ujistěte se, že virtuální počítač může připojit k licenční server vzdálené plochy. Můžete otestovat připojení k portu 135 mezi tímto a licenční server: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Pokud není žádná licenční server vzdálené plochy v prostředí a chcete, můžete si [instalace služby role Licencování vzdálené plochy](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Potom [konfigurace licencování vzdálené plochy](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Pokud licenční server vzdálené plochy je nakonfigurovaná a funkční, ujistěte se, že licenční server vzdálené plochy se aktivuje pomocí licence pro klientský přístup.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) k vyřešení problému.
