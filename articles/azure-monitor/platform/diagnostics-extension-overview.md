---
title: Přehled rozšíření Azure Diagnostics
description: Použití diagnostiky Azure pro ladění, měření výkonu, monitorování, analýzu provozu v cloud services, virtual machines a service fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236230"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co je rozšíření Azure Diagnostics
Rozšíření Azure Diagnostics je agent v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Rozšíření diagnostiky můžete z mnoha různých zdrojů. V tuto chvíli nepodporuje jsou cloudové služby Azure (klasické) webové a pracovní role virtuálních počítačů, virtuálních počítačů škálovat sady a Service Fabric. Další služby Azure mají různé diagnostiky metody. Zobrazit [Přehled monitorování v Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linuxový agent
A [Linux verze rozšíření](../../virtual-machines/extensions/diagnostics-linux.md) je k dispozici pro virtuální počítače s Linuxem. Shromažďovat statistiky a chování se liší od verze Windows.

## <a name="data-you-can-collect"></a>Data můžete shromažďovat
Rozšíření Azure Diagnostics můžete shromažďovat následující typy dat:

| Zdroj dat | Popis |
| --- | --- |
| Metriky čítače výkonu |Operační systém a vlastní čítače výkonu |
| Protokoly aplikací |Trasovací zprávy, autorem aplikace |
| Protokoly událostí Windows |Informace odesílané do systému pro protokolování událostí Windows |
| Rozhraní .NET EventSource protokoly |Kód zápisu událostí pomocí rozhraní .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) třídy |
| Protokoly IIS |Informace o webech služby IIS |
| [Manifest na základě protokolů trasování událostí pro Windows](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Události trasování pro Windows události generované modulem nějaký proces. (1) |
| Výpisy stavu systému (protokoly) |Informace o stavu procesu, pokud dojde k chybě aplikace |
| Vlastní protokoly chyb |Protokoly vytvořené metodami vaše aplikace nebo služba |
| Protokoly infrastruktury diagnostiky Azure |Informace o Azure Diagnostics, vlastní |

(1) Pokud chcete získat seznam zprostředkovatelů trasování událostí pro Windows, spusťte `c:\Windows\System32\logman.exe query providers` v okně konzoly na počítači, který chcete shromáždit informace z.

## <a name="data-storage"></a>Úložiště dat
Rozšíření ukládá data do [účtu služby Azure Storage](diagnostics-extension-to-storage.md) , který zadáte.

Můžete také odeslat ho do [Application Insights](../../azure-monitor/app/cloudservices.md). 

Další možností je na datový proud stream [centra událostí](../../event-hubs/event-hubs-about.md), která pak můžete odeslat do služby monitorování mimo Azure.

Máte také možnost odeslání dat do databáze časových řad metrik Azure monitoru. V současné době tuto jímku platí pouze pro čítače výkonu. Umožňuje vám odesílat čítače výkonu v jako vlastní metriky. Tato funkce je ve verzi Preview. Jímka Azure monitoru podporuje:
* Načítání všech čítačů výkonu odesílat přes Azure Monitor [Azure Monitor metriky rozhraní API.](https://docs.microsoft.com/rest/api/monitor/)
* Upozorňuje na všechny čítače výkonu odesílat přes Azure Monitor [upozornění na metriku](../../azure-monitor/platform/alerts-overview.md) ve službě Azure Monitor
* Použít zástupný znak operátoru v čítače výkonu jako dimenze "Instance" na vaše metriky.  Například pokud jste shromáždili "logický disk (\*) / DiskWrites za sekundu" čítače by být schopen filtr a rozdělit na dimenzi "Instanci" do diagramů nebo upozorňovat na zápis disku/s pro jednotlivé logické disky na virtuálním počítači (například C:)

Další informace o tom, jak nakonfigurovat tento jímky, najdete v tématu [dokumentace schématu Azure diagnostics.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Náklady
Každý z výše uvedených mohou vám být naúčtovány náklady. Ujistěte se, že jste výzkumné a předejít neočekávané faktury.  Application Insights v Centru událostí a Azure Storage mají samostatné náklady spojené s ingestování a čas uložený. Konkrétně se služby Azure Storage bude obsahovat všechna data navždy, možná budete chtít odstranit starší data za určité časové období pro neplaťte víc.    

## <a name="versioning-and-configuration-schema"></a>Schéma vytváření verzí a konfigurace
Zobrazit [historie verzí diagnostiky Azure a schéma](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Další postup
Zvolte službu, které se snažíte shromažďování diagnostických údajů na a použijte tyto články, abyste mohli začít. Pomocí odkazů obecné diagnostiky Azure pro referenční informace pro konkrétní úlohy.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloudové služby pomocí diagnostiky Azure
* Pokud používáte Visual Studio, přečtěte si téma [použijte sadu Visual Studio pro sledování aplikace Cloud Services](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) začít. V opačném případě naleznete v tématu
* [Monitorování cloudové služby pomocí diagnostiky Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Nastavení diagnostiky Azure do aplikace Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Pro cloudové služby pomocí diagnostiky Azure pomocí Application Insights](../../azure-monitor/app/cloudservices.md)
* [Trasování toku aplikace Cloud Services s využitím Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Nastavení diagnostiky na Cloud Services pomocí Powershellu](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuální počítače
* Pokud používáte Visual Studio, přečtěte si téma [pomocí aplikace Visual Studio trasování Azure Virtual Machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) začít. V opačném případě naleznete v tématu
* [Nastavení Azure Diagnostics na virtuálním počítači Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Pokročilejší témata naleznete v tématu

* [Použití Powershellu k nastavení diagnostiky v Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření Windows virtuální počítače s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Začínáme při [monitorovat aplikace Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mnoho dalších článcích diagnostické nástroje Service Fabric jsou k dispozici v navigačním stromu na levé straně, jakmile získáte k tomuto článku.

## <a name="general-articles"></a>Obecné články související s
* Zjistěte, jak [pomocí čítačů výkonu v diagnostice Azure](../../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže se spuštěním diagnostiky nebo hledání vaše data do tabulky v úložišti Azure, najdete v článku [odstraňování potíží diagnostiky Azure](diagnostics-extension-troubleshooting.md)

