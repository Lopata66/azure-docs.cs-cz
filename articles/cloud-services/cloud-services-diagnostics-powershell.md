---
title: Povolení diagnostiky v Azure Cloud Services pomocí PowerShellu | Microsoft Docs
description: Naučte se, jak povolit diagnostiku pro cloudové služby pomocí PowerShellu.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: f2b7e51971cc2e540ee7745b3b44571c58359613
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860214"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Povolení diagnostiky v Azure Cloud Services s využitím PowerShellu
Můžete shromažďovat diagnostická data, jako jsou protokoly aplikací, čítače výkonu atd. z cloudové služby pomocí rozšíření Azure Diagnostics. Tento článek popisuje, jak povolit rozšíření Azure Diagnostics pro cloudovou službu pomocí PowerShellu.  Potřebné předpoklady pro tento článek najdete v tématu [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/overview) .

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Tento přístup se vztahuje na typ souvislé integrace scénářů, kde diagnostické rozšíření může být povolené v rámci nasazení cloudové služby. Při vytváření nového nasazení cloudové služby můžete povolit rozšíření diagnostiky tak, že do rutiny [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) předáte parametr *ExtensionConfiguration* . Parametr *ExtensionConfiguration* přebírá pole konfigurací diagnostiky, které lze vytvořit pomocí rutiny [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) .

Následující příklad ukazuje, jak můžete povolit diagnostiku pro cloudovou službu pomocí webrole a role pracovního procesu, z nichž každá má jinou konfiguraci diagnostiky.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Pokud konfigurační soubor diagnostiky Určuje `StorageAccount` element s názvem účtu úložiště, `New-AzureServiceDiagnosticsExtensionConfig` použije rutina automaticky tento účet úložiště. Aby to fungovalo, musí být účet úložiště ve stejném předplatném, jako je nasazená cloudová služba.

Z Azure SDK 2,6 další konfigurační soubory rozšíření generované výstupem cíle publikování MSBuild budou zahrnovat název účtu úložiště na základě konfiguračního řetězce pro diagnostiku, který je zadaný v konfiguračním souboru služby (. cscfg). Skript níže ukazuje, jak analyzovat konfigurační soubory rozšíření z výstupu cíle publikování a nakonfigurovat diagnostické rozšíření pro každou roli při nasazení cloudové služby.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online používá podobný přístup pro automatizované nasazení Cloud Services s diagnostickým rozšířením. Úplný příklad naleznete v tématu [Publish-AzureCloudDeployment. ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) .

Pokud se `StorageAccount` v konfiguraci diagnostiky nezadal žádný, pak musíte rutinu předat parametr *StorageAccountName* . Je-li zadán parametr *StorageAccountName* , rutina vždy použije účet úložiště, který je zadán v parametru, a nikoli ten, který je zadán v konfiguračním souboru diagnostiky.

Pokud je účet úložiště diagnostiky v jiném předplatném, než je cloudová služba, musíte explicitně předat parametry *StorageAccountName* a *StorageAccountKey* do rutiny. Parametr *StorageAccountKey* není potřeba, pokud je účet úložiště diagnostiky ve stejném předplatném, protože se rutina může automaticky dotazovat a nastavit hodnotu klíče při povolování diagnostického rozšíření. Pokud je však účet úložiště diagnostiky v jiném předplatném, rutina nemusí být schopna získat klíč automaticky a Vy musíte explicitně zadat klíč prostřednictvím parametru *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Pomocí rutiny [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) můžete povolit nebo aktualizovat konfiguraci diagnostiky v cloudové službě, která je už spuštěná.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Získejte aktuální konfiguraci rozšíření diagnostiky
Pomocí rutiny [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) získáte aktuální konfiguraci diagnostiky pro cloudovou službu.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
Pokud chcete vypnout diagnostiku v cloudové službě, můžete použít rutinu [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili diagnostické rozšíření pomocí *set-AzureServiceDiagnosticsExtension* nebo *New-AzureServiceDiagnosticsExtensionConfig* bez parametru *role* , můžete rozšíření odebrat pomocí  *Remove-AzureServiceDiagnosticsExtension* bez parametru *role* . Pokud byl parametr *role* použit při povolení rozšíření, musí být použit také při odebrání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Další kroky
* Další pokyny týkající se používání diagnostiky Azure a dalších postupů k řešení problémů najdete v tématu [Povolení diagnostiky v Azure Cloud Services a Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) vysvětluje různé možnosti konfigurace XML pro diagnostické rozšíření.
* Informace o tom, jak povolit rozšíření diagnostiky pro Virtual Machines, najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky pomocí šablony Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) .
