---
title: Vytvoření, zobrazení a správa aktivit upozornění protokolů ve službě Azure Monitor
description: Postup vytvoření upozornění protokolu aktivit s využitím webu Azure portal šablony Azure Resource Manageru a Azure Powershellu.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: f25321fa5a13ed5a39a62a4115bb0bc10306d36f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244959"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Vytvoření, zobrazení a správa pomocí Azure monitoru upozornění protokolu aktivit  

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivovat při výskytu nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze.

Tyto výstrahy jsou pro prostředky Azure, můžete vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Obvykle je vytvoření upozornění protokolu aktivit pro příjem oznámení, když dojde k určité změny na prostředky ve vašem předplatném Azure, často obor skupiny určitého prostředku nebo prostředku. Například můžete chtít upozorněni, když některý virtuální počítač (ukázkové skupiny prostředků) **myProductionResourceGroup** je odstraněn, nebo můžete chtít získat vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.

> [!IMPORTANT]
> Upozornění na stav služby notification nelze vytvořit pomocí rozhraní pro vytvoření upozornění protokolu aktivit. Další informace o vytváření a používání oznámení o stavu služby, najdete v článku [dostávat upozornění protokolu aktivit na oznámení o stavu služby](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>portál Azure

> [!NOTE]
> 
>  Při vytváření pravidel upozornění, zkontrolujte následující:
> 
> - Předplatné v oboru není liší od předplatného, kde se vytvoří výstraha.
> - Kritéria musí být úroveň/status/volající / skupiny prostředků nebo id prostředku / typ prostředku / kategorie událostí, ve které je nakonfigurována oznámení.
> - Není žádný "anyOf" podmínky nebo vnořené podmínky v konfiguraci výstrahy JSON (v podstatě u žádné další allOf/anyOf je povoleno pouze jeden allOf).
> - Pokud je kategorie "správce". Musíte zadat alespoň jeden z předchozích kritéria v upozornění. Nelze vytvořit výstrahu, která se aktivuje vždy, když se vytvoří událost v protokolech aktivit.

### <a name="create-with-azure-portal"></a>Vytvořit pomocí webu Azure portal

Pomocí následujícího postupu:

1. Z webu Azure portal, vyberte **monitorování** > **výstrahy**
2. Klikněte na tlačítko **nové pravidlo upozornění** v horní části **výstrahy** okna.

     ![Nové pravidlo upozornění](media/alerts-activity-log/AlertsPreviewOption.png)

     **Vytvořit pravidlo** zobrazí se okno.

      ![nové možnosti pravidlo upozornění](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **V části definovat výstražný stav** zadejte následující informace a klikněte na tlačítko **provádí**.

   - **Cíl upozornění:** Chcete-li zobrazit a vybrat cíl pro nové výstrahy, použijte **filtrovat podle předplatného** / **filtrovat podle typu prostředku** a ze zobrazeného seznamu vyberte prostředek nebo skupinu prostředků.

     > [!NOTE]
     > 
     > můžete vybrat prostředek, skupinu prostředků nebo celé předplatné pro signálu protokolu aktivit.

     **Cíl ukázkové zobrazení výstrahy**
     ![vyberte cíl](media/alerts-activity-log/select-target.png)

   - V části **cílová kritéria**, klikněte na tlačítko **přidat kritéria** a jsou zobrazeny všechny dostupné signály pro cíl, včetně těch, které z různých kategorií **protokolu aktivit**; nebyla úspěšná Název kategorie připojí v **Monitor Service** název.

   - Vyberte signál, který se v seznamu zobrazí různých operací možné pro daný typ **protokolu aktivit**.

     Můžete vybrat na časové ose historie protokolu a odpovídající logika upozornění pro tento cíl signál:

     **Přidání obrazovky kritéria**

     ![Přidat kritéria](media/alerts-activity-log/add-criteria.png)

     **Historie čas**: Události, které jsou k dispozici pro vybranou operaci je mohou být vykreslena za posledních 6/12/24 hodin (nebo) za poslední týden.

     **Upozornění logiky**:

     - **Úroveň události**– úroveň závažnosti události. _Podrobné_, _informační_, _upozornění_, _chyba_, nebo _kritické_.
     - **Stav**: Stav události. _Spuštění_, _nepovedlo_, nebo _úspěšné_.
     - **Zahájit událost**: Označuje se taky jako volajícího E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

       Ukázkový graf signál s logika upozornění použít:

       ![ kritéria vybraná](media/alerts-activity-log/criteria-selected.png)

4. V části **definujte podrobnosti pravidla upozornění**, zadejte následující podrobnosti:

    - **Název pravidla upozornění** – název nové pravidlo upozornění
    - **Popis** – popis pro nové pravidlo upozornění
    - **Uložit upozornění do skupiny prostředků** – vyberte skupinu prostředků, ve které chcete uložit toto nové pravidlo.

5. V části **skupiny akcí**, z rozevírací nabídky, určete skupinu akcí, které chcete přiřadit k této nové pravidlo upozornění. Alternativně [vytvořit novou skupinu akcí](../../azure-monitor/platform/action-groups.md) a přiřadit nové pravidlo. Chcete-li vytvořit novou skupinu, klikněte na tlačítko **+ nová skupina**.

6. Po vytvoření povolit pravidla, klikněte na tlačítko **Ano** pro **Povolit pravidlo po vytvoření** možnost.
7. Klikněte na tlačítko **vytvořit pravidlo upozornění**.

    Vytvořit nové pravidlo upozornění protokolu aktivit a potvrzovací zpráva se zobrazí v horní části napravo od okna.

    Můžete povolit, zakázat, úprava nebo odstranění pravidla. Další informace o správě pravidel protokolů aktivit.


Alternativně je jednoduchý přirovnání pro vysvětlení podmínky, na kterých se dají vytvořit pravidla upozornění na protokol aktivit, prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure portal](activity-log-view.md#azure-portal). Ve službě Azure Monitor – protokol aktivit, jeden můžete filtrovat nebo najít potřebné události a pak vytvořte výstrahu pomocí **přidat upozornění protokolu aktivit** tlačítko; potom postupujte podle kroků 4 a vyšší jak je uvedeno v kurzu výše.
    
 ![ Přidat upozornění protokolu aktivit](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Umožňuje zobrazit a spravovat na webu Azure portal

1. Z webu Azure portal, klikněte na tlačítko **monitorování** > **výstrahy** a klikněte na tlačítko **spravovat pravidla** v levém horním rohu okna.

    ![ Spravovat pravidla výstrah](media/alerts-activity-log/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidla.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![ Hledat pravidla upozornění protokolu aktivit](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Můžete použít k dispozici tyto filtry – _předplatné_, _skupiny prostředků_, _prostředků_, _typ signálu_, nebo _stav_  najít pravidlo aktivit, které chcete upravit.

   > [!NOTE]
   > 
   > Můžete upravit pouze **popis** , **cílová kritéria** a **skupiny akcí**.

3. Vyberte pravidlo a dvakrát klikněte pro úpravu možností pravidla. Proveďte požadované změny a pak klikněte na tlačítko **Uložit**.

   ![ Spravovat pravidla výstrah](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Zakázání, povolení nebo odstranění pravidla. Vyberte příslušnou možnost v horní části okna, po výběru pravidla, jak je uvedeno v kroku 2.


## <a name="azure-resource-template"></a>Šablony Azure Resource
Pokud chcete vytvořit upozornění protokolu aktivit s využitím šablony Resource Manageru, vytvoříte prostředek typu `microsoft.insights/activityLogAlerts`. Potom vyplňte všechny související vlastnosti. Zde je šablona, která vytvoří upozornění protokolu aktivit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Výše uvedené ukázky json se dají uložit jako (Řekněme) sampleActivityLogAlert.json pro účely Tento názorný postup vás provede a je možné nasadit s použitím [Azure Resource Manageru na webu Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Může trvat až 5 minut nové pravidlo upozornění protokolu aktivit aktivuje

## <a name="rest-api"></a>REST API 
[Azure Monitor – protokol aktivit výstrahy API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) je rozhraní REST API a plně kompatibilní s rozhraním REST API Azure Resource Manageru. Proto může sloužit prostřednictvím Powershellu pomocí rutiny Resource Manageru a Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Nasazení šablony Resource Manageru pomocí Powershellu
Použití Powershellu k nasazení ukázkové šablony prostředků uvedené v předchozí [prostředků šablony části] (# – šablony resource Manageru –, použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Pokud sampleActivityLogAlert.parameters.json obsahuje hodnoty poskytnutý pro parametry potřebné pro vytvoření pravidla upozornění.

### <a name="use-activity-log-powershell-cmdlets"></a>Použití protokolu aktivit rutin prostředí PowerShell

Upozornění protokolu aktivit mít vyhrazené rutiny Powershellu, které jsou k dispozici:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0) : Vytvoří novou nebo aktualizovat existující upozornění protokolu aktivit.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0) : Získá jeden nebo více aktivit prostředky upozornění protokolů.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0) : Umožňuje existujícího upozornění protokolu aktivit a nastaví její klíčová slova.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0) : Zakáže existujícího upozornění protokolu aktivit a nastaví její klíčová slova.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    : Odebere upozornění protokolu aktivit.

## <a name="cli"></a>Rozhraní příkazového řádku

Vyhrazené příkazy rozhraní příkazového řádku Azure v rámci sady [upozornění protokolu aktivit monitorování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) jsou dostupné pro správu pravidla upozornění protokolu aktivit.

Pokud chcete vytvořit nové pravidlo upozornění protokolu aktivit, použijte v tomto pořadí:

1. [Vytvoření upozornění protokolu aktivit monitorování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Vytvořit nový prostředek pravidlo upozornění protokolu aktivit
1. [upozornění oboru protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Přidat obor pro pravidel upozornění protokolů vytvořené aktivity
1. [AZ monitor protokolu aktivit výstrahy – skupina akcí](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Přidat skupinu akcí pro pravidlo upozornění protokolu aktivit

K načtení některý pravidlo upozornění protokolu aktivit z prostředků, pomocí příkazu Azure CLI [az monitor protokolu aktivit upozornění zobrazit](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) lze použít. A pro zobrazení všech prostředků pravidlo upozornění protokolu aktivit ve skupině prostředků, použijte [seznam výstrah protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Prostředky pravidlo upozornění protokolu aktivit můžete odebrat pomocí příkazového řádku Azure [odstranění upozornění protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Další postup

- [Schéma Webhooku pro protokoly aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Přehled protokolů aktivit](../../azure-monitor/platform/activity-log-alerts.md) 
- Další informace o [skupiny akcí](../../azure-monitor/platform/action-groups.md).  
- Další informace o [služby oznámení o stavu](../../azure-monitor/platform/service-notifications.md).
