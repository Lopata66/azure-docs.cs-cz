---
title: Automatizace ověření služby Azure Stack pomocí Powershellu | Dokumentace Microsoftu
description: Ověření Azure Stack pomocí Powershellu, můžete automatizovat.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 99a1b218919380dcdb0c1dba4540d9f8219d2df3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593637"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizace ověření služby Azure Stack pomocí Powershellu

Ověření jako služba (VaaS) poskytuje možnost automatizovat spouštění testů pomocí **LaunchVaaSTests.ps1** skriptu.

> [!NOTE]
> Automation je dostupná jenom pro pracovní postup průchodu testu. Ověření balíčku a ověření řešení pracovní postupy se podporují jenom prostřednictvím portálu VaaS.

V tomto kurzu se dozvíte, jak vytvořit skript, který:

> [!div class="checklist"]
> * Nainstaluje požadavky
> * Nainstaluje a spustí místní agent
> * Spustí kategorie testů, jako jsou integrace, funkční, spolehlivosti
> * Sestavy výsledků testů

## <a name="launch-the-test-pass-workflow"></a>Spuštění pracovního postupu průchodu testu

1. Otevřete řádku Powershellu se zvýšenými oprávněními.

2. Spusťte následující skript ke stažení skriptu pro automatizaci:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. S hodnotami parametrů odpovídající, spusťte následující skript:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parametry**

    | Parametr | Popis |
    | --- | --- |
    | VaaSUserId | Vaše ID uživatele VaaS. |
    | VaaSUserPassword | VaaS heslo. |
    | VaaSAccountTenantId | Tenanta VaaS identifikátor GUID. |
    | VaaSSolutionName | Název VaaS řešení, ve kterém je test úspěšný, poběží. |
    | VaaSTestPassName | Název testu VaaS předat pracovní postup k vytvoření. |
    | VaaSTestCategories | `Integration`, `Functional`, nebo. `Reliability`. Pokud používáte více hodnot, jednotlivé hodnoty oddělte je čárkami.  |
    | ServiceAdminUserName | Účet správce služby Azure Stack.  |
    | ServiceAdminPassword | Heslo služby Azure Stack.  |
    | TenantAdminUserName | Správce pro primární tenant.  |
    | TenantAdminPassword | Heslo pro primární tenant.  |
    | CloudAdminUserName | Uživatelské jméno správce cloudu.  |
    | CloudAdminPassword | Heslo pro správce cloudu.  |

4. Zkontrolujte výsledky testu. Další možnosti najdete v tématu [monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Další postup

Další informace o prostředí PowerShell ve službě Azure Stack, přečtěte si nejnovější moduly.

> [!div class="nextstepaction"]
> [Modul služby Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
