---
title: Zdroje dat v Azure Monitor | Microsoft Docs
description: Popisuje data dostupná ke sledování stavu a výkonu vašich prostředků Azure a aplikací, které jsou na nich spuštěné.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: b2ec9fd70d1eb64c5968de0312941bfbc98d3033
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670521"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Zdroje dat monitorování pro Azure Monitor
Azure Monitor je založený na [běžné platformě dat pro monitorování](data-platform.md) , která zahrnuje [protokoly](data-platform-logs.md) a [metriky](data-platform-metrics.md). Shromažďování dat na této platformě umožňuje analyzovat data z několika prostředků společně pomocí běžné sady nástrojů v Azure Monitor. Data monitorování se můžou posílat i na další místa pro podporu určitých scénářů a některé prostředky můžou zapisovat do jiných umístění, než je budete moct shromažďovat do protokolů nebo metrik.

Tento článek popisuje různé zdroje dat monitorování shromažďovaných nástrojem Azure Monitor kromě dat monitorování vytvořených prostředky Azure. Odkazy jsou k dispozici pro podrobné informace o konfiguraci potřebné ke shromažďování těchto dat do různých umístění.

## <a name="application-tiers"></a>Úrovně aplikace

Zdroje dat monitorování z aplikací Azure je možné uspořádat do vrstev, nejvyšší úrovně samotné aplikace a nižších vrstev, které jsou součástí platformy Azure. Metoda přístupu k datům z jednotlivých vrstev se liší. Aplikační vrstvy jsou shrnuté v následující tabulce a zdroje dat monitorování v jednotlivých vrstvách jsou uvedeny v následujících částech. V tématu [monitorování umístění dat v Azure](data-locations.md) najdete popis každého umístění dat a způsob, jakým můžete získat přístup k datům.


![Úrovně monitorování](../media/overview/overview.png)


### <a name="azure"></a>Azure
Následující tabulka stručně popisuje aplikační vrstvy, které jsou specifické pro Azure. Následující odkaz vám podrobnější informace najdete v následujících částech.

| Vrstva | Popis | Metoda Collection |
|:---|:---|:---|
| [Tenant Azure](#azure-tenant) | Data o provozu služeb Azure na úrovni tenanta, jako je například Azure Active Directory. | Zobrazte data AAD na portálu nebo nakonfigurujte kolekci tak, aby se Azure Monitor pomocí nastavení diagnostiky tenanta. |
| [Předplatné Azure](#azure-subscription) | Data související se stavem a správou služeb pro více prostředků ve vašem předplatném Azure, například Správce prostředků a Service Health. | Zobrazte na portálu nebo nakonfigurujte kolekci tak, aby se Azure Monitor pomocí profilu protokolu. |
| [Prostředky Azure](#azure-resources) |  Údaje o provozu a výkonu každého prostředku Azure. | Automaticky shromažďované metriky, zobrazit v Průzkumník metrik.<br>Nakonfigurujte nastavení diagnostiky pro shromažďování protokolů v Azure Monitor.<br>Řešení a přehledy, které jsou k dispozici pro podrobnější monitorování konkrétních typů prostředků. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, jiný Cloud nebo místní 
Následující tabulka stručně popisuje aplikační vrstvy, které se můžou nacházet v Azure, jiném cloudu nebo místním prostředí. Následující odkaz vám podrobnější informace najdete v následujících částech.

| Vrstva | Popis | Metoda Collection |
|:---|:---|:---|
| [Operační systém (host)](#operating-system-guest) | Data o operačním systému výpočetních prostředků. | Nainstalujte agenta Log Analytics ke shromáždění zdrojů dat klientů do Azure Monitor a agenta závislostí ke shromáždění závislostí, které podporují Azure Monitor pro virtuální počítače.<br>Pro virtuální počítače Azure nainstalujte diagnostické rozšíření Azure, které shromáždí protokoly a metriky do Azure Monitor. |
| [Kód aplikace](#application-code) | Údaje o výkonu a funkcích skutečné aplikace a kódu, včetně trasování výkonu, protokolů aplikací a telemetrie uživatelů. | Instrumentujte kód pro shromažďování dat do Application Insights. |
| [Vlastní zdroje](#custom-sources) | Data z externích služeb nebo jiných komponent nebo zařízení. | Shromažďovat data protokolu nebo metrik do Azure Monitor z libovolného klienta REST. |

## <a name="azure-tenant"></a>Tenant Azure
Telemetrie související s vaším klientem Azure se shromažďují ze služeb, jako je Azure Active Directory.

![Kolekce tenantů Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Protokoly auditu Azure Active Directory
[Vytváření sestav Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) obsahuje historii přihlašovacích aktivit a revizní záznam změn provedených v rámci určitého tenanta. 

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly Azure AD, které se budou shromažďovat v Azure Monitor a analyzovat je s ostatními daty monitorování. | [Integrace protokolů služby Azure AD s protokoly Azure Monitor (Preview)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportujte protokoly služby Azure AD, které se Azure Storage k archivaci. | [Kurz: archivace protokolů služby Azure AD do účtu úložiště Azure (Preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamování protokolů Azure AD do dalších umístění pomocí Event Hubs. | [Kurz: streamování Azure Active Directory protokolů do centra událostí Azure (Preview)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Předplatné Azure
Telemetrie související se stavem a provozem vašeho předplatného Azure.

![Předplatné Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Protokol aktivit Azure 
[Protokol aktivit Azure](platform-logs-overview.md) zahrnuje záznamy o stavu služeb společně se záznamy o všech změnách konfigurace provedených v prostředcích v předplatném Azure. Protokol aktivit je dostupný pro všechny prostředky Azure a představuje jejich _externí_ zobrazení.

| Cíl | Popis | Referenční informace |
|:---|:---|
| Protokol aktivit | Protokol aktivit se shromáždí do vlastního úložiště dat, které můžete zobrazit v nabídce Azure Monitor, nebo můžete použít k vytvoření výstrah protokolu aktivit. | [Dotazování protokolu aktivit v Azure Portal](activity-log-view.md#azure-portal) |
| Protokoly služby Azure Monitor | Nakonfigurujte protokoly Azure Monitor pro shromáždění protokolu aktivit, abyste ho mohli analyzovat s ostatními daty monitorování. | [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exportujte protokol aktivit do Azure Storage k archivaci. | [Protokol aktivit archivu](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Streamování protokolu aktivit do jiných umístění pomocí Event Hubs | [Streamování protokolu aktivit do centra událostí](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) poskytuje informace o stavu služeb Azure ve vašem předplatném, na kterém závisí vaše aplikace a prostředky.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokol aktivit<br>Protokoly služby Azure Monitor | Service Health záznamy se ukládají do protokolu aktivit Azure, takže je můžete zobrazit v Azure Portal nebo provádět jakékoli jiné aktivity, které můžete s protokolem aktivit provádět. | [Zobrazení oznámení o stavu služby s využitím webu Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Prostředky Azure
Metriky a protokoly prostředků poskytují informace o _interní_ operaci prostředků Azure. Jsou k dispozici pro většinu služeb Azure a monitorovací řešení a přehledy shromažďují další data pro konkrétní služby.

![Kolekce prostředků Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Metriky platformy 
Většina služeb Azure bude odesílat [metriky platforem](data-platform-metrics.md) , které odpovídají jejich výkonu a provozu přímo do databáze metrik. Konkrétní [metriky se budou lišit pro každý typ prostředku](metrics-supported.md). 

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Azure Monitor metriky | Metriky platformy budou zapisovat do databáze metrik Azure Monitor bez konfigurace. Přístup k metrikám platformy z Průzkumník metrik.  | [Začínáme s Průzkumníkem metrik Azure](metrics-getting-started.md)<br>[Podporované metriky s Azure Monitor](metrics-supported.md) |
| Protokoly služby Azure Monitor | Kopírování metrik platforem do protokolů pro účely trendů a dalších analýz pomocí Log Analytics. | [Diagnostika Azure přímo na Log Analytics](resource-logs-collect-workspace.md) |
| Event Hubs | Streamujte metriky do jiných umístění pomocí Event Hubs. |[Streamování dat monitorování Azure do centra událostí pro využití externím nástrojem](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Protokoly prostředků
[Protokoly prostředků](platform-logs-overview.md) poskytují přehled o _interní_ operaci prostředku Azure.  Protokoly prostředků se vytvářejí automaticky, ale musíte vytvořit nastavení diagnostiky, abyste určili cíl pro shromažďování pro jednotlivé prostředky.

Požadavky na konfiguraci a obsah protokolů prostředků se liší podle typu prostředku a ne u všech služeb, které je ještě nevytvořily. Podrobnosti o každé službě a odkazy na podrobné postupy konfigurace najdete v tématu [podporované služby, schémata a kategorie pro protokoly prostředků Azure](diagnostic-logs-schema.md) . Pokud tato služba není uvedená v tomto článku, tato služba aktuálně nevytváří protokoly prostředků.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Odešlete protokoly prostředků, aby se protokoly Azure Monitor pro analýzu s dalšími shromážděnými daty protokolů. | [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure Monitor](resource-logs-collect-storage.md) |
| Úložiště | Odešlete protokoly prostředků, které se Azure Storage k archivaci. | [Archivace protokolů prostředků Azure](resource-logs-collect-workspace.md) |
| Event Hubs | Streamujte protokoly prostředků do jiných umístění pomocí Event Hubs. |[Streamování protokolů prostředků Azure do centra událostí](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operační systém (host)
Výpočetní prostředky v Azure, v jiných cloudech a místně mají hostovaný operační systém, který monitoruje. Při instalaci jednoho nebo více agentů můžete shromáždit telemetrii z hosta do Azure Monitor a analyzovat ji pomocí stejných nástrojů pro monitorování, jako jsou samotné služby Azure.

![Shromažďování prostředků Azure COMPUTE](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
Povolení rozšíření Azure Diagnostics pro virtuální počítače Azure umožňuje shromažďovat protokoly a metriky z hostovaného operačního systému výpočetních prostředků Azure, včetně webových a pracovních rolí Azure Cloud Service (Classic), Virtual Machines, virtuálního počítače. Sady škálování a Service Fabric.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Úložiště | Rozšíření Azure Diagnostics se vždycky zapisuje do účtu Azure Storage. | [Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](diagnostics-extension-windows-install.md)<br>[Monitorování metrik a protokolů pomocí diagnostického rozšíření systému Linux](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor metriky | Když nakonfigurujete diagnostické rozšíření pro shromažďování čítačů výkonu, budou zapsány do databáze Azure Monitor metrik. | [Odeslání metriky hostovaného operačního systému do úložiště metriky Azure Monitor pomocí šablony Správce prostředků pro virtuální počítač s Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Nakonfigurujte diagnostické rozšíření pro streamování dat do jiných umístění pomocí Event Hubs.  | [Streamování Azure Diagnostics dat pomocí Event Hubs](diagnostics-extension-stream-event-hubs.md)<br>[Monitorování metrik a protokolů pomocí diagnostického rozšíření systému Linux](../../virtual-machines/extensions/diagnostics-linux.md) |
| Protokoly Application Insights | Shromažďovat protokoly a čítače výkonu z výpočetního prostředku podporující vaši aplikaci, která se bude analyzovat pomocí dalších aplikačních dat | [Odeslat cloudovou službu, virtuální počítač nebo Service Fabric diagnostická data do Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agent Log Analytics 
Nainstalujte agenta Log Analytics pro komplexní monitorování a správu virtuálních počítačů se systémem Windows nebo Linux. Virtuální počítač může běžet v Azure, jiném cloudu nebo v místním prostředí.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Agent Log Analytics se připojuje k Azure Monitor přímo nebo prostřednictvím System Center Operations Manager a umožňuje shromažďovat data ze zdrojů dat, které nakonfigurujete, nebo z monitorování řešení, která poskytují další přehledy o aplikacích. běží na virtuálním počítači. | [Zdroje dat agenta v Azure Monitor](agent-data-sources.md)<br>[Připojit Operations Manager k Azure Monitor](om-agents.md) |
| Úložiště virtuálního počítače | Azure Monitor pro virtuální počítače používá agenta Log Analytics k ukládání informací o stavu stavu ve vlastním umístění. Další informace najdete v další části.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače 
[Azure monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené prostředí pro monitorování virtuálních počítačů, které poskytují funkce nad rámec základních Azure monitor funkcí, včetně stavu služby a stavu virtuálního počítače. Vyžaduje Dependency Agent na virtuálních počítačích s Windows a Linux, které se integrují s agentem Log Analytics ke shromažďování zjištěných dat o procesech spuštěných na virtuálním počítači a závislostech externích procesů.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data o procesech a závislostech na agentovi. | [Použití mapy Azure Monitor pro virtuální počítače (Preview) k porozumění součástem aplikace](../insights/vminsights-maps.md) |
| Úložiště virtuálního počítače | Azure Monitor pro virtuální počítače používá agenta Log Analytics k ukládání informací o stavu stavu ve vlastním umístění. To je dostupné jenom pro Azure Monitor pro virtuální počítače v Azure Portal kromě [REST API stavu prostředků Azure](/rest/api/resourcehealth/). | [Pochopení stavu virtuálních počítačů Azure](../insights/vminsights-health.md)<br>[REST API stavu prostředků Azure](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Kód aplikace
Podrobné monitorování aplikací v Azure Monitor se provádí s [Application Insights](https://docs.microsoft.com/azure/application-insights/) , která shromažďuje data z aplikací běžících na různých platformách. Aplikace může běžet v Azure, jiném cloudu nebo v místním prostředí.

![Shromažďování dat aplikací](media/data-sources/applications.png)


### <a name="application-data"></a>Data aplikací
Pokud povolíte Application Insights pro aplikaci instalací balíčku instrumentace, budou shromažďovány metriky a protokoly týkající se výkonu a provozu aplikace. Application Insights ukládá data, která shromažďuje, do stejné Azure Monitor datové platformy, kterou používají jiné zdroje dat. Obsahuje obsáhlé nástroje pro analýzu těchto dat, ale můžete je také analyzovat pomocí dat z jiných zdrojů pomocí nástrojů, jako jsou Průzkumník metrik a Log Analytics.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Provozní data o vaší aplikaci, včetně zobrazení stránky, žádostí o aplikace, výjimek a trasování. | [Analyzovat data protokolu v Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informace o závislostech mezi součástmi aplikace pro podporu mapy aplikace a korelace telemetrie. | [Korelace telemetrie v Application Insights](../app/correlation.md) <br> [Mapa aplikace](../app/app-map.md) |
|            | Výsledky testů dostupnosti, které testují dostupnost a rychlost odezvy vaší aplikace z různých míst na veřejném Internetu. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
| Azure Monitor metriky | Application Insights shromažďuje metriky, které popisují výkon a provoz aplikace, spolu s vlastními metrikami, které definujete v aplikaci, do databáze Azure Monitor metrik. | [Neagregované metriky založené na protokolu a Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights rozhraní API pro vlastní události a metriky](../app/api-custom-events-metrics.md) |
| Azure Storage | Odešle data aplikace do Azure Storage k archivaci. | [Export telemetrie z Application Insights](../app/export-telemetry.md) |
|            | Podrobnosti testů dostupnosti jsou uloženy v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu. Výsledky testů dostupnosti jsou uloženy v protokolech Azure Monitor. | [Sledování dostupnosti a odezvy libovolných webů](../app/monitor-web-app-availability.md) |
|            | Data trasování profileru se ukládají v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu.  | [Profilace produkčních aplikací v Azure pomocí Application Insights](../app/profiler-overview.md) 
|            | Data snímku ladění, která jsou zachycena pro podmnožinu výjimek, jsou uložena v Azure Storage. Pomocí Application Insights v Azure Portal si můžete stáhnout pro místní analýzu.  | [Jak fungují snímky](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitorování řešení a přehledů
[Monitorování řešení](../insights/solutions.md) a [přehledů](../insights/insights-overview.md) shromažďuje data, která poskytují další přehledy o provozu konkrétní služby nebo aplikace. Můžou řešit prostředky v různých aplikačních vrstvách a dokonce i na více úrovních.

### <a name="monitoring-solutions"></a>Řešení monitorování

| Cíl | Popis | Referenční informace
|:---|:---|:---|
| Protokoly služby Azure Monitor | Řešení monitorování shromažďují data do protokolů Azure Monitor, kde je lze analyzovat pomocí dotazovacího jazyka nebo [zobrazení](view-designer.md) , která jsou obvykle obsažena v řešení. | [Podrobnosti shromažďování dat pro řešení monitorování v Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
[Azure monitor for Containers](../insights/container-insights-overview.md) poskytuje přizpůsobené možnosti monitorování pro [Azure KUBERNETES Service (AKS)](/azure/aks/). Shromažďuje další údaje o těchto prostředcích popsaných v následující tabulce.

| Cíl | Popis | Referenční informace |
|:---|:---|:---|
| Protokoly služby Azure Monitor | Ukládá data monitorování pro AKS, včetně inventáře, protokolů a událostí. Data metriky se také ukládají v protokolech, aby bylo možné využít její funkci analýzy na portálu. | [Porozumění výkonu clusteru AKS pomocí služby Azure Monitor pro kontejnery](../insights/container-insights-analyze.md) |
| Azure Monitor metriky | Data metriky se ukládají v databázi metrik pro řízení vizualizace a výstrah. | [Zobrazit metriky kontejneru v Průzkumníkovi metrik](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Poskytuje přímý přístup k protokolům kontejnerů služby Azure Kubernetes (stdout/stderr), událostem a pod metrikám na portálu. | [Jak zobrazit protokoly Kubernetes, události a metriky pod v reálném čase](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure monitor pro virtuální počítače](../insights/vminsights-overview.md) poskytuje přizpůsobené prostředí pro monitorování virtuálních počítačů. Popis dat shromažďovaných službou Azure Monitor pro virtuální počítače je obsažený v části [operační systém (host)](#operating-system-guest) výše.

## <a name="custom-sources"></a>Vlastní zdroje
Kromě standardních úrovní aplikace může být nutné monitorovat další prostředky, které mají telemetrii, které nelze shromáždit s ostatními zdroji dat. Pro tyto prostředky zapište tato data do metriky nebo protokolů pomocí rozhraní Azure Monitor API.

![Vlastní kolekce](media/data-sources/custom.png)

| Cíl | Metoda | Popis | Referenční informace |
|:---|:---|:---|:---|
| Protokoly služby Azure Monitor | Rozhraní API kolekce dat | Shromážděte data protokolu z libovolného klienta REST a uložte je do pracovního prostoru Log Analytics. | [Odeslání dat protokolu do Azure Monitor pomocí rozhraní API kolekce dat HTTP (Public Preview)](data-collector-api.md) |
| Azure Monitor metriky | Rozhraní API pro vlastní metriky | Shromážděte data metrik z libovolného klienta REST a uložte je do databáze Azure Monitorch metrik. | [Odesílat vlastní metriky pro prostředek Azure do úložiště metrik Azure Monitor pomocí REST API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Další služby
Další služby v Azure zapisují data na Azure Monitor datovou platformu. Díky tomu můžete analyzovat data shromážděná těmito službami s daty shromažďovanými nástrojem Azure Monitor a využívat stejné nástroje pro analýzu a vizualizaci.

| Service | Cíl | Popis | Referenční informace |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Protokoly služby Azure Monitor | Azure Security Center ukládá data zabezpečení shromažďovaná v pracovním prostoru Log Analytics, který umožňuje jejich analýzu s dalšími daty protokolů shromážděnými Azure Monitor.  | [Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Sentinel Azure](/azure/sentinel/) | Protokoly služby Azure Monitor | Služba Azure Sentinel ukládá data, která shromažďuje z různých zdrojů dat v pracovním prostoru Log Analytics, což umožňuje jejich analýzu s dalšími daty protokolů shromážděnými pomocí Azure Monitor.  | [Připojení zdrojů dat](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [typech dat monitorování shromažďovaných nástrojem Azure monitor](data-platform.md) a o tom, jak tato data zobrazit a analyzovat.
- Seznamte se s [různými umístěními, kde prostředky Azure ukládají data](data-locations.md) a jak k nim máte přístup. 
