---
title: Použití úložiště objektů blob pro službu IIS a table storage pro události ve službě Azure Monitor | Dokumentace Microsoftu
description: Azure Monitor můžete přečíst v protokolech služby Azure, které zápis diagnostiky do table storage nebo protokoly IIS zapsána do úložiště objektů blob.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 35befe7122f493998d0d91c2721e6013e057fed3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540597"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-azure-monitor"></a>Používání úložiště objektů blob v Azure pro službu IIS a Azure table storage pro události prostřednictvím služby Azure Monitor

Azure Monitor můžete přečíst v protokolech tyto služby, které zapsat diagnostiky do table storage nebo protokoly IIS zapsána do úložiště objektů blob:

* Clustery Service Fabric (verze Preview)
* Virtuální počítače
* Webové a pracovní role

Předtím, než Azure Monitor může shromažďovat data do pracovního prostoru Log Analytics pro tyto prostředky, musí být povolená Diagnostika Azure.

Jakmile je povolená Diagnostika, můžete použít na webu Azure portal nebo Powershellu konfigurovat pracovní prostor ke shromažďování protokolů.

Diagnostika Azure je rozšířením Azure, která umožňuje shromažďovat diagnostická data z role pracovního procesu, webové role nebo virtuálního počítače spuštěného v Azure. Data uložená v účtu služby Azure storage a můžou shromažďovat službou Azure Monitor.

Protokoly Azure monitoru ke shromažďování těchto protokolech diagnostiky Azure, musí být v následujících umístěních:

| Typ protokolu | Typ prostředku | Umístění |
| --- | --- | --- |
| Protokoly IIS |Virtuální počítače <br> Webové role <br> Role pracovního procesu |wad-iis-logfiles (úložiště objektů Blob) |
| Syslog |Virtuální počítače |LinuxsyslogVer2v0 (Table Storage) |
| Provozní události služby Service Fabric |Uzly Service Fabricu |WADServiceFabricSystemEventTable |
| Události služby Service Fabric Reliable Actors |Uzly Service Fabricu |WADServiceFabricReliableActorEventTable |
| Události služby Service Fabric Reliable Service |Uzly Service Fabricu |WADServiceFabricReliableServiceEventTable |
| Protokoly událostí Windows |Uzly Service Fabricu <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADWindowsEventLogsTable (Table Storage) |
| Protokoly trasování událostí pro Windows Windows |Uzly Service Fabricu <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADETWEventTable (Table Storage) |

> [!NOTE]
> Protokoly služby IIS z Azure Websites se momentálně nepodporují.
>
>

Pro virtuální počítače, máte možnost instalace [agenta Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) k virtuálnímu počítači povolit další statistiky. Kromě schopnost analyzovat protokoly událostí a protokoly služby IIS, můžete provádět další analýzu, včetně sledování změn konfigurace SQL posouzení a posouzení aktualizací.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Azure diagnostics na virtuálním počítači povolit protokol událostí a služby IIS protokolu kolekce
Následujícím postupem povolte Azure diagnostics na virtuálním počítači pro shromažďování protokolů služby IIS a protokolu událostí na portálu Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Povolení diagnostiky Azure v rámci virtuálního počítače pomocí webu Azure portal
1. Při vytváření virtuálního počítače, nainstalujte agenta virtuálního počítače. Pokud virtuální počítač už existuje, ověřte, že Agent virtuálního počítače je už nainstalovaný.

   * Na webu Azure Portal, přejděte k virtuálnímu počítači, vyberte **volitelná konfigurace**, pak **diagnostiky** a nastavte **stav** k **na** .

     Po dokončení se virtuální počítač má rozšíření Azure Diagnostics nainstalovaný a spuštěný. Toto rozšíření je zodpovědná za shromažďovat diagnostická data.
2. Povolení monitorování a konfigurace protokolování událostí v existující virtuální počítač. Můžete povolit diagnostiku na úrovni virtuálního počítače. Povolení diagnostiky a potom nakonfigurujte protokolování událostí, postupujte následovně:

   1. Vyberte virtuální počítač.
   2. Klikněte na tlačítko **monitorování**.
   3. Klikněte na tlačítko **diagnostiky**.
   4. Nastavte **stav** k **ON**.
   5. Vyberte každou diagnostický protokol, který chcete shromáždit.
   6. Klikněte na **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Povolení diagnostiky Azure ve webové roli pro shromažďování protokolů a událostí služby IIS
Odkazovat na [jak k povolení diagnostiky v cloudové službě](../../cloud-services/cloud-services-dotnet-diagnostics.md) obecné pokyny k povolení diagnostiky Azure. Podle následujících pokynů tuto informaci použít a přizpůsobit ho pro použití se službou Log Analytics.

S povolenou diagnostikou Azure:

* Protokoly služby IIS jsou uloženy ve výchozím nastavení, s přenesených v intervalu hodnota scheduledTransferPeriod přenosu dat protokolu.
* Ve výchozím nastavení se nepřenesou protokoly událostí Windows.

### <a name="to-enable-diagnostics"></a>Povolit diagnostiku
Povolení protokolů událostí Windows, nebo chcete změnit hodnota scheduledTransferPeriod, konfigurování diagnostiky Azure pomocí konfiguračního souboru XML (diagnostics.wadcfg), jak je znázorněno v [krok 4: Vytvořte konfigurační soubor diagnostiky a instalace rozšíření](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Následující příklad konfiguračního souboru shromažďuje protokoly služby IIS a všechny události z protokolů aplikace a systém:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Ujistěte se, že vaše ConfigurationSettings Určuje účet úložiště, jako v následujícím příkladu:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** a **AccountKey** hodnoty se nacházejí na webu Azure Portal na řídicím panelu úložiště účtu, pod spravovat přístupové klíče. Protokol pro připojovací řetězec musí být **https**.

Jakmile aktualizované konfigurace diagnostiky se použije ke cloudové službě a je Diagnostika zapisuje do služby Azure Storage, pak jste připraveni ke konfiguraci pracovního prostoru Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Shromažďování protokolů ze služby Azure Storage pomocí webu Azure portal
Na webu Azure portal můžete použít ke konfiguraci pracovního prostoru Log Analytics ve službě Azure Monitor pro shromažďování protokolů u následujících služeb Azure:

* Clustery Service Fabricu
* Virtuální počítače
* Webové a pracovní role

Na webu Azure Portal přejděte do pracovního prostoru Log Analytics a provádět následující úlohy:

1. Klikněte na tlačítko *protokoly účtů úložiště*
2. Klikněte na tlačítko *přidat* úkolu
3. Vyberte účet úložiště, který obsahuje diagnostické protokoly
   * Tento účet může být klasický účet úložiště nebo účet úložiště Azure Resource Manageru
4. Vyberte typ dat chcete shromažďovat protokoly
   * Možnosti jsou protokoly služby IIS. Události. Syslog (Linux); Protokoly trasování událostí pro Windows. Události Service Fabric
5. Hodnota zdroje se vyplní automaticky na základě datového typu a nedá se změnit
6. Kliknutím na tlačítko OK uložte konfiguraci

Opakujte kroky 2 až 6 pro další účty úložiště a datové typy, které chcete shromáždit do pracovního prostoru.

Během přibližně 30 minut budete moct zobrazit data z účtu úložiště v pracovním prostoru Log Analytics. Zobrazí se pouze data, která jsou zapsána do úložiště, až tato konfigurace používá. Pracovní prostor nenačítá existující data z účtu úložiště.

> [!NOTE]
> Na portálu se neověřuje, jestli zdroj existuje v účtu úložiště nebo pokud je zapisovaná nová data.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Azure diagnostics na virtuálním počítači povolit protokol událostí a služby IIS protokolu kolekce pomocí Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Postupujte podle kroků v [konfigurace Azure Monitor k indexování Azure diagnostics](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) čtení z diagnostiky Azure, který se zapisovat do table storage pomocí Powershellu.

Pomocí prostředí Azure PowerShell můžete přesněji určit události, které jsou zapsány do služby Azure Storage.
Další informace najdete v tématu [povolení diagnostiky v Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Můžete povolit a aktualizovat diagnostiky Azure pomocí následujícího skriptu prostředí PowerShell.
Tento skript můžete použít také s vlastní konfiguraci protokolování.
Upravte skript, který chcete nastavit účet úložiště, název služby a název virtuálního počítače.
Skript používá rutiny pro klasické virtuální počítače.

Projděte si následující ukázkový skript, zkopírujte ho, podle potřeby upravte, uložte vzorku jako soubor skriptu Powershellu a spusťte skript.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Další postup
* [Shromažďovat protokoly a metriky pro služby Azure](collect-azure-metrics-logs.md) pro podporované služby Azure.
* [Povolení řešení](../../azure-monitor/insights/solutions.md) k poskytování přehledů o data.
* [Pomocí vyhledávacích dotazů](../../azure-monitor/log-query/log-query-overview.md) chcete analyzovat data.
