---
title: Mít klasických upozornění na metriku oznámení do systému mimo Azure, pomocí webhooku
description: Zjistěte, jak přesměrovat Azure upozornění metrik, která mimo Azure systémů.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345770"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Mít klasických upozornění na metriku oznámení do systému mimo Azure, pomocí webhooku
Použití webhooků pro směrování Azure oznámení s dalšími systémy pro následné zpracování nebo vlastní akce. Webhooku v upozornění můžete směrovat do služby, které posílat SMS zprávy do protokolu chyb, které chcete upozornit tým prostřednictvím chatu nebo zasílání zpráv služby, nebo pro různé další akce. 

Tento článek popisuje, jak nastavit webhooku v upozornění metriky Azure. Taky se naučíte vypadá datová část požadavku HTTP POST do webhooku. Informace o instalaci a schéma pro aktivitu Azure upozornění protokolu (upozornění na události), najdete v části [zavolání webhooku v upozornění protokolu aktivit Azure](alerts-log-webhook.md).

Upozornění v Azure pomocí HTTP POST k odesílání výstrah obsah ve formátu JSON pro webhook identifikátor URI, který zadáte při vytváření upozornění. Schéma je definované dále v tomto článku. Identifikátor URI musí být platný koncový bod HTTP nebo HTTPS. Azure účtuje jedna položka každý požadavek při aktivaci výstrahy.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurace webhooků prostřednictvím webu Azure portal
Chcete-li přidat nebo aktualizovat webhook identifikátor URI, v [webu Azure portal](https://portal.azure.com/), přejděte na **Create/Update výstrahy**.

![Přidání pravidla výstrahy podokna](./media/alerts-webhooks/Alertwebhook.png)

Můžete také nakonfigurovat výstrahu na Odeslat požadavek POST webhooku identifikátoru URI pomocí [rutin prostředí Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [– multiplatformního rozhraní příkazového řádku](../../azure-monitor/platform/cli-samples.md#work-with-alerts), nebo [REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Ověření se webhook.
Webhook se ověřit pomocí ověřování založené na tokenech. Webhook identifikátoru URI je uložen s ID tokenu Příklad: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Datová část schématu
Operace POST obsahuje následující datovou část JSON a schéma pro všechny výstrahy na základě metrik:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Pole | Povinné | Pevné sady hodnot | Poznámky |
|:--- |:--- |:--- |:--- |
| status |Ano |Aktivováno, vyřešeno |Stav upozornění na základě podmínek, je nastavit. |
| context |Ano | |Kontext výstrahy. |
| timestamp |Ano | |Čas, kdy byla výstraha. |
| id |Ano | |Každé pravidlo upozornění má jedinečné ID. |
| name |Ano | |Název výstrahy. |
| description |Ano | |Popis výstrahy. |
| conditionType |Ano |Metriky, události |Podporuje dva typy výstrah: metrik a událostí. Upozornění na metriky jsou založeny na podmínku metriky. Výstrahy na události jsou založené na události v protokolu aktivit. Tuto hodnotu použijte, chcete-li zkontrolovat, jestli upozornění je založené na metriku nebo události. |
| condition |Ano | |Na základě konkrétních polí ke kontrole **conditionType** hodnotu. |
| metricName |Pro upozornění na metriku | |Název metriky, která definuje, co pravidlo monitoruje. |
| metricUnit |Pro upozornění na metriku |Počet bajtů, BytesPerSecond, Count, CountPerSecond, %, sekund |Jednotka povolené v metrice. Zobrazit [povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Pro upozornění na metriku | |Skutečná hodnota metriky, který výstrahu způsobil. |
| threshold |Pro upozornění na metriku | |Prahová hodnota, na který se aktivuje upozornění. |
| windowSize |Pro upozornění na metriku | |Časový úsek, který slouží k monitorování výstrah aktivity podle prahovou hodnotu. Hodnota musí být mezi 5 minutami a 1 den. Hodnota musí být ve formátu ISO 8601 doby trvání. |
| timeAggregation |Pro upozornění na metriku |Průměr, poslední, Maximum, Minimum, None, celkem |Jak by měl kombinovat data, která se shromažďují v čase. Výchozí hodnota je průměr. Zobrazit [povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Pro upozornění na metriku | |Operátor, který se používá k porovnání aktuálního data metriky pro nastavenou prahovou hodnotu. |
| subscriptionId |Ano | |ID předplatného Azure. |
| resourceGroupName |Ano | |Název skupiny prostředků pro daný prostředek. |
| resourceName |Ano | |Název prostředku ovlivněných prostředků. |
| resourceType |Ano | |Typ prostředku ovlivněných prostředků. |
| resourceId |Ano | |ID prostředku ovlivněných prostředků. |
| resourceRegion |Ano | |Oblast nebo umístění ovlivněných prostředků. |
| portalLink |Ano | |Přímý odkaz na souhrnné stránce portálu prostředků. |
| properties |Ne |Nepovinné |Sada párů klíč/hodnota, který obsahuje podrobnosti o události. Například, `Dictionary<String, String>`. Vlastnosti pole je volitelné. Do vlastního uživatelského rozhraní nebo pracovní postup založený na aplikaci logiky mohou uživatelé zadat dvojice klíč/hodnota, které mohou být předány prostřednictvím datové části. Existuje alternativní způsob předat vlastní vlastnosti webhooku je prostřednictvím webhooku URI samotného (jako parametry dotazu). |

> [!NOTE]
> Můžete nastavit **vlastnosti** pole pouze pomocí [REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Další postup
* Další informace o upozornění v Azure a webhooky ve videu [výstrahy integrace Azure s použitím PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Zjistěte, jak [spouštění skriptů Azure Automation (runbooky) na upozornění Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Zjistěte, jak [pomocí aplikace logiky můžete poslat zprávu SMS přes Twilio z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Zjistěte, jak [pomocí aplikace logiky můžete poslat zprávu Slack z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Zjistěte, jak [pomocí aplikace logiky pro odeslání zprávy do fronty služby Azure z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

