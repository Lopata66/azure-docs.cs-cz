---
title: Shromažďovat & analyzovat protokoly prostředků
description: Zaznamenává a analyzuje události protokolu prostředků pro Azure Container Registry, jako je například ověřování, vkládání obrázků a vyžádanou image.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409639"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky

Tento článek vysvětluje, jak shromažďovat data protokolu pro službu Azure Container Registry pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [protokoly prostředků](../azure-monitor/platform/platform-logs-overview.md) (dříve nazývané *diagnostické protokoly*) pro události řízené uživateli v registru. Shromažďování a využívání těchto dat pro splnění potřeb, jako jsou:

* Auditování událostí ověřování registru za účelem zajištění zabezpečení a dodržování předpisů 

* Zadejte úplný záznam aktivity pro artefakty registru, jako jsou události Pull a pull, abyste mohli diagnostikovat provozní problémy s registrem. 

Shromažďování dat protokolu prostředků pomocí Azure Monitor může nabývat dalších nákladů. Viz [ceny Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Události úložiště

V současné době jsou protokolovány následující události na úrovni úložiště pro bitové kopie a jiné artefakty:

* **Události push**
* **Události vyžádání**
* **Události zrušit označení**
* **Odstranit události** (včetně událostí odstranění úložiště)

Události na úrovni úložiště, které nejsou aktuálně přihlášené: vyprázdnit události

## <a name="registry-resource-logs"></a>Protokoly prostředků registru

Protokoly prostředků obsahují informace vydávané prostředky Azure, které popisují jejich interní operace. V případě služby Azure Container registry obsahují protokoly události na úrovni ověřování a úložiště uložené v následujících tabulkách. 

* **ContainerRegistryLoginEvents** – události a stav ověřování registru, včetně příchozí identity a IP adresy
* **ContainerRegistryRepositoryEvents** – operace jako push a pull pro image a další artefakty v úložištích registru
* **AzureMetrics** - [metrik registru kontejnerů](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , jako jsou agregovaná nabízená oznámení a počty vyžádaného čtení.

V případě operací zahrnuje data protokolu:
  * Stav úspěch nebo neúspěch
  * Počáteční a koncové časové razítko

Kromě protokolů prostředků poskytuje Azure [Protokol aktivit](../azure-monitor/platform/platform-logs-overview.md), což je jeden záznam událostí správy Azure na úrovni předplatného, jako je například vytvoření nebo odstranění registru kontejneru.

## <a name="enable-collection-of-resource-logs"></a>Povolit shromažďování protokolů prostředků

Kolekce protokolů prostředků pro registr kontejneru není ve výchozím nastavení povolená. Explicitně povolte nastavení diagnostiky pro každý registr, který chcete monitorovat. Možnosti pro povolení nastavení diagnostiky najdete [v tématu Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../azure-monitor/platform/diagnostic-settings.md).

Pokud například chcete zobrazit protokoly a metriky pro registr kontejnerů téměř v reálném čase v Azure Monitor, Shromážděte protokoly prostředků v pracovním prostoru Log Analytics. Povolení tohoto nastavení diagnostiky pomocí Azure Portal:

1. Pokud ještě nemáte pracovní prostor, vytvořte pracovní prostor pomocí [Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Chcete-li minimalizovat latenci v shromažďování dat, ujistěte se, že je pracovní prostor ve **stejné oblasti** jako registr kontejneru.
1. Na portálu vyberte registr a vyberte **monitorování > nastavení diagnostiky > přidat nastavení diagnostiky**.
1. Zadejte název nastavení a vyberte **Odeslat do Log Analytics**.
1. Vyberte pracovní prostor pro diagnostické protokoly registru.
1. Vyberte data protokolu, která chcete shromáždit, a klikněte na **Uložit**.

Následující obrázek ukazuje vytvoření nastavení diagnostiky pro registr pomocí portálu.

![Povolení nastavení diagnostiky](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Shromážděte jenom data, která potřebujete, vyvážením nákladů a vašich potřeb pro monitorování. Pokud například potřebujete auditovat pouze události ověřování, vyberte pouze protokol **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Zobrazit data v Azure Monitor

Po povolení shromažďování diagnostických protokolů v Log Analytics může trvat několik minut, než se data zobrazí v Azure Monitor. Chcete-li zobrazit data na portálu, vyberte registr a vyberte **sledování > protokoly**. Vyberte jednu z tabulek, která obsahuje data pro registr. 

Spusťte dotazy pro zobrazení dat. Je k dispozici několik ukázkových dotazů nebo vlastní spuštění. Například následující dotaz načte nejaktuálnější 24 hodin dat z tabulky **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Následující obrázek ukazuje vzorový výstup:

![Dotazy na data protokolů](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Kurz týkající se použití Log Analytics v Azure Portal najdete v tématu [Začínáme s Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)nebo vyzkoušejte [ukázkové prostředí](https://portal.loganalytics.io/demo)Log Analytics. 

Další informace o dotazech protokolu najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Další příklady dotazů

#### <a name="100-most-recent-registry-events"></a>100 nejnovějších událostí registru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Další cíle protokolu

Kromě odesílání protokolů pro Log Analytics, nebo jako alternativu můžete běžným scénářem vybrat Azure Storage účet jako cíl protokolu. Pokud chcete archivovat protokoly v Azure Storage, vytvořte si účet úložiště ještě předtím, než povolíte archivaci prostřednictvím nastavení diagnostiky.

Události diagnostického protokolu můžete také streamovat do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs může ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o použití [Log Analytics](../azure-monitor/log-query/get-started-portal.md) a vytváření [dotazů protokolu](../azure-monitor/log-query/get-started-queries.md).
* Další informace o protokolech platforem, které jsou k dispozici v různých vrstvách Azure, najdete v tématu [Přehled protokolů platformy Azure](../azure-monitor/platform/platform-logs-overview.md) .

