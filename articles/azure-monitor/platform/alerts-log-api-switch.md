---
title: Upgrade na aktuální rozhraní API výstrahy protokolu Azure Monitor
description: Přečtěte si, jak přepnout na rozhraní API ScheduledQueryRules výstrahy protokolu.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294508"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Upgradovat na aktuální rozhraní API upozornění protokolů ze starší verze rozhraní API Log Analytics

> [!NOTE]
> Tento článek se týká jenom Azure Public (**ne** pro Azure Government nebo Azure Čína Cloud).

> [!NOTE]
> Když se uživatel rozhodne přepnout Předvolby na aktuální [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) , není možné vrátit se zpátky na starší [verze rozhraní API výstrah Log Analytics](api-alerts.md).

V minulosti uživatelé používali [starší rozhraní API pro upozornění Log Analytics](api-alerts.md) ke správě pravidel upozornění protokolů. Aktuální pracovní prostory používají [rozhraní ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules). Tento článek popisuje výhody a proces přepínání ze starší verze rozhraní API na aktuální rozhraní API.

## <a name="benefits"></a>Výhody

- Jedna šablona pro vytváření pravidel upozornění (dříve nutné tři samostatné šablony).
- Jedno rozhraní API pro Log Analytics pracovní prostory nebo prostředky Application Insights.
- [Podpora rutin PowerShellu](alerts-log.md#managing-log-alerts-using-powershell).
- Zarovnání závažnosti u všech ostatních typů výstrah.
- Možnost vytvořit [výstrahu protokolu pro různé pracovní prostory](../log-query/cross-workspace-query.md) , která bude zahrnovat několik externích prostředků, jako jsou Log Analytics pracovní prostory nebo prostředky Application Insights.
- Uživatelé mohou zadat dimenze pro rozdělení výstrah pomocí parametru Aggregate on.
- Výstrahy protokolu mají prodlouženou dobu až dva dny dat (dříve omezené na jeden den).

## <a name="impact"></a>Dopad

- Všechna nová pravidla se musí vytvořit nebo upravit s aktuálním rozhraním API. Podívejte se na [ukázkové použití prostřednictvím šablony prostředků Azure](alerts-log-create-templates.md) a [ukázkového použití prostřednictvím PowerShellu](alerts-log.md#managing-log-alerts-using-powershell).
- Pravidla se Azure Resource Manager sledovaných prostředků v aktuálním rozhraní API a musí být jedinečná. ID prostředku pravidla se změní na tuto strukturu: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Zobrazované názvy pravidla výstrahy zůstanou beze změny.

## <a name="process"></a>Proces

Proces přepínání není interaktivní a nevyžaduje ve většině případů ruční kroky. Vaše pravidla upozornění nejsou v průběhu nebo po přepínači zastavená nebo zastavená.
Přepněte do tohoto volání všechna pravidla upozornění přidružená ke konkrétnímu pracovnímu prostoru Log Analytics:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

S textem žádosti obsahujícím níže uvedený kód JSON:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Tady je příklad použití [ARMClient](https://github.com/projectkudu/ARMClient), open source nástroje příkazového řádku, který zjednodušuje vyvolání výše uvedeného volání rozhraní API:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Pokud je přepínač úspěšný, odpověď je:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Zkontroluje stav přepínání pracovního prostoru.

Toto volání rozhraní API můžete použít také ke kontrole stavu přepínače:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Můžete také použít nástroj [ARMClient](https://github.com/projectkudu/ARMClient) :

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud byl pracovní prostor Log Analytics přepnut na [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules), odpověď je následující:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Pokud nebyl pracovní prostor Log Analytics přepnut, odpověď je následující:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [výstrahách Azure monitor-log](alerts-unified-log.md).
- Naučte se [Spravovat výstrahy protokolu pomocí rozhraní API](alerts-log-create-templates.md).
- Naučte se [Spravovat výstrahy protokolu pomocí PowerShellu](alerts-log.md#managing-log-alerts-using-powershell).
- Přečtěte si další informace o [prostředí výstrah Azure](./alerts-overview.md).
