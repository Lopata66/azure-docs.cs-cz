---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65404921"
---
Můžete využít výhod množství příležitostí k monitorování virtuálních počítačů shromažďování, zobrazení a analýza diagnostických a protokolovat data. Provést jednoduché [monitorování](../articles/azure-monitor/overview.md) vašeho virtuálního počítače, můžete použít obrazovka s přehledem pro virtuální počítač na webu Azure Portal. Můžete použít [rozšíření](../articles/virtual-machines/windows/extensions-features.md) ke konfiguraci diagnostiky na virtuálních počítačích shromažďovat další data metriky. Můžete použít také pokročilejší možnosti monitorování, jako například [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) a [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostika a metriky 

Můžete vytvořit a monitorovat kolekce [diagnostická data](https://docs.microsoft.com/cli/azure/vm/diagnostics) pomocí [metriky](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) v na webu Azure portal, rozhraní příkazového řádku Azure, Azure PowerShell a programovací rozhraní aplikace programovací (rozhraní API). Můžete například provést následující věci:

- **Sledujte základní metriky pro virtuální počítač.** Na obrazovce Přehled na webu Azure portal zahrnují základní metriky uvedené využití CPU, využití sítě, celkový počet bajtů disku a diskových operací za sekundu.

- **Povolit shromažďování diagnostiky spouštění a zobrazte ho pomocí webu Azure portal.** Při přenosu vlastní image do Azure nebo spouštění některé z imagí platformy, může být mnoho důvodů, proč virtuální počítač dostane do nespustitelného stavu. Při vytváření virtuálního počítače kliknutím můžete snadno povolit diagnostiku **povoleno** pro diagnostiku spouštění obrazovky nastavení v části monitorování.

    Při spouštění virtuálních počítačů, agent diagnostiky spouštění zaznamenává výstup spouštění a uloží jej v úložišti Azure. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Diagnostika spouštění nepovolí automaticky při vytvoření virtuálního počítače z nástroje příkazového řádku. Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Pokud povolíte diagnostiku spouštění na webu Azure Portal, účet úložiště se automaticky vytvoří za vás.

    Pokud jste nepovolili Diagnostika spouštění při vytvoření virtuálního počítače, můžete vždy povolit ho později pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic), nebo [šablony Azure Resource Manageru](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Povolte shromažďování dat diagnostiky hostovaného operačního systému.** Při vytváření virtuálního počítače, máte možnost v dialogovém okně nastavení Povolit diagnostiku hostovaného operačního systému. Pokud povolíte shromažďování dat diagnostiky [IaaSDiagnostics rozšíření pro Linux](../articles/virtual-machines/linux/diagnostic-extension.md) nebo [IaaSDiagnostics rozšíření pro Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) se přidá do virtuálního počítače, které umožňuje shromažďovat další datový disk, procesoru a paměti.

    Pomocí shromážděná diagnostická data, můžete nakonfigurovat automatické škálování pro virtuální počítače. Můžete také nakonfigurovat protokoly k ukládání dat a nastavení výstrah s oznámením výkon není úplně vpravo.

## <a name="alerts"></a>Výstrahy

Můžete vytvořit [výstrahy](../articles/azure-monitor/platform/alerts-overview.md) podle konkrétních metrik výkonu. Příklady problémy, se kterými můžete dostat upozornění: když průměrné využití procesoru překročí určitou prahovou hodnotu nebo volné místo na disku klesne pod určitou dobu. Upozornění se dá nakonfigurovat v [webu Azure portal](../articles/azure-monitor/platform/alerts-classic-portal.md)s použitím [prostředí Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), nebo [rozhraní příkazového řádku Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) poskytuje individuální pokyny a podporu, pokud vás ovlivňují problémy ve službách Azure, a pomůže vás připravit na chystanou plánovanou údržbu. Azure Service Health upozorní vás a vašich týmů pomocí cílených a flexibilních upozornění.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource health](../articles/service-health/resource-health-overview.md) pomáhá při diagnostice a získání podpory v případě, že problém Azure ovlivňuje vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.

## <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokolu aktivit Azure](../articles/azure-monitor/platform/activity-logs-overview.md) je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. V protokolu obsahuje celou řadu dat z Azure Resource Manageru provozních dat k aktualizacím na události služby Service Health. Protokol aktivit můžete kliknout na webu Azure Portal do zobrazení protokolu pro váš virtuální počítač.

Některé z akcí, které vám pomůžou s protokolu aktivit patří:

- Vytvoření [výstrahu pro událost protokolu aktivit](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Stream do centra událostí](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.
- Analyzovat pomocí Power BI [balíček obsahu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Uložte ho do účtu úložiště](../articles/azure-monitor/platform/archive-activity-log.md) pro archivaci nebo ruční kontrolu. Můžete zadat dobu uchování (ve dnech) pomocí profilu protokolu.

Data protokolu aktivit se můžete dostat taky pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor), nebo [monitorování rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/).

[Diagnostické protokoly Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) jsou protokoly generované ve vašem virtuálním počítači, které poskytují bohatou, časté data o její činnosti. Diagnostické protokoly se liší od protokolů aktivit tím, že poskytuje dokonalý přehled o operacích provedených ve virtuálním počítači.

Některé z akcí, které vám pomůžou s diagnostických protokolů patří:

- [Uložit do účtu úložiště](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Můžete zadat dobu uchování (ve dnech), pomocí nastavení diagnostiky prostředku.
- [Stream do služby Event Hubs](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.
- Analyzovat pomocí [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Pokročilé sledování

- [Azure Monitor](../articles/azure-monitor/overview.md) je služba, která monitoruje cloudové a místní prostředí s cílem zachovat jejich dostupnost a výkon. To poskytuje komplexní řešení pro shromažďování, analýzu a funguje na telemetrická data z vašeho cloudu a místních prostředích. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé. Rozšíření můžete nainstalovat [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/extensions-oms.md) nebo [virtuálního počítače Windows](../articles/virtual-machines/windows/extensions-oms.md) , který nainstaluje agenta Log Analytics můžete shromažďovat data protokolu a uložit do pracovního prostoru Log Analytics.

    Pro Windows a virtuální počítače s Linuxem po instalaci agenta Log Analytics je doporučená metoda pro shromažďování protokolů. Nejjednodušší způsob, jak nainstalovat agenta Log Analytics na virtuálním počítači je prostřednictvím [rozšíření Log Analytics pro virtuální počítač](../articles/log-analytics/log-analytics-azure-vm-extension.md). Použití rozšíření zjednodušuje proces instalace a automaticky agenta nakonfiguruje pro odesílání dat do pracovního prostoru služby Log Analytics, který zadáte. Agent se také automaticky upgraduje a tím zajišťuje, abyste měli nejnovější funkce a opravy.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) vám umožní monitorovat vašeho virtuálního počítače a její přidružené prostředky jejich vztah k síti, ke které jsou v. Rozšíření Network Watcher Agent můžete nainstalovat [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/extensions-nwa.md) nebo [virtuálního počítače Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure Monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) monitoruje analýzou výkonu a stavu systému Windows a virtuální počítače s Linuxem, včetně jejich různé procesy a propojených záviset na jiných prostředcích a externí virtuální počítače Azure (VM) ve velkém měřítku procesy. 

## <a name="next-steps"></a>Další postup
- Provede v [monitorování virtuálního počítače Windows pomocí Azure Powershellu](../articles/virtual-machines/windows/tutorial-monitoring.md) nebo [monitorování virtuálního počítače s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Další informace o osvědčených postupech kolem [monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
