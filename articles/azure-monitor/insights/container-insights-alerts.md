---
title: Vytváření výstrah výkonu pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak pomocí Azure Monitor pro kontejnery vytvořit vlastní výstrahy na základě dotazů protokolu pro paměť a využití procesoru.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2019
ms.author: magoedte
ms.openlocfilehash: 2b1ee0e56b5a133e65a25b5d9af645f351d039c0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722685"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Nastavení výstrah pro problémy s výkonem v Azure Monitor pro kontejnery
Azure Monitor pro kontejnery monitorují výkon zatížení kontejnerů, které jsou nasazené do Azure Container Instances nebo spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes (AKS).

Tento článek popisuje, jak povolit výstrahy v následujících situacích:

- Pokud využití procesoru nebo paměti na uzlech clusteru překročí prahovou hodnotu
- Pokud využití procesoru nebo paměti na jakémkoli kontejneru v rámci kontroleru překročí prahovou hodnotu v porovnání s limitem nastaveným na odpovídajícím prostředku
- Počet uzlů stavu pro stav
- Počet neúspěšných, nevyřízených, neznámých, *spuštěných*nebo *úspěšných* fází pod sebou
- Pokud volné místo na disku na uzlech clusteru překročí prahovou hodnotu 

Pokud chcete upozornit na vysoké využití procesoru nebo paměti nebo je málo volného místa na disku v uzlech clusteru, použijte dotazy, které jsou k dispozici k vytvoření výstrahy metriky nebo upozornění na měření metriky. Výstrahy metriky mají nižší latenci než výstrahy protokolu. Výstrahy protokolu ale poskytují pokročilé dotazy a větší sofistikovanější. Dotazy protokolu výstrahy protokolují data a času pomocí operátoru *Now* a vrátí jednu hodinu. (Azure Monitor pro kontejnery ukládají všechna data ve formátu koordinovaného světového času (UTC).)

Pokud nejste obeznámeni s výstrahami Azure Monitor, přečtěte si téma [Přehled výstrah v Microsoft Azure](../platform/alerts-overview.md) před tím, než začnete. Další informace o výstrahách, které používají dotazy protokolu, najdete [v tématu protokolování výstrah v Azure monitor](../platform/alerts-unified-log.md). Další informace o upozorněních metrik najdete [v tématu výstrahy metrik v Azure monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Dotazy na hledání protokolu využití prostředků
Dotazy v této části podporují jednotlivé scénáře upozorňování. Používají se v kroku 7 části [Vytvoření výstrahy](#create-an-alert-rule) tohoto článku.

Následující dotaz vypočítá průměrné využití procesoru jako průměr využití procesoru členskými uzly každou minutu.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Následující dotaz vypočítá průměrné využití paměti jako průměr využití paměti členských uzlů každou minutu.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Následující dotazy používají zástupné hodnoty \<, které jsou vaší clusterem a \<řadičem v názvu clusteru > a >. Nahraďte je hodnotami specifickými pro vaše prostředí při nastavování výstrah.

Následující dotaz vypočítá průměrné využití procesoru u všech kontejnerů v řadiči jako průměrnou hodnotu využití procesoru každé instance kontejneru v řadiči v každé minutě. Měření je procento limitu nastaveného pro kontejner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Následující dotaz vypočítá průměrné využití paměti u všech kontejnerů v řadiči jako průměr využití paměti každé instance kontejneru v kontroleru každou minutu. Měření je procento limitu nastaveného pro kontejner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Následující dotaz vrátí všechny uzly a počty, které mají stav připraveno a nepříliš *stupňů*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Následující dotaz vrátí hodnotu pod počty fází na základě všech fází: *Selhání*, *čeká*, *Neznámý*, *spuštěný*nebo *úspěšný*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Chcete-li upozornit na některé fáze pod,jako je například nevyřízená, neúspěšná nebo *neznámá*, upravte poslední řádek dotazu. Například pro upozornění na *FailedCount* použití: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Následující dotaz vrátí disky uzlů clusteru, které překračují 90% volného místa. Chcete-li získat ID clusteru, nejprve spusťte následující dotaz a zkopírujte hodnotu z `ClusterId` vlastnosti:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Pomocí těchto kroků vytvořte v Azure Monitor upozornění protokolu pomocí jednoho z pravidel hledání protokolu, která byla k dispozici dříve.  

>[!NOTE]
>Následující postup vytvoření pravidla výstrahy pro využití prostředků kontejneru vyžaduje, abyste přešli na nové rozhraní API upozornění protokolu, jak je popsáno v tématu [předvoleb rozhraní API pro protokolování výstrah](../platform/alerts-log-api-switch.md).
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V podokně na levé straně vyberte **monitor** . Včásti přehledy vyberte **kontejnery**.
3. Na kartě **monitorované clustery** vyberte cluster ze seznamu.
4. V podokně na levé straně **monitorování**vyberte **protokoly** a otevřete stránku Azure monitor protokoly. Tato stránka slouží k zápisu a provádění dotazů Azure Log Analytics.
5. Na stránce **protokoly** vyberte **+ nové pravidlo výstrahy**.
6. V části **Podmínka** vyberte pokaždé, **když je \<hledání vlastního protokolu > logiku nedefinované** předem definovaný vlastní podmínka protokolu. Vlastní typ signálu **hledání vlastního protokolu** se vybere automaticky, protože vytváříme pravidlo upozornění přímo ze stránky Azure monitor protokoly.  
7. Vložte jeden z [dotazů](#resource-utilization-log-search-queries) , které byly zadány dříve, do pole **vyhledávacího dotazu** .
8. Výstrahy nakonfigurujte následujícím způsobem:

    1. V rozevíracím seznamu **Na základě** vyberte **Měření metriky**. Měření metriky vytvoří výstrahu pro každý objekt v dotazu, který má hodnotu nad naši zadanou prahovou hodnotou.
    1. Jako **podmínku**vyberte **větší než**a jako počáteční **hodnotu prahové hodnoty** počátečního směrného plánu pro výstrahy využití procesoru a paměti zadejte **75** . Pro upozornění na nedostatek místa na disku zadejte **90**. Nebo zadejte jinou hodnotu, která vyhovuje vašim kritériím.
    1. V části **Výstraha aktivační události na základě** oddílu vyberte **po sobě jdoucí porušení**. V rozevíracím seznamu vyberte **větší než**a zadejte **2**.
    1. Chcete-li nakonfigurovat výstrahu pro využití procesoru nebo paměti kontejneru, vyberte v části **agregace zapnuto**možnost **ContainerName**. Pokud chcete konfigurovat pro upozornění na nízký disk uzlu clusteru, vyberte **ClusterId**.
    1. V části **vyhodnocováno na základě** oddílu nastavte hodnotu **perioda** na **60 minut**. Pravidlo se spustí každých 5 minut a vrátí záznamy, které byly vytvořeny během poslední hodiny od aktuálního času. Nastavení časového období pro velké účty oken za účelem potenciální latence dat. Také zajišťuje, že dotaz vrátí data, aby se předešlo nezápornému falešně pozitivnímu upozornění, že výstraha nebude nikdy aktivována.

9. Vyberte **Hotovo** a dokončete pravidlo výstrahy.
10. Do pole **název pravidla výstrahy** zadejte název. Zadejte **Popis** , který poskytuje podrobnosti o výstraze. A vyberte příslušnou úroveň závažnosti z poskytnutých možností.
11. Chcete-li ihned aktivovat pravidlo výstrahy, přijměte výchozí hodnotu pro **pravidlo Povolit při vytváření**.
12. Vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Tento krok zajistí, aby se při každém spuštění výstrahy provedly stejné akce. Nakonfigurujte na základě toho, jak váš tým IT nebo DevOps Operations spravuje incidenty.
13. Vyberte **vytvořit pravidlo výstrahy** a dokončete pravidlo výstrahy. Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další postup

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.
- Další informace o Azure Monitor a o tom, jak monitorovat další aspekty clusteru AKS, najdete v tématu [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
