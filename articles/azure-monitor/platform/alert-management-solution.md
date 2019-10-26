---
title: Řešení Alert Management v Azure Log Analytics | Microsoft Docs
description: Řešení Alert Management v Log Analytics pomáhá analyzovat všechny výstrahy ve vašem prostředí.  Kromě slučování výstrah vygenerovaných v rámci Log Analytics importuje aplikace do Log Analytics výstrahy z připojených skupin pro správu System Center Operations Manager.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: fe484d8b5a06946b844acb5e506ec4dcc99ebc23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932731"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Řešení Alert Management v Azure Log Analytics

![Ikona Alert Management](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor teď podporuje vylepšené funkce pro [správu výstrah ve velkém měřítku](https://aka.ms/azure-alerts-overview), včetně těch, které vygenerovaly [Nástroje pro monitorování, jako je System Center Operations Manager, Zabbix nebo Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Řešení Alert Management vám pomůže analyzovat všechny výstrahy v úložišti Log Analytics.  Tyto výstrahy můžou pocházet z nejrůznějších zdrojů, včetně těchto zdrojů [vytvořených Log Analytics](../../azure-monitor/platform/alerts-overview.md) nebo [importovaných z Nagios nebo Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Řešení také importuje výstrahy ze všech [připojených skupin pro správu System Center Operations Manager](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Předpoklady
Řešení funguje se všemi záznamy v úložišti Log Analytics s typem **výstrahy**, takže musíte provést jakoukoli konfiguraci, která je nutná ke shromáždění těchto záznamů.

- Pro výstrahy Log Analytics [vytvořte pravidla upozornění](../../azure-monitor/platform/alerts-overview.md) pro vytváření záznamů výstrah přímo v úložišti.
- U upozornění Nagios a Zabbix [nakonfigurujte tyto servery](../../azure-monitor/learn/quick-collect-linux-computer.md) tak, aby odesílaly výstrahy Log Analytics.
- Pro výstrahy System Center Operations Manager [Připojte skupinu pro správu Operations Manager k vašemu pracovnímu prostoru Log Analytics](../../azure-monitor/platform/om-agents.md).  Všechny výstrahy vytvořené v System Center Operations Manager jsou importovány do Log Analytics.  

## <a name="configuration"></a>Konfigurace
Přidejte Alert Management řešení do svého pracovního prostoru Log Analytics pomocí postupu popsaného v tématu [Přidání řešení](../../azure-monitor/insights/solutions.md). Není nutná žádná další konfigurace.

## <a name="management-packs"></a>Sady Management Pack
Pokud je vaše skupina pro správu System Center Operations Manager připojená k pracovnímu prostoru Log Analytics, po přidání tohoto řešení se do System Center Operations Manager nainstalují následující sady Management Pack.  Není nutná žádná konfigurace nebo údržba požadovaných sad Management Pack.

* Microsoft System Center Advisor Alert Management (Microsoft. IntelligencePacks. AlertManagement)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Shromažďování dat
### <a name="agents"></a>Agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|:--- |:--- |:--- |
| [Agenti systému Windows](agent-windows.md) | Ne |Přímo agenti Windows negenerují výstrahy.  Výstrahy Log Analytics lze vytvořit z událostí a dat o výkonu shromážděných z agentů systému Windows. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne |Přímo agenti Linux negenerují výstrahy.  Výstrahy Log Analytics lze vytvořit z událostí a dat o výkonu shromážděných z agentů systému Linux.  Výstrahy Nagios a Zabbix se shromažďují z těch serverů, které vyžadují agenta pro Linux. |
| [System Center Operations Manager skupina pro správu](../../azure-monitor/platform/om-agents.md) |Ano |Výstrahy, které jsou generovány v agentech Operations Manager, jsou doručeny do skupiny pro správu a poté předány do Log Analytics.<br><br>Přímé připojení od agentů Operations Manager k Log Analytics se nevyžaduje. Data výstrah se předávají ze skupiny pro správu do úložiště Log Analytics. |


### <a name="collection-frequency"></a>Četnost shromažďování dat
- Záznamy výstrah jsou k dispozici pro řešení, jakmile jsou uloženy v úložišti.
- Data výstrah se odesílají ze skupiny pro správu Operations Manager k Log Analytics každé tři minuty.  

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení Alert Management do pracovního prostoru Log Analytics, do řídicího panelu se přidá dlaždice **Alert Management** .  Tato dlaždice zobrazuje počet a grafické znázornění počtu aktuálně aktivních výstrah, které byly vygenerovány během posledních 24 hodin.  Tento časový rozsah nemůžete změnit.

![Dlaždice Alert Management](media/alert-management-solution/tile.png)

Kliknutím na dlaždici **Alert Management** otevřete řídicí panel **Alert Management** .  Řídicí panel obsahuje sloupce v následující tabulce.  V každém sloupci je uveden seznam prvních 10 výstrah podle počtu, který odpovídá kritériím daného sloupce pro zadaný obor a časový rozsah.  Hledání v protokolu, které poskytuje celý seznam, můžete spustit kliknutím na **Zobrazit vše** v dolní části sloupce nebo kliknutím na záhlaví sloupce.

| Column | Popis |
|:--- |:--- |
| Důležitá upozornění |Všechny výstrahy se závažností kritické seskupené podle názvu výstrahy.  Kliknutím na název výstrahy spustíte hledání v protokolu, které vrátí všechny záznamy pro tuto výstrahu. |
| Varovné výstrahy |Všechny výstrahy se závažností upozornění seskupené podle názvu výstrahy.  Kliknutím na název výstrahy spustíte hledání v protokolu, které vrátí všechny záznamy pro tuto výstrahu. |
| Výstrahy Active System Center Operations Manager |Všechny výstrahy shromážděné z Operations Manager s jakýmkoli jiným stavem než *uzavřeným* seskupeným podle zdroje, který výstrahu vygeneroval. |
| Všechny aktivní výstrahy |Všechny výstrahy s libovolnou závažností seskupené podle názvu výstrahy. Zahrnuje jenom Operations Manager výstrahy se stavem jiný než *Uzavřeno*. |

Pokud se posunete napravo, řídicí panel obsahuje několik běžných dotazů, na které můžete kliknout a provést [prohledávání protokolu](../../azure-monitor/log-query/log-query-overview.md) pro data výstrah.

![Řídicí panel Alert Management](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení Alert Management analyzuje libovolný záznam s typem **výstrahy**.  Výstrahy vytvořené Log Analytics nebo shromážděné z Nagios nebo Zabbix nejsou tímto řešením přímo shromažďovány.

Řešení importuje výstrahy z System Center Operations Manager a vytvoří odpovídající záznam pro každý s typem **výstrahy** a SourceSystem **OpsManager**.  Tyto záznamy mají vlastnosti v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*Upozornění* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Podrobnosti položky dat, která způsobila generování výstrahy ve formátu XML. |
| `AlertDescription` |Podrobný popis výstrahy |
| `AlertId` |Identifikátor GUID výstrahy |
| `AlertName` |Název výstrahy |
| `AlertPriority` |Úroveň priority výstrahy |
| `AlertSeverity` |Úroveň závažnosti výstrahy. |
| `AlertState` |Poslední stav řešení výstrahy |
| `LastModifiedBy` |Jméno uživatele, který výstrahu naposledy změnil |
| `ManagementGroupName` |Název skupiny pro správu, ve které byla výstraha vygenerována. |
| `RepeatCount` |Počet vygenerovaných stejných výstrah pro stejný monitorovaný objekt, protože byl vyřešen. |
| `ResolvedBy` |Jméno uživatele, který výstrahu vyřešil. Prázdné, pokud výstraha ještě není vyřešena. |
| `SourceDisplayName` |Zobrazovaný název objektu monitorování, který výstrahu vygeneroval. |
| `SourceFullName` |Úplný název objektu monitorování, který výstrahu vygeneroval. |
| `TicketId` |ID lístku pro výstrahu, pokud je prostředí System Center Operations Manager integrováno do procesu pro přiřazování lístků pro výstrahy.  Není přiřazeno prázdné ID lístku. |
| `TimeGenerated` |Datum a čas, kdy byla výstraha vytvořena. |
| `TimeLastModified` |Datum a čas poslední změny výstrahy. |
| `TimeRaised` |Datum a čas, kdy byla výstraha vygenerována. |
| `TimeResolved` |Datum a čas, kdy byla výstraha vyřešena Prázdné, pokud výstraha ještě není vyřešena. |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy výstrah shromážděné tímto řešením: 

| Dotaz | Popis |
|:---|:---|
| Výstraha &#124; , kde SourceSystem = = "OpsManager" and AlertSeverity = = "Error" a TimeRaised > před (24 hodin) |Kritické výstrahy vyvolané během posledních 24 hodin |
| Výstraha &#124; , kde AlertSeverity = = "Warning" a TimeRaised > před (24 hodin) |Výstrahy upozornění vyvolané během posledních 24 hodin |
| Výstraha &#124; , kde SourceSystem = = "OpsManager" and stav alertstate! = "Closed" and TimeRaised > před ( &#124; 24 hodin): Count = Count () by zdrojovýnázevzobrazení |Zdroje s aktivními výstrahami vyvolanými během posledních 24 hodin |
| Výstraha &#124; , kde SourceSystem = = "OpsManager" and AlertSeverity = = "Error" a TimeRaised > před (24 hodin) a stav alertstate! = "uzavřeno" |Kritické výstrahy vyvolané během posledních 24 hodin, které jsou pořád aktivní |
| Výstraha &#124; , kde SourceSystem = = "OpsManager" a TimeRaised > před (24 hodin) a stav alertstate = = "uzavřeno" |Výstrahy vyvolané během posledních 24 hodin, které jsou nyní uzavřeny |
| Výstraha &#124; , kde SourceSystem = = "OpsManager" and TimeRaised > před ( &#124; 1d) hodnota sumarizace = Count () od AlertSeverity |Výstrahy vyvolané za poslední 1 den seskupené podle závažnosti |
| Výstraha &#124; , kde SourceSystem = = "OpsManager" a TimeRaised > před ( &#124; 1d) seřadit podle RepeatCount DESC |Výstrahy vyvolané za poslední 1 den seřazené podle hodnoty počtu opakování |



## <a name="next-steps"></a>Další kroky
* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](../../azure-monitor/platform/alerts-overview.md).
