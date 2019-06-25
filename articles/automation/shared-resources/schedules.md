---
title: Plány ve službě Azure Automation
description: Plány služeb automatizace se používají k plánování runbooků ve službě Azure Automation, aby se spouštěla automaticky. Popisuje, jak vytvářet a spravovat plán v tak, aby může automaticky spustit runbook v určitou dobu nebo podle plánu opakování.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128800"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Naplánování runbooku v Azure Automation

Plánování runbooku ve službě Azure Automation ke spuštění v určenou dobu, můžete ho propojit s jedním nebo víc plány. Plán můžete nakonfigurovat na spuštění jednou nebo vyskytovat nadále hodinové nebo denní plán pro sady runbook na portálu Azure portal. Můžete také naplánovat je pro každý týden, měsíc, konkrétní dny v týdnu nebo dny v měsíci nebo konkrétní den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Plány Azure Automation DSC konfigurace aktuálně nepodporují.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů s prostředím PowerShell ve službě Azure Automation. Se dodávají jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Načte plán. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Vytvoří nový plán. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Odstraní plán. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Nastaví vlastnosti pro existující plán. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Načte naplánované runbooky. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Přidruží sady runbook k plánu. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates sady runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Nový plán pro sady runbook můžete vytvořit na webu Azure Portal nebo pomocí Powershellu.

> [!NOTE]
> Při spuštění novou naplánovanou úlohu Azure Automation používá nejnovější moduly ve vašem účtu Automation.  Aby se zabránilo dopadu na vaše sady runbook a procesy, které jsou automatizaci, měli byste otestovat nejprve všechny runbooky, který mají propojené plány se účet Automation, který je vyhrazený pro testování.  Tato operace ověří vaše naplánované runbooky dál fungují správně, a pokud ne, můžete další řešení potíží a použít požadované změny před migrací verze aktualizované sady runbook do produkčního prostředí.
> Svůj účet Automation nezíská automaticky všem novým verzím modulů, pokud jste je aktualizovat ručně tak, že vyberete [aktualizace modulů Azure](../automation-update-azure-modules.md) možnost **moduly**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Chcete-li vytvořit nový plán na webu Azure Portal

1. Na portálu Azure ve svém účtu automation vyberte **plány** části **sdílené prostředky** na levé straně.
2. Klikněte na tlačítko **přidat plán** v horní části stránky.
3. Na **nový plán** podokně zadejte **název** a volitelně **popis** nového plánu.
4. Vyberte, jestli plán se spustí jednorázově nebo podle plánu opakované tak, že vyberete **jednou** nebo **periodický**. Pokud vyberete **jednou** zadat **počáteční čas**a potom klikněte na tlačítko **vytvořit**. Pokud vyberete **periodický**, zadejte **počáteční čas** a pro **opakovat každých**, vyberte frekvenci, jak často chcete sadu runbook opakujte – podle **hodinu**, **den**, **týden**, nebo **měsíc**.
    1. Pokud vyberete **týden**, zadáte seznam dny v týdnu lze vybírat. Vyberte libovolný počet dní, jak chcete. První spuštění vašeho plánu se stane první den vybrané po času zahájení. Například zvolte víkendu plán, zvolte **sobota** a **neděle**.

       ![Nastavení plánu opakování víkendu](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete **měsíc**, jsou uvedeny různé možnosti. Pro **měsíční opakování** , vyberte buď **dny v měsíci** nebo **dnech**. Pokud se rozhodnete **dny v měsíci**, kalendáře, se zobrazí, který umožňuje zvolit libovolný počet dní, jak chcete. Pokud zvolíte datum třeba 31, nedojde v aktuálním měsíci, plán se nespustí. Pokud chcete plán pro spuštění poslední den, zvolte **Ano** pod **spustit poslední den v měsíci**. Pokud se rozhodnete **dnech**, **opakovat každých** možnost se zobrazí. Zvolte **první**, **druhý**, **třetí**, **čtvrtý**, nebo **poslední**. A nakonec zvolte den na opakovat.

       ![Měsíční plán na první patnáctý a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Po dokončení klikněte na tlačítko **vytvořit**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí prostředí PowerShell

Můžete použít [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) rutina pro vytvoření plány. Můžete zadat čas zahájení pro plán a frekvenci, který se má spustit. Následující příklady ukazují, jak vytvořit mnoho scénářů jiný plán.

#### <a name="create-a-one-time-schedule"></a>Vytvoření časového plánu

Následující vzorové příkazy vytvoří jeden časový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření plánu opakování

Následující vzorové příkazy znázorňují postup vytvoření plánu opakování, který spouští každý den v 1:00 hodin na celý rok zdarma.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření plánu týdenního opakování

Následující vzorové příkazy znázorňují postup vytvoření týdenní plán, který spouští pouze ve všechní dny.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření plánu týdenního opakování pro víkendy

Následující vzorové příkazy znázorňují postup vytvoření týdenní plán, který běží na víkendy pouze.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Vytvoření plánu opakování pro funkce first, 15 a poslední den v měsíci

Následující vzorové příkazy znázorňují postup vytvoření plánu opakování, který běží na 1, 15 a poslední den v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Připojení plánu k runbooku

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud runbook obsahuje parametry, můžete zadat hodnoty pro ně. Musíte zadat hodnoty všech povinných parametrů a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění runbooku pomocí tohoto plánu. Můžete připojit stejnou sadu runbook na jiný plán a zadejte jiné hodnoty parametru.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Připojení plánu k runbooku pomocí portálu Azure portal

1. Na portálu Azure ve svém účtu automation vyberte **sady Runbook** části **automatizace procesů** na levé straně.
2. Klikněte na název runbooku, naplánování.
3. Pokud sada runbook není aktuálně propojený s plánu, že nabízí možnost vytvořit nový plán nebo odkaz k existujícímu plánu.
4. Pokud má runbook parametry, můžete vybrat možnost **upravit nastavení spouštění (výchozí: Azure)** a **parametry** podokně se zobrazí, ve kterém můžete zadat informace.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Připojení plánu k runbooku pomocí prostředí PowerShell

Můžete použít [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) rutiny pro připojení plánu. Hodnoty pro parametry runbooku můžete zadat pomocí parametru parametry. Další informace o zadání hodnot parametrů najdete v tématu [spuštění Runbooku ve službě Azure Automation](../automation-starting-a-runbook.md).
Následující vzorové příkazy znázorňují postup připojení plánu k runbooku pomocí rutiny Azure Resource Manageru s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Plánování runbooků častěji

Interval nejčastější plánu ve službě Azure Automation lze nakonfigurovat pro je jedna hodina. Pokud budete potřebovat plány, které se spouští častěji než, existují dvě možnosti:

* Vytvoření [webhooku](../automation-webhooks.md) pro sadu runbook a použití [Azure Scheduleru](../../scheduler/scheduler-get-started-portal.md) pro volání webhooku. Azure Scheduler poskytuje další detailnější členitosti při definování plánu.

* Vytvořte čtyři plány všechna spuštění do 15 minut od sebe navzájem spuštění jednou za hodinu. Tento scénář umožňuje spuštění každých 15 minut pomocí různých plánů sady runbook.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Při zakázání plánu libovolné sady runbook je již propojen se spouští podle tohoto plánu. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti pro plány s frekvencí při jejich vytváření. Po vypršení časového limitu, je plán zakázán.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Zakázání plánu z portálu Azure portal

1. Na portálu Azure ve svém účtu Automation vyberte **plány** části **sdílené prostředky** na levé straně.
2. Klikněte na název plánu a otevřete tak podokno Podrobnosti.
3. Změna **povolené** k **ne**.

> [!NOTE]
> Pokud chcete zakázat plán, který se má počáteční čas v minulosti, musíte změnit počáteční datum před uložením na čas v budoucnosti.

### <a name="to-disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí prostředí PowerShell

Můžete použít [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) rutiny, chcete-li změnit vlastnosti existujícího plánu. Chcete-li zakázat plán, zadejte **false** pro **IsEnabled** parametru.

Následující vzorové příkazy ukazují, jak zakázat plán pro sady runbook pomocí rutiny Azure Resource Manageru.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další postup

* Začínáme se sadami runbook ve službě Azure Automation najdete v článku [spuštění Runbooku ve službě Azure Automation](../automation-starting-a-runbook.md)

