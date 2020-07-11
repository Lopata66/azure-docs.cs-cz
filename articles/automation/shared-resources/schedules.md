---
title: Spravovat plány v Azure Automation
description: V tomto článku se dozvíte, jak vytvořit a pracovat s plánem v Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8bd988029b8d78a29de38e995c36ee1860d8cda9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187349"
---
# <a name="manage-schedules-in-azure-automation"></a>Spravovat plány v Azure Automation

Pokud chcete naplánovat spuštění Runbooku v Azure Automation v zadanou dobu, můžete ho propojit s jedním nebo více plány. Plán se dá nakonfigurovat tak, aby se spouštěl buď jednou, nebo podle opakovaného hodinového nebo denního plánu pro Runbooky v Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo určitý den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Azure Automation podporuje letní čas a plánuje je vhodným způsobem pro operace automatizace.

> [!NOTE]
> V plánech aktuálně nejsou povoleny Azure Automation konfigurací DSC.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Rutiny PowerShellu používané pro přístup k plánům

Rutiny v následující tabulce vytvářejí a spravují plány automatizace pomocí PowerShellu. Dodávají se jako součást [AZ moduls](modules.md#az-modules). 

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Načte plán. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Načte naplánované Runbooky. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Vytvoří nový plán. |
| [Registrovat – AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Přidruží sadu Runbook k plánu. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Odebere plán. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Nastaví vlastnosti pro existující plán. |
| [Zrušit registraci – AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissociates sadu Runbook z plánu. |

## <a name="create-a-schedule"></a>Vytvořit plán

Můžete vytvořit nový plán pro Runbooky v Azure Portal nebo pomocí PowerShellu. Abyste se vyhnuli vlivu na vaše Runbooky a procesy, které automatizují, měli byste nejprve otestovat všechny Runbooky s propojenými plány s účtem služby Automation vyhrazeným pro testování. Test ověří, zda budou naplánované Runbooky nadále fungovat správně. Pokud se zobrazí problém, můžete řešit problémy a před migrací aktualizované verze Runbooku do produkčního prostředí provést nějaké změny.

> [!NOTE]
> Váš účet Automation automaticky nezíská žádné nové verze modulů, pokud jste je neaktualizovali ručně, a to tak, že vyberete možnost [aktualizovat moduly Azure](../automation-update-azure-modules.md) z **modulů**. Azure Automation používá nejnovější moduly v účtu Automation při spuštění nové naplánované úlohy. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Vytvořit nový plán v Azure Portal

1. V Azure Portal v účtu Automation na levé straně v části **sdílené prostředky** vyberte **plány** .
1. V horní části stránky vyberte **Přidat plán** .
1. V podokně **Nový plán** zadejte název a volitelně zadejte popis nového plánu.
1. Vyberte, jestli se plán spouští jednou, nebo na základě plánu **opakování**, a to tak, že vyberete **jednou** nebo znovu. Pokud vyberete jednu z **nich, zadejte**čas spuštění a pak vyberte **vytvořit**. Pokud vyberete možnost **opakovaná**, zadejte čas spuštění. U **každého opakování**vyberte, jak často se má sada Runbook opakovat. Vyberte podle hodin, dnů, týdnů nebo měsíců.
    1. Pokud vyberete možnost **týden**, zobrazí se dny v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění vašeho plánu nastane první den, který se vybere po počátečním čase. Chcete-li například vybrat víkendový plán, vyberte možnost sobota a neděle.
    
       ![Nastavování opakovaného plánu na víkend](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete možnost **month (měsíc**), budete mít k disdílně různé možnosti. Pro možnost **měsíčních výskytů** vyberte buď **dny v měsíci** nebo **dny v týdnu**. Pokud vyberete **dny v měsíci**, zobrazí se kalendář, abyste si mohli vybrat tolik dní, kolik chcete. Pokud zvolíte datum, například 31, ke kterému nedochází v aktuálním měsíci, plán se nespustí. Pokud chcete, aby plán běžel za poslední den, vyberte v části **Spustit poslední den v měsíci**možnost **Ano** . Pokud vyberete **dny v týdnu**, zobrazí se **všechny možnosti opakování** . Vyberte **první**, **druhý**, **třetí**, **čtvrtý**nebo **Poslední**. Nakonec vyberte den, kdy se má opakovat.

       ![Měsíční plán na prvních, patnáct a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

1. Až budete hotovi, vyberte **vytvořit**.

### <a name="create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

K vytvoření plánů použijte rutinu [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) . Zadejte čas spuštění plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánování.

#### <a name="create-a-one-time-schedule"></a>Vytvoření jednorázového plánu

Následující příklad vytvoří Jednorázový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření plánu opakování

Následující příklad ukazuje, jak vytvořit opakovaný plán, který se spouští každý den v 1:00./odp. za rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření týdenního opakovaného plánu

Následující příklad ukazuje, jak vytvořit týdenní plán, který se spouští pouze v pracovních dnech.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření týdenního opakovaného plánu pro víkendy

Následující příklad ukazuje, jak vytvořit týdenní plán, který běží pouze na víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Vytvoření opakovaného plánu pro první, patnáctý a poslední den v měsíci

Následující příklad ukazuje, jak vytvořit opakovaný plán, který běží na prvním, patnáctém a posledním dni v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Propojení plánu k sadě Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud sada Runbook obsahuje parametry, můžete pro ně zadat jejich hodnoty. Je nutné zadat hodnoty pro všechny povinné parametry a také můžete zadat hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění sady Runbook tímto plánem. Stejný Runbook můžete připojit k jinému plánu a zadat jiné hodnoty parametrů.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Připojení plánu k sadě Runbook pomocí Azure Portal

1. V Azure Portal na svém účtu Automation v části **Automatizace procesu**vyberte **Runbooky** .
1. Vyberte název Runbooku, který chcete naplánovat.
1. Pokud sada Runbook není aktuálně propojená s plánem, nabídne se vám možnost vytvořit nový plán nebo propojit s existujícím plánem.
1. Pokud má Runbook parametry, můžete vybrat možnost **Upravit nastavení spouštění (výchozí: Azure)** a zobrazí se podokno **parametry** . Sem můžete zadat informace o parametrech.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Připojení plánu k Runbooku pomocí PowerShellu

K propojení plánu použijte rutinu [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) . Parametry Runbooku můžete zadat pomocí parametru Parametry . Další informace o tom, jak zadat hodnoty parametrů, najdete [v tématu Spuštění Runbooku v Azure Automation](../start-runbooks.md).
Následující příklad ukazuje, jak propojit plán k sadě Runbook pomocí rutiny Azure Resource Manager s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Plánování spouštění Runbooků častěji

Častý interval, pro který je možné nakonfigurovat plán ve Azure Automation, je jedna hodina. Pokud vyžadujete, aby se plány spouštěly častěji než to, máte dvě možnosti:

* Vytvořte [Webhook](../automation-webhooks.md) pro Runbook a použijte [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) pro volání Webhooku. Azure Logic Apps poskytuje přesnější členitost k definování plánu.

* Vytvořte čtyři plány, které začínají během 15 minut od sebe, a každý den se spustí jednou za hodinu. Tento scénář umožňuje spuštění sady Runbook každých 15 minut s různými plány.

## <a name="disable-a-schedule"></a>Zakázat plán

Když plán zakážete, na tento plán se už nespustí žádná sada Runbook, na kterou se odkazuje. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti plánů s frekvencí při jejich vytváření. Po dosažení doby vypršení platnosti je plán zakázán.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Zakázat plán z Azure Portal

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **plány** .
1. Vyberte název plánu a otevřete tak podokno podrobností.
1. Změňte možnost **povoleno** na **ne**.

> [!NOTE]
> Pokud chcete zakázat plán, který má čas začátku v minulosti, musíte změnit počáteční datum na čas v budoucnosti před tím, než ho uložíte.

### <a name="disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí PowerShellu

Pomocí rutiny [set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) můžete změnit vlastnosti stávajícího plánu. Pokud chcete plán zakázat, zadejte pro parametr hodnotu false `IsEnabled` .

Následující příklad ukazuje, jak zakázat plán pro sadu Runbook pomocí rutiny Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Odebrat plán

Až budete připraveni k odebrání plánů, můžete použít Azure Portal nebo PowerShell. Mějte na paměti, že můžete odebrat jenom plán, který je zakázaný, jak je popsané v předchozí části.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Odebrání plánu pomocí Azure Portal

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **plány** .
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Klikněte na **Odstranit**.

### <a name="remove-a-schedule-with-powershell"></a>Odebrání plánu pomocí PowerShellu

Pomocí `Remove-AzAutomationSchedule` rutiny, jak je znázorněno níže, můžete odstranit stávající plán. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách, které se používají pro přístup k plánům, najdete v tématu [Správa modulů v Azure Automation](modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).
