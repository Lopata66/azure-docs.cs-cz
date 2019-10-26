---
title: Řešení potíží s rozšířením virtuálního počítače Azure Log Analytics v Azure Monitor | Microsoft Docs
description: Popište příznaky, příčiny a řešení nejběžnějších problémů s rozšířením Log Analytics virtuálního počítače pro virtuální počítače Azure s Windows a Linux.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/06/2019
ms.openlocfilehash: 9ec0d5036632c575415a7de19b9ea35eb2a28118
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931929"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Řešení potíží s rozšířením Log Analytics VM v Azure Monitor
Tento článek poskytuje nápovědu k řešení chyb, ke kterým může docházet s rozšířením virtuálního počítače s Log Analytics pro virtuální počítače s Windows a Linux běžící v Microsoft Azure a navrhuje možná řešení pro jejich řešení.

Chcete-li ověřit stav rozšíření, proveďte následující kroky z Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **virtuální počítače**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **virtuální počítače**.
3. V seznamu virtuálních počítačů ho vyhledejte a vyberte.
3. Na virtuálním počítači klikněte na **rozšíření**.
4. V seznamu Zkontrolujte, zda je povoleno rozšíření Log Analytics.  Pro Linux je agent uveden jako **OMSAgentforLinux** a pro Windows, agent je uveden jako **MicrosoftMonitoringAgent**.

   ![Zobrazení rozšíření virtuálních počítačů](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknutím na rozšíření zobrazíte podrobnosti. 

   ![Podrobnosti o rozšíření virtuálního počítače](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Řešení potíží s rozšířením virtuálního počítače Azure s Windows

Pokud rozšíření virtuálního počítače *Microsoft Monitoring Agent* neinstaluje nebo nevytváří sestavy, můžete tento problém vyřešit provedením následujících kroků.

1. Pomocí kroků v [článku KB 2965986](https://support.microsoft.com/kb/2965986#mt1)ověřte, zda je agent virtuálního počítače Azure nainstalovaný a funguje správně.
   * Můžete také zkontrolovat soubor protokolu agenta virtuálního počítače `C:\WindowsAzure\logs\WaAppAgent.log`
   * Pokud protokol neexistuje, není nainstalován agent virtuálního počítače.
   * [Instalace agenta virtuálního počítače Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Zkontrolujte soubory protokolu rozšíření Microsoft Monitoring Agent VM v `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Ujistěte se, že virtuální počítač může spustit PowerShellové skripty.
4. Zajistěte, aby se oprávnění na C:\Windows\temp nezměnila.
5. Zadáním následujícího příkazu v okně PowerShellu se zvýšenými oprávněními na virtuálním počítači zobrazte stav Microsoft Monitoring Agent `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Zkontrolujte soubory protokolu instalace Microsoft Monitoring Agent v `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Další informace najdete v tématu [řešení potíží s rozšířeními systému Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Řešení potíží s rozšířením virtuálního počítače Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Pokud se neinstaluje nebo nehlásí rozšíření *Log Analytics Agent pro Linux* VM, můžete problém vyřešit provedením následujících kroků.

1. Pokud je stav rozšíření *neznámá* kontrola, jestli je agent virtuálního počítače Azure nainstalovaný a funguje správně, Projděte si soubor protokolu agenta virtuálního počítače `/var/log/waagent.log`
   * Pokud protokol neexistuje, není nainstalován agent virtuálního počítače.
   * [Instalace agenta virtuálního počítače Azure do virtuálních počítačů se systémem Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. U dalších stavů, které nejsou v pořádku, zkontrolujte soubory protokolů rozšíření Log Analytics Agent pro Linux VM v `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` a `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Pokud je stav rozšíření v pořádku, ale data se neodesílají, zkontrolujte soubory protokolu Log Analytics agenta pro Linux v `/var/opt/microsoft/omsagent/log/omsagent.log`

Další informace najdete v tématu [řešení potíží s rozšířeními pro Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Další kroky

Další pokyny k odstraňování potíží souvisejících s agentem Log Analytics pro Linux hostovanou na počítačích mimo Azure najdete v tématu [řešení potíží s agentem azure Log Analytics Linux](agent-linux-troubleshoot.md).  
