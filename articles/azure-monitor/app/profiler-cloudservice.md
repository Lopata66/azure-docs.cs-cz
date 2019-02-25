---
title: Profilování živých Azure Cloud Services službou Application Insights | Dokumentace Microsoftu
description: Povolte Application Insights Profiler pro Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 32604c06c6a4325f7ae6cb45930de902a1366480
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750323"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilování živých Azure Cloud Services službou Application Insights

Můžete také nasadit Application Insights Profiler za tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler je nainstalován pomocí rozšíření Azure Diagnostics. Stačí ke konfiguraci diagnostiky Azure pro instalaci Profiler a profily posílala vašemu prostředku Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Povolit Profiler pro Azure Cloud Services
1. Ujistěte se, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Stačí potvrdit, že *ServiceConfiguration.\*.cscfg* soubory mají `osFamily` hodnotu "5" nebo novější.

1. Přidat [Application Insights SDK pro Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

  >**V profileru, který se dodává v nejnovější verzi WAD pro Cloud Services je chyba.** Chcete-li použít profiler s cloudovou službou, podporuje pouze sady Application Insights SDK verzi 2.7.2. Pokud používáte novější verzi sady Application Insights SDK, budete muset přejít zpět na 2.7.2, aby bylo možné využívat profiler.

1. Požadavky na sledování pomocí Application Insights:

    * V případě webových rolí technologie ASP.NET Application Insights můžete sledovat, požadavky automaticky.

    * V případě rolí pracovních procesů [přidat kód pro sledování požadavků](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurovat rozšíření Azure Diagnostics pro umožnění Profiler následujícím způsobem:

    a. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* souboru pro vaši roli v aplikaci, jak je znázorněno zde:  

      ![Umístění souboru konfigurace diagnostiky](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Pokud nemůže najít soubor, přečtěte si téma [nastavení diagnostiky pro Azure Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Pokud *diagnostics.wadcfgx* soubor obsahuje také další jímky typu ApplicationInsights, musí odpovídat všechny tři z následujících klíčů instrumentace:  
    > * Klíč, který používá vaše aplikace. 
    > * Klíč, který je používán ApplicationInsights jímky. 
    > * Klíč, který je používán ApplicationInsightsProfiler jímky. 
    >
    > Můžete najít hodnotu klíče skutečné instrumentace, která používá `ApplicationInsights` jímky *ServiceConfiguration.\*.cscfg* soubory. 
    > Po vydání Visual Studio 15.5 Azure SDK pouze Instrumentační klíče, které používají aplikace a datovou sadu jímky ApplicationInsightsProfiler muset vzájemně odpovídaly.

1. Nasazení služby s novou konfigurací Diagnostics a Application Insights Profiler je konfigurován pro běh ve službě service.
 
## <a name="next-steps"></a>Další postup

* Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](monitor-web-app-availability.md)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
* Zobrazit [trasy Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
* Řešení potíží Profiler, najdete v článku [Profiler řešení potíží s](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
