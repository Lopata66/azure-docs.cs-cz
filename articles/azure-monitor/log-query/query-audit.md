---
title: Dotazy auditu v Azure Monitor dotazy protokolu
description: Podrobnosti o protokolech auditu dotazů protokolu, které poskytují telemetrii o dotazech protokolů spouštěných v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/25/2020
ms.openlocfilehash: cb38dcba2f61a432decb56164b816688ad3192d8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893702"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Auditovat dotazy v protokolech Azure Monitor (Preview)
Protokoly auditu dotazů na protokol poskytují telemetrii o dotazech protokolů spouštěných v Azure Monitor. Obsahuje například informace o tom, kdy se dotaz spustil, kdo ho spustil, jaký nástroj se použil, text dotazu a Statistika výkonu popisující spuštění dotazu.

## <a name="current-limitations"></a>Aktuální omezení
Během veřejné verze Preview platí následující omezení:

- Budou protokolovány pouze dotazy zaměřené na pracovní prostor. Dotazy spouštěné v režimu orientovaném na prostředky nebo spuštěné proti Application Insights nenakonfigurovanému jako založeném na pracovním prostoru nebudou protokolovány.


## <a name="configure-query-auditing"></a>Konfigurace auditování dotazů
Auditování dotazů je povoleno s [nastavením diagnostiky](../platform/diagnostic-settings.md) v pracovním prostoru Log Analytics. To vám umožní posílat data auditu do aktuálního pracovního prostoru nebo jakéhokoli jiného pracovního prostoru v rámci předplatného, do Azure Event Hubs posílat mimo Azure nebo Azure Storage k archivaci. 

### <a name="azure-portal"></a>portál Azure
Přístup k nastavení diagnostiky pro Log Analytics pracovní prostor v Azure Portal v jednom z následujících umístění:

- V nabídce **Azure monitor** vyberte **nastavení diagnostiky**a pak vyhledejte a vyberte pracovní prostor.

    [![Nastavení diagnostiky Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png)](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- V nabídce **Log Analytics pracovní prostory** vyberte pracovní prostor a pak vyberte **nastavení diagnostiky**.

    [![Nastavení diagnostiky Log Analytics pracovní prostor ](media/log-query-audit/diagnostic-setting-workspace.png)](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Šablona Resource Manageru
Můžete získat příklad Správce prostředků šablonu z [nastavení diagnostiky pro Log Analytics pracovní prostor](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Data auditu
Záznam auditu se vytvoří pokaždé, když se spustí dotaz. Pokud data odešlete do pracovního prostoru Log Analytics, uloží se do tabulky s názvem *LAQueryLogs*. Následující tabulka popisuje vlastnosti v jednotlivých záznamech dat auditu.

| Pole | Popis |
|:---|:---|
| TimeGenerated         | Čas UTC při odeslání dotazu. |
| CorrelationId         | Jedinečné ID pro identifikaci dotazu. Dá se použít při řešení potíží při kontaktování Microsoftu o pomoc. |
| AADObjectId           | Azure Active Directory ID uživatelského účtu, který spustil dotaz  |
| AADTenantId           | ID tenanta uživatelského účtu, který spustil dotaz  |
| AADEmail              | E-mail tenanta uživatelského účtu, který spustil dotaz  |
| AADClientId           | ID a vyřešený název aplikace použité ke spuštění dotazu. |
| RequestClientApp      | Vyřešený název aplikace použitý ke spuštění dotazu. |
| QueryTimeRangeStart   | Začátek časového rozsahu vybraného pro dotaz Tato hodnota nemusí být naplněna v některých případech, například když je dotaz spuštěn z Log Analytics a v dotazu není zadán časový rozsah místo pro výběr času. |
| QueryTimeRangeEnd     | Konec časového rozsahu vybraného pro dotaz Tato hodnota nemusí být naplněna v některých případech, například když je dotaz spuštěn z Log Analytics a v dotazu není zadán časový rozsah místo pro výběr času.  |
| QueryText             | Text dotazu, který byl spuštěn. |
| RequestTarget         | Adresa URL rozhraní API se použila k odeslání dotazu.  |
| Třída requestContext        | Seznam prostředků, na které byl dotaz požádán ke spuštění. Obsahuje až tři řetězcová pole: pracovní prostory, aplikace a prostředky. V rámci předplatného nebo skupiny prostředků se budou zobrazovat cílené dotazy jako *prostředky*. Zahrnuje cíl odvozený pomocí RequestTarget. |
| RequestContextFilters | Sada filtrů zadaná jako součást vyvolání dotazu. Zahrnuje až tři možná pole řetězců:<br>-ResourceType-typ prostředku pro omezení oboru dotazu<br>-Pracovní prostory – seznam pracovních prostorů, na které se má dotaz omezit<br>-WorkspaceRegions-seznam oblastí pracovního prostoru pro omezení dotazu |
| ResponseCode          | Kód odpovědi HTTP vrácený při odeslání dotazu. |
| ResponseDurationMs    | Čas, který má být vrácen pro odpověď.  |
| StatsCPUTimeMs       | Celkový výpočetní čas, který se používá pro výpočet, analýzu a načítání dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedKB | Množství dat, ke kterým byl přidaný dotaz zpracován. Ovlivněno velikostí cílové tabulky, použitým časovým rozsahem, použitými filtry a počtem odkazovaných sloupců. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedStart | Čas nejstarší dat, ke kterým byl přidaný dotaz zpracován. Ovlivněno explicitním časovým rozsahem dotazu a použitými filtry. To může být větší než explicitní časové období z důvodu dělení dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedEnd  |Čas nejnovějších dat, ke kterým došlo při zpracování dotazu. Ovlivněno explicitním časovým rozsahem dotazu a použitými filtry. To může být větší než explicitní časové období z důvodu dělení dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsWorkspaceCount | Počet pracovních prostorů, ke kterým dotaz přistupoval. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsRegionCount | Počet oblastí, ke kterým dotaz přistupoval. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [nastavení diagnostiky](../platform/diagnostic-settings.md).
- Přečtěte si další informace o [optimalizaci dotazů protokolu](query-optimization.md).