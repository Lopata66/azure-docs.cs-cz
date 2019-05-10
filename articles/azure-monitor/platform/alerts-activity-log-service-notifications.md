---
title: Dostávat upozornění protokolu aktivit pro oznámení služby Azure
description: Nechte se informovat přes zprávu SMS, e-mailu nebo webhooku dojde k službě Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 3b3c967cd43745a4ae87fefc578282f5427a5f79
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405699"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Vytvoření upozornění protokolu aktivit pro oznámení služby
## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak vytvořit upozornění protokolu aktivit pro oznámení o stavu služby s využitím webu Azure portal.  

Můžete obdržíte výstrahu při Azure odesílá oznámení o stavu služby ke svému předplatnému Azure. Můžete nakonfigurovat výstrahu na základě:

- Třída oznámení o stavu služby (Service problémy, plánovaná údržba poradci pro stav).
- Předplatné vliv.
- Služby vliv.
- Oblasti vliv.

> [!NOTE]
> Oznámení o stavu služby neodesílá výstrahu týkající se prostředků události týkající se stavu.

Můžete také konfigurovat Komu musí odesílat výstrahy:

- Vyberte existující skupinu akcí.
- Vytvořte novou skupinu akcí (který lze použít pro budoucí výstrahy).

Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../../azure-monitor/platform/action-groups.md).

Informace o tom, jak nakonfigurovat službu stavu oznámení výstrah pomocí šablon Azure Resource Manageru najdete v tématu [šablon Resource Manageru](alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Podívejte se na video o nastavení prvního upozornění služby Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Skupina akcí oznámení a nové pomocí webu Azure portal
1. V [portál](https://portal.azure.com)vyberte **Service Health**.

    ![Služby "Služba stavu"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. V **výstrahy** vyberte **výstrahy týkající se stavu**.

    ![Na kartě "Výstrahy týkající se stavu"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Vyberte **upozornění na stav služby vytvořit** a přejít k vyplnění polí.

    ![Příkaz "Vytvořit službu stavu upozornění"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Vyberte **předplatné**, **služby**, a **oblastech** chcete být upozorňováni.

    ![Dialogové okno "Přidat upozornění protokolu aktivit."](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Toto předplatné se používá pro uložení upozornění protokolu aktivit. Upozornění prostředek je nasazená s tímto předplatným a sledování událostí v protokolu aktivit.

1. Zvolte **typy událostí** chcete být upozorňováni: *Služba problém*, *plánované údržby*, a *poradci pro stav* 

1. Definujte podrobnosti o vaše upozornění tak, že zadáte **název pravidla upozornění** a **popis**.

1. Vyberte **skupiny prostředků** místo, kam chcete výstrahu, kterou chcete uložit.

1. Vytvořit novou skupinu akcí tak, že vyberete **nová skupina akcí**. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se odkazuje v oznámení, která se posílají, když se aktivuje toto upozornění.

    ![Vytvořit novou skupinu akcí](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definujte seznam příjemců pro příjemce:

    a. **Název**: Zadejte název, alias nebo identifikátor příjemce.

    b. **Typ akce**: Vyberte služby SMS, e-mailu, webhooku, aplikace Azure a další.

    c. **Podrobnosti o**: Závislosti na typu akce zvolili, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, atd.

1. Vyberte **OK** k vytvoření skupiny akcí a poté **vytvořit pravidlo upozornění** dokončete upozornění.

Během několika minut upozornění je aktivní a začne aktivovat na základě podmínek, které jste zadali při vytváření.

Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy pro správu problémů](../../service-health/service-health-alert-webhook-guide.md). Informace o schématu webhooků pro upozornění protokolu aktivit najdete v tématu [upozornění protokolů Webhooky Azure aktivity](../../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akcí, které jsou definované v následujícím postupu je opakovaně použitelný jako existující skupiny akcí pro všechny budoucí upozornění definice.
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Upozornění s existující skupina akcí pomocí webu Azure portal

1. Postupujte podle kroků 1 až 7 v předchozí části, chcete-li vytvořit oznámení o stavu služby. 

1. V části **definujte skupinu akcí**, klikněte na tlačítko **akce výběru skupiny** tlačítko. Vyberte skupinu příslušnou akci.

1. Vyberte **přidat** přidat skupinu akcí a poté **vytvořit pravidlo upozornění** dokončete upozornění.

Během několika minut upozornění je aktivní a začne aktivovat na základě podmínek, které jste zadali při vytváření.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Skupina akcí oznámení a nové pomocí šablon Azure Resource Manageru

Následuje příklad, který vytvoří skupiny akcí s cíli e-mailu a povolí všechna oznámení o stavu služby pro cílové předplatné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Správa výstrah

Po vytvoření upozornění je zobrazeno v **výstrahy** část **monitorování**. Vyberte výstrahu, kterou chcete spravovat:

* Upravte.
* Odstraňte ho.
* Zakázat nebo povolit, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
- Další informace o [osvědčené postupy pro nastavení oznámení služby Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Zjistěte, jak [nastavení pro mobilní nabízená oznámení pro Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy pro správu problémů](../../service-health/service-health-alert-webhook-guide.md).
- Další informace o [služby oznámení o stavu](../../azure-monitor/platform/service-notifications.md).
- Další informace o [rychlosti oznámení](../../azure-monitor/platform/alerts-rate-limiting.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Získat [přehled upozornění protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a zjistěte, jak dostávat upozornění. 
- Další informace o [skupiny akcí](../../azure-monitor/platform/action-groups.md).
