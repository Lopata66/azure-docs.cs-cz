---
title: Přehled služby Azure agentů monitorování | Dokumentace Microsoftu
description: Tento článek obsahuje podrobný přehled o dostupných agentů služby Azure, které podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridního prostředí.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 12eea032c37c8d737ae004d622b72536195c4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977569"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Přehled služby Azure agentů monitorování 
Microsoft Azure poskytuje několik způsobů, jak shromažďovat různé typy dat z virtuálních počítačů se systémem Microsoft Windows a Linux hostované v Azure, vaše datové centrum nebo jiných poskytovatelů cloudových služeb. Jsou tři typy agenty, které jsou k dispozici pro monitorování virtuálního počítače:

* Rozšíření Azure Diagnostics
* Agenta log Analytics pro systémy Linux a Windows
* Agent závislostí

Tento článek popisuje rozdíly mezi nimi a jejich funkce v pořadí, abyste mohli určit, která bude podporovat správy služeb IT nebo obecné požadavků na monitorování.  

## <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
[Rozšíření Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md) (označované jako rozšíření Windows Azure diagnostiky (WAD) nebo Linux Azure Diagnostic (LAD)), který byl poskytnut pro Azure Cloud Services od stala všeobecně dostupná v 2010 je agent, který dodává simple shromažďování diagnostických dat z výpočetním prostředkem Azure jako virtuální počítač a zachována do služby Azure storage. Jakmile se v úložišti, budete chtít zobrazit s jedním z několika dostupných nástrojů, jako například [Průzkumníku serveru v sadě Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) a [Průzkumníka služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Můžete shromažďovat:

* Předdefinované sady čítačů výkonu operačního systému a protokoly událostí nebo je můžete určit, které se mají shromažďovat. 
* Všechny požadavky a/nebo neúspěšné požadavky na webový server služby IIS
* Výstupní protokoly trasování aplikace .NET
* Trasování událostí pro Windows (ETW) 
* Shromažďovat události protokolu syslog  
* Výpisy stavu systému 

Agenta diagnostiky Azure byste měli použít, když chcete:

* Archivovat protokoly a metriky do služby Azure storage
* Data monitorování integraci s nástroji třetích stran. Tyto nástroje používat různými metodami, včetně dotazování na účet úložiště předá [Event Hubs](../../event-hubs/event-hubs-about.md), nebo dotazování pomocí [rozhraním API REST pro monitorování Azure](../../azure-monitor/platform/rest-api-walkthrough.md)
* Nahrání dat do služby Azure Monitor k vytvoření grafů metrik na webu Azure Portal nebo vytvořit téměř v reálném čase [upozornění na metriku](../../azure-monitor/platform/alerts-metric-overview.md). 
* Škálovací sady virtuálních počítačů automatického škálování a cloudové služby Classic podle metrik operačního systému hosta.
* Prozkoumat problémy spouštění virtuálních počítačů s [Diagnostika spouštění](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Porozumět, jaký výkon vašich aplikací a proaktivně identifikuje problémy neovlivňují pomocí [Application Insights](../../azure-monitor/overview.md).
* Konfigurace služby Azure Monitor importovat metrik a protokolů data shromážděná ze služby Cloud Services, klasické virtuální počítače a služby infrastruktury uzly uložených v účtu služby Azure storage.

## <a name="log-analytics-agent"></a>Agenta log Analytics
U rozšířené monitorování, které je potřeba shromažďovat více než metriky a podmnožinu protokolů, se vyžaduje agenta Log Analytics pro Windows (také označované jako Microsoft Monitoring Agent (MMA)) a Linux. Agenta Log Analytics byla vyvinuta pro komplexní správu napříč místní fyzické a virtuální počítače, System Center Operations Manager monitoruje počítače a hostované virtuální počítače v jiných cloudech. Agenti Windows a Linux se připojit k pracovnímu prostoru Log Analytics ve službě Azure Monitor ke shromažďování dat monitorování na základě řešení i vlastní zdroje dat, které nakonfigurujete.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Agenta Log Analytics byste měli použít, když chcete:

* Shromažďovat data z nejrůznějších zdrojů, jak v rámci Azure, dalších poskytovatelů cloudových a místních prostředků. 
* Použijte jednu z Azure monitoru, například monitorování řešení [monitorování Azure pro virtuální počítače](../insights/vminsights-overview.md), [monitorování Azure pro kontejnery](../insights/container-insights-overview.md)atd.  
* Použijte jednu z jiných služeb Azure pro správu, jako [Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md)atd.

Dříve byly spojeny několik služeb Azure jako *Operations Management Suite*, a v důsledku agenta Log Analytics je sdílen mezi službami, včetně Azure Security Center a Azure Automation.  To zahrnuje úplnou sadu funkcí, které nabízejí, poskytování komplexní správu virtuálních počítačů Azure prostřednictvím jejich životního cyklu.  Některé příklady jsou:

* [Azure Automation Update management](../../automation/automation-update-management.md) aktualizací operačního systému.
* [Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) udržovat konzistentní konfiguraci stavu.
* Sledovat změny konfigurace s [Azure Automation Change Tracking a Inventory](../../automation/change-tracking.md).
* Služby Azure, jako [Application Insights](https://docs.microsoft.com/azure/application-insights/) a [Azure Security Center](https://docs.microsoft.com/azure/security-center/), která nativně ukládají data přímo do Log Analytics.  

## <a name="dependency-agent"></a>Agent závislostí
Agent závislostí vyvinula jako součást řešení Service Map, který se původně vyvinutý microsoftem. [Řešení Service Map](../insights/service-map.md) a [monitorování Azure pro virtuální počítače](../insights/vminsights-overview.md) vyžaduje, aby Agent závislostí ve Windows a Linuxu virtuálních počítačů a integruje do agenta Log Analytics ke shromažďování zjištěná data o procesech spuštěných na virtuální počítače a procesu externí závislosti. Tato data ukládá v pracovním prostoru Log Analytics a vizualizuje zjištěných vzájemně propojených součástí.

Možná bude nutné určitou kombinaci těchto agentů monitorování virtuálního počítače. Agenty je možné nainstalovat vedle sebe jako rozšíření Azure, ale v Linuxu, agenta Log Analytics *musí* nainstalovat první, jinak se instalace nezdaří. 

## <a name="next-steps"></a>Další postup

- Zobrazit [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) zkontrolovat požadavky a podporované metody nasazení agenta do počítače hostované v Azure, do vašeho datového centra nebo jiné cloudové prostředí.

