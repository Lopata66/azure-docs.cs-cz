---
title: Vytvoření upozornění Azure Advisor pro nová doporučení pomocí šablony Správce prostředků
description: Vytvořit upozornění Azure Advisor pro nové doporučení
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921075"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Rychlý Start: vytváření Azure Advisor upozornění na nová doporučení pomocí šablony ARM

V tomto článku se dozvíte, jak nastavit upozornění pro nová doporučení od Azure Advisor pomocí šablony Azure Resource Manager (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Kdykoli Azure Advisor zjistí nové doporučení pro jeden z vašich prostředků, uloží se do [protokolu aktivit Azure](/azure/azure-monitor/platform/activity-logs-overview). Pro tyto události můžete nastavit výstrahy z Azure Advisor pomocí prostředí pro vytváření výstrah specifických pro doporučení. Můžete vybrat předplatné a volitelně také skupinu prostředků a určit prostředky, na které chcete dostávat výstrahy.

Můžete také určit typy doporučení pomocí těchto vlastností:

- Kategorie
- Úroveň dopadu
- Typ doporučení

Můžete taky nakonfigurovat akci, která se provede, když se aktivuje výstraha:  

- Výběr existující skupiny akcí
- Vytváří se nová skupina akcí.

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Výstrahy služby Advisor jsou momentálně dostupné jenom pro doporučení vysoké dostupnosti, výkonu a nákladů. Doporučení zabezpečení nejsou podporovaná.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.
- Pokud chcete spustit příkazy z místního počítače, nainstalujte rozhraní příkazového řádku Azure CLI nebo moduly Azure PowerShell. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) a [instalace Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Kontrola šablony

Následující šablona vytvoří skupinu akcí s cílem e-mailu a povolí všechna oznámení o stavu služby pro cílové předplatné. Tuto šablonu uložte jako *CreateAdvisorAlert.jsna*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
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
      "comments": "Azure Advisor Activity Log Alert",
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
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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

Šablona definuje dva prostředky:

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/Upozorněníprotokoluaktivit](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí libovolné standardní metody pro [nasazení šablony ARM](../azure-resource-manager/templates/deploy-portal.md) , jako jsou následující příklady, pomocí rozhraní příkazového řádku a PowerShellu. Nahraďte ukázkové hodnoty pro **skupinu prostředků**a **EmailAddress** příslušné hodnoty pro vaše prostředí. Název pracovního prostoru musí být jedinečný mezi všemi předplatnými Azure.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Ověření nasazení

Ověřte, zda byl pracovní prostor vytvořen pomocí jednoho z následujících příkazů. Nahraďte ukázkové hodnoty pro **skupinu prostředků** hodnotou, kterou jste použili výše.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky ponechat na místě. Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní pravidlo upozornění a související prostředky. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Další kroky

- Získejte [Přehled výstrah protokolu aktivit](../azure-monitor/platform/alerts-overview.md)a Naučte se přijímat výstrahy.
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).
