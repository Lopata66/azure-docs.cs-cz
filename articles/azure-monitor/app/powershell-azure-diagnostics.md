---
title: Použití prostředí PowerShell k nastavení Application Insights v Azure | Dokumentace Microsoftu
description: Automatizujte konfiguraci diagnostiky Azure tak, aby data přepojila na Přehledy aplikací.
ms.topic: conceptual
ms.date: 08/06/2019
ms.openlocfilehash: da1796c8af5b9463d8223615f4b0629ba65eb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669799"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Nastavení přehledů aplikací pro Cloudové služby Azure pomocí PowerShellu

[Microsoft Azure](https://azure.com) může být [konfigurované k odesílání Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) do [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Diagnostika se týká cloudových služeb Azure a virtuálních počítačů Azure. Doplňují telemetrii, kterou odesíláte z aplikace pomocí Application Insights SDK. Jako součást automatizace procesu vytváření nových prostředků v Azure můžete nakonfigurovat diagnostiku pomocí prostředí PowerShell.

## <a name="azure-template"></a>Šablony Azure
Pokud je webová aplikace v Azure a vy vytvoříte své prostředky pomocí šablony správce prostředků Azure, můžete nakonfigurovat Application Insights přidáním tohoto uzlu prostředků:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` – název prostředku Application Insights
* `myWebAppName`- ID webové aplikace

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Povolit rozšíření diagnostiky jako součást nasazení cloudové služby
Rutina `New-AzureDeployment` obsahuje parametr `ExtensionConfiguration`, který přijímá pole konfigurace diagnostiky. Ty lze vytvořit pomocí rutiny `New-AzureServiceDiagnosticsExtensionConfig`. Například:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Povolit rozšíření diagnostiky na existující službu Cloud
Na existující službu použijte `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Získejte aktuální konfiguraci rozšíření diagnostiky
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Odeberte rozšíření diagnostiky
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Pokud jste povolili rozšíření diagnostiky pomocí `Set-AzureServiceDiagnosticsExtension` nebo `New-AzureServiceDiagnosticsExtensionConfig` bez parametru Role můžete rozšíření odebrat pomocí `Remove-AzureServiceDiagnosticsExtension` bez parametru Role. Pokud byl použit parametr role při povolování rozšíření, pak musí být rovněž použit při odebírání rozšíření.

Chcete-li odebrat rozšíření diagnostiky pro každou jednotlivou roli:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Viz také
* [Monitorování aplikací Azure Cloud Services pomocí nástroje Application Insights](../../azure-monitor/app/cloudservices.md)
* [Odesílání Azure Diagnostics do Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatizace konfigurace výstrah](powershell-alerts.md)

