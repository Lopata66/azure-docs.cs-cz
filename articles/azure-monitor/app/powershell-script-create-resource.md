---
title: Skript Powershellu pro vytvoření prostředku Application Insights | Dokumentace Microsoftu
description: Automatizace vytváření prostředků Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 8a7b19dd6e5bc08c0c7e278b514ecaa9dc13a00e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254483"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Rutina PowerShell pro vytvoření prostředku Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete monitorování nové aplikace - nebo nové verze aplikace – díky [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), nastavte nový prostředek v Microsoft Azure. Tento prostředek je kde analyzovat a zobrazit telemetrická data z vaší aplikace. 

Vytvoření nového prostředku můžete automatizovat pomocí prostředí PowerShell.

Například pokud vyvíjíte aplikace pro mobilní zařízení, je pravděpodobné, že v každém okamžiku bude existovat několik publikované verze vaší aplikace v vaše zákazníky. Nechcete k získání požadovaných výsledků telemetrická data z různých verzí promíchají. Takže získáš váš proces sestavení vytvořit nový prostředek pro každé sestavení.

> [!NOTE]
> Pokud chcete vytvořit sadu prostředků všechny ve stejnou dobu, vezměte v úvahu [vytváření prostředků pomocí šablony Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript pro vytvoření prostředku Application Insights
V tématu Specifikace příslušné rutiny:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Skript prostředí PowerShell*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Co dělat s iKey
Každý prostředek je identifikován jeho Instrumentační klíč (Instrumentační klíč). IKey jde o výstup skriptu pro vytváření prostředků. Váš skript buildu by měla poskytnout že vložený Instrumentační klíč do Application Insights SDK do vaší aplikace.

Existují dva způsoby, jak zpřístupnit iKey sady SDK:

* In [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Nebo v [inicializační kód](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvořit Application Insights a webových testů prostředků ze šablony](powershell.md)
* [Nastavte monitorování diagnostiky Azure pomocí Powershellu](powershell-azure-diagnostics.md) 
* [Nastavení výstrah pomocí Powershellu](powershell-alerts.md)

