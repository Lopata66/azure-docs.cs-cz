---
title: Analýza událostí Azure Service Fabric pomocí Log Analytics | Dokumentace Microsoftu
description: Další informace o vizualizaci a analýzu událostí pomocí Log Analytics pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: e8719b071bf2e836ed92fa4f6dcddc5f1865b320
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668790"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Události analýzy a vizualizace s využitím Log Analytics
 Log Analytics shromažďuje a analyzuje telemetrii z aplikace a služby hostované v cloudu a poskytuje analýzy nástroje, které vám umožní maximalizovat jejich dostupnost a výkon. Tento článek popisuje, jak spouštět dotazy v Log Analytics k získání přehledu a řešení potíží s co se děje ve vašem clusteru. Se podrobněji probírají následující běžné otázky:

* Jak řešit události stavu
* Jak poznám, kdy se uzel ocitne mimo provoz?
* Jak poznám, že pokud služby vaší aplikace mají spouštění nebo zastavování?

## <a name="overview-of-the-log-analytics-workspace"></a>Přehled pracovního prostoru Log Analytics

>[!NOTE] 
>Diagnostické úložiště je povolen ve výchozím nastavení při vytváření clusteru, stále musíte vytvořit pracovní prostor Log Analytics pro čtení z úložiště diagnostiky.

Log Analytics shromažďuje data ze spravovaných prostředků, včetně tabulku úložiště Azure nebo agenta a udržuje v centrálním úložišti. Data lze potom slouží pro analýzy, výstrahy a vizualizace nebo další exportu. Log Analytics podporuje události, údaje o výkonu nebo jiné vlastní data. Podívejte se na [postup pro konfiguraci rozšíření diagnostiky pro agregaci událostí](service-fabric-diagnostics-event-aggregation-wad.md) a [kroky k vytvoření pracovního prostoru Log Analytics pro čtení z události ve službě storage](service-fabric-diagnostics-oms-setup.md) k Ujistěte se, že se data přenášejí do Log Analytics .

Po přijetí dat pomocí Log Analytics, Azure nabízí několik *řešení pro správu* , která jsou připravená řešení nebo provozní řídicí panely se budou monitorovat příchozí data upravit tak, aby několik scénářů. Mezi ně patří *analýza služby Service Fabric* řešení a *kontejnery* řešení, které jsou dva nejdůležitější ty, které Diagnostika a monitorování clusterů Service Fabric. Tento článek popisuje, jak použít řešení analýza služby Service Fabric, který je vytvořen s pracovním prostorem.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení analýza služby Service Fabric

V [webu Azure Portal](https://portal.azure.com), přejděte do skupiny prostředků, ve které jste vytvořili řešení analýza služby Service Fabric.

Vyberte prostředek **ServiceFabric\<nameOfOMSWorkspace\>**.

V `Summary`, zobrazí se dlaždice ve formě grafu pro každou z řešení povolené, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** grafu a pokračujte v Service Fabric analytického řešení.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Následující obrázek ukazuje domovské stránce řešení analýza služby Service Fabric. Tato stránka Domovská stránka obsahuje zobrazení snímku, co se děje ve vašem clusteru.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Pokud jste povolili Diagnostika při vytváření clusteru, zobrazí se události pro 

* [Události clusteru Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Programovacího modelu Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě události Service Fabric připravené, můžete shromážděná podrobnější systémové události [aktualizací konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události Service Fabric, včetně akce na uzlech

Na stránce Analýza služby Service Fabric, klikněte na graf pro **události Service Fabric**.

![Service Fabric řešení provozní kanál](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Jakmile tady se zobrazí všechny události systému, které byly shromážděny. Pro srovnání jde z **WADServiceFabricSystemEventsTable** ve službě Azure Storage jsou z těchto tabulek příslušných účtu a podobně spolehlivé služby a objekty actor události se zobrazí vedle.
    
![Provozní kanál dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Můžete případně klikněte na lupu na levé straně a používat jazyk dotaz Kusto najít, co hledáte. Například pokud chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí používá pod se nacházejí v [provozní kanál události – referenční informace](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete zadávat dotazy na mnoho více polí, jako je například konkrétním uzlům (počítač) služby system (název úlohy).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Události služby Reliable Service Fabric zobrazení a objektu Actor

Na stránce Analýza služby Service Fabric, klikněte na graf pro **Reliable Services**.

![Řešení Service Fabric Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Zde můžete zobrazit události z modelu reliable services. Můžete zobrazit jednotlivé události pro při runasync služby je spuštěno a dokončeno, obvykle probíhá na nasazení a upgrade. 

![Reliable Services v dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Události Reliable actors můžou zobrazit podobným způsobem. Pro konfiguraci podrobnější událostí reliable actors, budete muset změnit `scheduledTransferKeywordFilter` v konfiguraci rozšíření diagnostiky (viz dole). Podrobnosti o hodnoty pro tyto [události reliable actors – referenční informace](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Je výkonný dotazovací jazyk Kusto. Další cenné dotaz, který můžete spustit je a zjistěte uzlů, které generují nejvíce událostem. Dotaz na snímku obrazovky níže zobrazí provozní události Service Fabric agregovat pomocí konkrétní službu a uzel.

![Dotaz události za uzel](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Další postup

* Pokud chcete povolit infrastruktury, například monitorování čítače výkonu, přejděte na [Přidání agenta Log Analytics](service-fabric-diagnostics-oms-agent.md). Agent shromažďuje čítače výkonu a přidá je do existujícího pracovního prostoru.
* Log Analytics nabízí místními clustery brány (dopředu proxy server HTTP), který slouží k odesílání dat do Log Analytics. Další informace o, že v [počítače bez připojení k Internetu připojení k Log Analytics pomocí Log Analytics gateway](../azure-monitor/platform/gateway.md).
* Konfigurace [automatické upozorňování](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást Log Analytics.
* Získejte podrobnější přehled o Log Analytics a navíc nabízejí, přečtěte si [co je služba Log Analytics?](../operations-management-suite/operations-management-suite-overview.md).
