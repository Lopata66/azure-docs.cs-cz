---
title: Spuštění Runbooku ve službě Azure Automation
description: Popisuje podrobnosti o zpracování sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/18/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a2efc90e14180cd2b26223ef968a7f192b440ebd
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287039"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění Runbooku ve službě Azure Automation

Při spuštění runbooku ve službě Azure Automation, se vytvoří úloha. Úloha je instance jednoho spuštění Runbooku. Pracovník s Azure Automation se přiřadí ke spuštění každé úlohy. Když zaměstnanci sdílí mnoho účtů Azure, úlohy z různých účtů Automation jsou od sebe. Nemáte máte kontrolu, nad kterou pracovního procesu služby žádost pro vaši úlohu. Jeden runbook může mít mnoho úloh spuštěných současně. Spouštěcí prostředí pro úlohy ve stejném účtu Automation může znovu použít. Další úlohy spuštění ve stejnou dobu, tím častěji může odesílat do stejného izolovaného prostoru. Úlohy spuštěné ve stejném izolovaném prostoru procesu může ovlivnit mezi sebou, jedním z příkladů je spuštěna `Disconnect-AzureRMAccount` rutiny. Spuštění této rutiny by odpojit každé úlohy runbooku ve sdílené izolovaného prostoru procesu. Při zobrazení seznamu sad runbook na portálu Azure portal, zobrazí stav všech úloh, které byly spuštěny pro každou sadu runbook. Zobrazí se seznam úloh pro každou sadu runbook, chcete-li sledovat stav každého. Protokoly úlohy jsou uloženy pro mít délku maximálně 30 dnů. Popis stavy různé úlohy [stavy úlohy](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks), [grafické runbooky](automation-runbook-types.md#graphical-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy - pracovního postupu Powershellu](./media/automation-runbook-execution/job-statuses.png)

Úlohy mají přístup k prostředkům Azure tím, že připojení k předplatnému Azure. Pouze mají přístup k prostředkům ve vašem datovém centru Pokud tyto prostředky jsou přístupné z veřejného cloudu.

## <a name="where-to-run-your-runbooks"></a>Kde spustit své sady runbook

Runbooky ve službě Azure Automation můžete spustit v jedné izolovaný prostor v Azure nebo [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Izolovaný prostor je sdílená prostředí v Azure, které mohou být využívána více úloh. Úlohy pomocí stejné izolovaného prostoru zavazujete se k jejich omezení prostředků izolovaného prostoru. Hybridní pracovní procesy Runbooku můžete spustit runbooky přímo na počítači, který je hostitelem role a s prostředky v prostředí ke správě místních prostředků. Sady Runbook jsou uloženy a spravovaná ve službě Azure Automation a pak doručí jeden nebo více počítačů přiřazených. Většina sad runbook můžete snadno spouštět v Azure izolovaných prostorů. Existují konkrétní scénáře, ve kterém výběr Hybrid Runbook v Azure izolovaný prostor ke spuštění vaší sady runbook mohou být doporučeny. Najdete v následující tabulce najdete seznam nějaké ukázkové scénáře:

|Úkol|Nejlepší volbou|Poznámky|
|---|---|---|
|Integrace s prostředky Azure|Azure Sandbox|Hostované v azure, ověřování je jednodušší. Pokud používáte Hybrid Runbook Worker na Virtuálním počítači Azure, můžete použít [spravovaných identit pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Zajištění optimálního výkonu ke správě prostředků azure|Azure Sandbox|Skript je spuštěn ve stejném prostředí, která naopak má nižší latence|
|Minimalizovat provozní náklady|Azure Sandbox|Neexistuje žádné režijní náklady na výpočetní prostředky, není nutné pro virtuální počítač|
|Dlouho běžící skript|Hybrid Runbook Worker|Máte Azure sandboxy [omezení prostředků](../azure-subscription-service-limits.md#automation-limits)|
|Interakci s místním službám|Hybrid Runbook Worker|Můžete mít přístup přímo na hostitelském počítači|
|3. software jiného výrobce a spustitelné soubory|Hybrid Runbook Worker|Spravovat operační systém a mohou instalovat software|
|Monitorovat soubor nebo složka s sady runbook|Hybrid Runbook Worker|Použití [úlohy sledovacího procesu](automation-watchers-tutorial.md) v procesu Hybrid Runbook worker|
|Náročné na skriptu prostředků|Hybrid Runbook Worker| Máte Azure sandboxy [omezení prostředků](../azure-subscription-service-limits.md#automation-limits)|
|Používání modulů s konkrétním požadavkům| Hybrid Runbook Worker|Tady je několik příkladů:</br> **WinSCP** -závislost na winscp.exe </br> **IISAdministration** -IIS musí být povolen|
|Instalace modulu, který vyžaduje Instalační program|Hybrid Runbook Worker|Moduly pro izolovaný prostor musí být xcopyable|
|Pomocí runbooky a moduly, které vyžadují rozhraní .NET Framework liší od 4.7.2|Hybrid Runbook Worker|Sandboxy Automation máte rozhraní .NET Framework 4.7.2 a neexistuje žádný způsob, jak upgradovat|
|Skripty, které vyžadují ke zvýšení úrovně oprávnění|Hybrid Runbook Worker|Sandboxy neumožňují zvýšení oprávnění. Tento problém vyřešit, použijte Hybrid Runbook Worker a můžete ji vypnout nástroje Řízení uživatelských účtů a použití `Invoke-Command` při spuštění příkazu, který vyžaduje zvýšení oprávnění|
|Skripty, které vyžadují přístup k rozhraní WMI|Hybrid Runbook Worker|Úlohy spuštěné v izolovaných prostorů cloudu [nemají přístup k rozhraní WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Chování sady Runbook

Spuštění sady Runbook na základě logiky, která je definována v nich obsažené. Pokud dojde k přerušení sady runbook, runbook restartuje na začátek. Toto chování vyžaduje sady runbook k zapsání způsobem, které podporují právě restartován, pokud byly nějaké přechodné problémy.

### <a name="creating-resources"></a>Vytváření prostředků

Pokud váš skript vytvoří prostředky, zkontrolujte jestli prostředek už existuje než se pokusíte vytvořit znovu. Základním příkladu je vidět v následujícím příkladu:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Čas závislých skriptů

Při autorizaci sad runbook je třeba pečlivě zvážit. Jak už bylo zmíněno dříve, sady runbook musí být vytvořená tak, že jsou to robustní a dokáže zpracovat přechodné chyby, které může způsobit, že runbook k restartování nebo selhat. Pokud sadu runbook nezdaří, zopakuje se. Pokud obvykle v rámci omezení času spuštění sady runbook, logiku a zkontrolujte, že čas spuštění by měla být implementována v sadě runbook, aby operace, jako je spuštění, vypnutí nebo horizontální navýšení kapacity jsou spouštěny pouze v určité době.

### <a name="tracking-progress"></a>Sledování průběhu

Je vhodné ne autorizaci sad runbook, aby ze své podstaty modulární. To znamená, že strukturování logiky v sadě runbook, takže můžete opakovaně používat a snadného. Sledování pokroku v sadě runbook je dobrým způsobem, jak zajistit, že logiky v sadě runbook se spustí správně Pokud byly nějaké problémy. Některé možné způsoby, jak můžete sledovat postup prací sady runbook je pomocí externího zdroje, jako je například účty úložiště, databáze nebo sdílené soubory. Díky sledování stavu externě, můžete vytvořit logiku v sadě runbook, do první kontrola stavu poslední akci, kterou trvalo sady runbook. Potom založených na výsledcích buď přeskočit nebo pokračovat konkrétní úlohy v sadě runbook.

### <a name="prevent-concurrent-jobs"></a>Zabránit souběžných úloh

Některé sady runbook může se chovat podivné spuštěné mezi několika úlohami ve stejnou dobu. V takovém případě je potřeba implementovat logiku ke kontrole zjistíte, pokud sada runbook již má s probíhající úlohou. V následujícím příkladu můžete vidět základní příklad, jak může dělat toto chování:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Práce s několika předplatnými

Při autorizaci sad runbook, který řešení s více předplatnými, vaše sada runbook vyžaduje použití [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) rutiny Ujistěte se, že váš kontext ověřování není načtena z jiného runbooku, který může být spuštěný ve stejném izolovaném prostoru. Pak budete muset použít `-AzureRmContext` parametr na vaši `AzureRM` rutiny a předejte jí potřebný kontext.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Zpracování výjimek

Při vytváření skriptů, je důležité mít možnost pro zpracování výjimek a potenciální krátkodobých selhání. Následují některé různé způsoby, jak zpracovat výjimky nebo přerušované problémy ve svých runboocích:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) preferenční proměnné určuje, jak Powershellu reaguje na neukončující chyba. Ukončující chyby nejsou ovlivněny `$ErrorActionPreference`, vždy ukončit. S použitím `$ErrorActionPreference`, obvykle neukončující chybě, jako jsou `PathNotFound` z `Get-ChildItem` rutiny se zastavit sadu runbook dokončit. Následující příklad ukazuje použití `$ErrorActionPreference`. Finální `Write-Output` řádku se nikdy neprovede, protože skript se zastaví.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Nakonec Try Catch

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) se používá v Powershellové skripty umožňují zpracování chyb, které ukončujícího znaku. Pomocí Try Catch může zachytit specifické výjimky nebo obecné výjimky. Příkaz Catch by měl použít ke sledování chyb nebo využit chybu zpracovat. Následující příklad se pokusí stáhnout soubor, který neexistuje. Zachycena `System.Net.WebException` výjimku, pokud se vrátí poslední hodnotu jiného výjimka.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>vyvolání výjimky

[Vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) lze použít ke generování dojde k ukončující chybě. To může být užitečné při definování vlastní logiky v sadě runbook. Pokud je splněna určitá kritéria, která by se měla zastavit skript, můžete použít `throw` zastavit skriptu. Následující příklad ukazuje machine parametr funkce vyžaduje použití `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Pomocí spustitelné soubory nebo volání procesy

Sady Runbook spouštět v Azure sandboxy nepodporuje volání procesy (například .exe nebo subprocess.call). Je to proto, že Azure izolovaných prostorů jsou sdílené procesů spuštěných v kontejnerech, které nemusí mít přístup k základní rozhraní API. Pro scénáře, kdy potřebujete 3. software jiného výrobce nebo voláním procedury sub procesy, se doporučuje spustit sadu runbook na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Vlastnosti zařízení a aplikací

Úlohy sady Runbook spouštět v Azure sandboxy nemají přístup k vlastnosti jakékoli zařízení nebo aplikace. Nejčastěji používané rozhraním API, které se používá na metriky výkonu dotazů na Windows je rozhraní WMI. Jsou některé z těchto běžné metriky využití paměti a procesoru. Nicméně, není důležité, co se používá rozhraní API. Úlohy běžící v cloudu nemáte příslušná oprávnění Microsoft provádění Web založený (WBEM Enterprise Management), které je postavené na CIM Common Information Model (), což je oborový standard pro definování vlastnosti zařízení a aplikací.

## <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje různé stavy, které můžou u úlohy nastat. Prostředí PowerShell má dva typy chyb, ukončujícími a neukončujícími chybami. Chyby se ukončuje nastavit stav sady runbook **neúspěšné** Pokud k nim dojde. Neukončující chyby povolit skriptu pokračovat i po jejich výskytu. Příklad neukončující chyba používá `Get-ChildItem` rutiny s cestou, která neexistuje. Prostředí PowerShell vidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Tato chyba nebude nastavit stav sady runbook **neúspěšné** a může být označen jako **dokončeno**. Chcete-li vynutit sady runbook a zastavit na neukončující chybě, můžete použít `-ErrorAction Stop` na rutiny.

| Status | Popis |
|:--- |:--- |
| Dokončení |Úloha se úspěšně dokončila. |
| Selhalo |Pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md), sada runbook se nepodařilo zkompilovat. Pro [Powershellový skript sady runbook](automation-runbook-types.md), se nepodařilo spustit sadu runbook nebo úloha měla výjimku. |
| Se nezdařilo, čekání na prostředky |Úloha se nezdařila, protože bylo dosaženo [spravedlivé sdílení](#fair-share) omezit třikrát a spustit pokaždé, když ze stejného kontrolního bodu nebo od začátku runbooku. |
| Ve frontě |Úloha čeká, než budou dostupné prostředky pracovního procesu Automatizace, aby se dala spustit. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a spouštění systému. |
| Obnovení |Systém obnovuje úlohy poté, co bylo pozastaveno. |
| Spuštěno |Úloha je spuštěná. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože bylo dosaženo [spravedlivé sdílení](#fair-share) limit. Bude pokračovat i za chvíli od svého posledního kontrolního bodu. |
| Zastaveno |Úlohu uživatel zastavil před tím, než se dokončila. |
| Zastavování |V systému Probíhá zastavování úlohy. |
| Pozastaveno |Úlohu pozastavil uživatel, systém nebo příkaz v Runbooku. Pokud sada runbook neobsahuje kontrolní bod, začne od začátku runbooku. Pokud má kontrolní bod, můžete začít znovu a pokračovat od svého posledního kontrolního bodu. Sada runbook je pouze pozastaveno systémem, když dojde k výjimce. Ve výchozím nastavení, ErrorActionPreference nastavená na **pokračovat**, což znamená, že úloha běžel na chybu. Pokud je tato předvolba proměnné nastavená **Zastavit**, pak v případě chyby pozastaví úlohu. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |
| Pozastavování |Systém se pokouší pozastavit úlohu na žádost uživatele. Sada runbook dosažení následujícího kontrolního bodu předtím, než je možné ho pozastavit. Pokud už uplynulé svého posledního kontrolního bodu, pak dokončí předtím, než je možné ho pozastavit. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy na webu Azure Portal

Můžete zobrazit souhrnný stav všech úloh sady runbook nebo přejdete na podrobnosti o konkrétní runbooku na portálu Azure portal. Můžete také konfigurovat integraci s pracovního prostoru Log Analytics předávat datové proudy úlohy stavu a úlohu runbooku. Další informace o integraci se službou Azure Monitor protokoly najdete v tématu [dál stavu úlohy a datové proudy úlohy ze služby Automation na protokoly Azure monitoru](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Souhrn úlohy runbooků služby Automation

Na pravé straně vybraný účet Automation, zobrazí se souhrn všech úloh sad runbook v rámci **statistiky úlohy** dlaždici.

![Dlaždice statistiky úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Na této dlaždici se zobrazuje počet a grafické vyjádření stavu úlohy pro všechny úlohy provést.

Uvede počet kliknutí na dlaždici **úlohy** stránky, která obsahuje souhrnný seznam všech úloh provedeny. Tato stránka zobrazuje stav, časy zahájení a dokončení časy.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem **filtrování úloh** a jsou filtrované na konkrétní sadu runbook, stav úloh, nebo z rozevíracího seznamu a časový rozsah má hledat.

![Filtr stavu úlohy](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativně můžete zobrazit podrobnosti souhrnu úlohy pro konkrétní sadu runbook tak, že vyberete dané sady runbook z **sady Runbook** stránky ve vašem účtu Automation a pak vyberte **úlohy** dlaždici. Tato akce představuje **úlohy** stránky, a z něj můžete kliknout na úlohu záznam, který chcete zobrazit podrobnosti a výstup.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Souhrn úlohy

Můžete zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní sadu runbook a jejich aktuální stav. Můžete filtrovat tento seznam podle stavu úlohy a rozsahu dat poslední změny úlohy. Chcete-li zobrazit podrobné informace a výstup, klikněte na název úlohy. V podrobném zobrazení úlohy najdete hodnoty parametrů Runbooku poskytnuté pro tuto úlohu.

Úlohy Runbooku můžete zobrazit takto.

1. Na webu Azure Portal, vyberte **automatizace** a potom vyberte název účtu Automation.
2. Z centra, vyberte **sady Runbook** a pak na **sady Runbook** stránky ze seznamu vybrat sadu runbook.
3. Na stránce pro vybranou sadu runbook, klikněte na tlačítko **úlohy** dlaždici.
4. Klikněte na jednu z úloh v seznamu a na stránce podrobností úlohy runbooku můžete zobrazit podrobnosti a výstup.

## <a name="retrieving-job-status-using-windows-powershell"></a>Načtení stavu úlohy pomocí prostředí Windows PowerShell

Můžete použít [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) můžete načíst úlohy vytvořené pro runbook a podrobnosti konkrétní úlohy. Pokud runbook spustíte s Windows Powershellem pomocí [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), vrátí se výsledná úloha. Použití [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) výstupu úlohy.

Následující ukázkové příkazy z poslední úlohu ukázkového runbooku načíst a zobrazit její stav, hodnot poskytnutých parametrů runbooku a výstup z úlohy.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí všechny záznamy. V případě, že došlo k výjimce pro jeden ze záznamů, výjimka je zapsán místo hodnoty. Toto chování je užitečné jako výjimky může poskytovat doplňující informace, které nemusejí být zaznamenány obvykle při výstupu.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Získejte informace od protokolů aktivit

Další podrobnosti, jako je osoba nebo účet, který spustil sadu runbook můžete získat z protokolu aktivit pro účet automation. Následující příklad Powershellu poskytuje poslední uživatel ke spuštění sady runbook dotyčný:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Spravedlivé sdílení

Azure Automation ke sdílení prostředků mezi všechny runbooky v cloudu, dočasně uvolní nebo jakoukoli úlohu, která byla spuštěna déle než tři hodiny se zastaví. Úlohy pro [sady runbook pomocí prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) a [runbooky Python](automation-runbook-types.md#python-runbooks) se zastaví a ne restartuje, a stav úlohy zobrazuje zastaveno.

Pro dlouho běžící úlohy, doporučuje se použít [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Procesy hybrid Runbook Worker není omezena spravedlivé sdílení a nemají omezení můžete spustit na jak dlouho sady runbook. Další úlohy [omezení](../azure-subscription-service-limits.md#automation-limits) platí pro Azure karantény a procesy Hybrid Runbook Worker. Zatímco proces Hybrid Runbook Worker nejsou omezeny limit spravedlivé sdílení 3 hodiny, by měl být sady runbook spouštět na nich vyvinutý za účelem podpory chování restartování z neočekávané místní infrastruktury problémy.

Další možností je Optimalizujte sady runbook pomocí runbooků. Pokud vaše sada runbook prochází stejnou funkci na několik prostředků, jako je například databázová operace na několik databází, můžete přesunout na tuto funkci [podřízeného runbooku](automation-child-runbooks.md) a volání s [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) rutiny. Každá z těchto podřízených runbooků spustí paralelně v oddělených procesech. Toto chování snižuje celkovou dobu pro nadřazený runbook k dokončení. Můžete použít [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) rutina v sadě runbook, a zkontrolujte stav úlohy pro každý podřízený prvek, zda operace, které provádějí po dokončení podřízeného runbooku.

## <a name="next-steps"></a>Další postup

* Další informace o různých metodách, které můžete použít ke spuštění sady runbook ve službě Azure Automation najdete v tématu [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md)

