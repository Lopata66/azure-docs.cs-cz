---
title: Tiché instalace Azure Backup Server V2
description: Použijte Powershellový skript bezobslužné instalace Azure Backup Server V2. Tento typ instalace se také nazývá bezobslužné instalace.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: 66ed5765a91b607bc5b765926c5df87d13ff6a24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109844"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Spuštění bezobslužné instalace Azure Backup serveru

Zjistěte, jak spuštění bezobslužné instalace Azure Backup serveru.

Tyto kroky nevztahují při instalaci Azure Backup Server V1.

## <a name="install-backup-server"></a>Instalace serveru pro zálohování

1. Na serveru, který hostuje Azure Backup Server V2 nebo později, vytvořte textový soubor. (Tento soubor můžete vytvořit v programu Poznámkový blok nebo jiném textovém editoru.) Uložte soubor jako MABSSetup.ini.

2. Vložte následující kód do souboru MABSSetup.ini. Nahraďte text v závorkách (\< \>) s hodnotami ze svého prostředí. Následující text je příklad:

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Uložte soubor. Na příkazového řádku se zvýšenými oprávněními na instalačním serveru zadejte tento příkaz:

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Pro instalaci, můžete použít tyto příznaky:</br>
**/f**: cesta k souboru INI</br>
**/l**: Cesta k protokolu</br>
**/i**: Instalační cesta</br>
**/x**: Odinstalace cesta</br>

## <a name="next-steps"></a>Další postup
Po instalaci serveru pro zálohování zjistěte, jak připravit váš server, nebo začít chránit úlohy.

- [Příprava úloh zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Pomocí zálohování serveru k zálohování SQL serveru](backup-azure-sql-mabs.md)
- [Přidání moderního úložiště záloh k zálohování serveru](backup-mabs-add-storage.md)
