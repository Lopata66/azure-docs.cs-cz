---
title: Nakonfigurujte skripty před a po nasazení správy aktualizací v Azure
description: Tento článek popisuje, jak konfigurovat a spravovat před a po skriptů pro nasazení aktualizací
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84df04a6d3fbd634524d3819657860c6a3448d65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499802"
---
# <a name="manage-pre-and-post-scripts"></a>Spravovat skripty před a po

Skripty před a po umožňují spustit Powershellové runbooky ve vašem účtu Automation před (předběžné úkolu) a po nasazení (po úloh) aktualizace. Skripty před a po spuštění v rámci Azure a ne místně. Předběžné skripty se spouští na začátku nasazení aktualizace. Spustit skripty příspěvek na konci nasazení a po žádné restartování počítače, které jsou nakonfigurované.

## <a name="runbook-requirements"></a>Požadavky na sady Runbook

Sady runbook má být použit jako před nebo po skriptu že runbook bude potřebovat lze importovat do účtu automation a publikovaná. Další informace o tomto procesu najdete v tématu [publikování runbooku](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Pomocí předzálohovacího nebo pozálohovacího skriptu

Použít po předem a po skript v nasazení aktualizací, začněte tím, že vytvoření nasazení aktualizace. Vyberte **předzálohovacích skriptů a skripty příspěvek**. Tato akce otevře **vyberte předzálohovacích skriptů a pozálohovacích skriptů** stránky.  

![Vyberte skriptů](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít v tomto příkladu, můžete použít **UpdateManagement TurnOnVms** sady runbook. Když vyberte sadu runbook **konfigurace skriptu** otevře se stránka, zadejte hodnoty pro parametry a zvolte **předzálohovací skript**. Klikněte na tlačítko **OK** až budete hotovi.

![Konfigurace skriptu](./media/pre-post-scripts/configure-script.png)

Opakujte tento postup u **UpdateManagement TurnOffVms** skriptu. Ale při volbě **typ skriptu**, zvolte **pozálohovací skript**.

**Vybraných položek** části nyní zobrazuje obě skripty vybrané a na se předzálohovací skript a druhý je pozálohovací skript.

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončení konfigurace nasazení aktualizace.

Po dokončení aktualizace nasazení můžete přejít na **nasazení aktualizací** zobrazíte výsledky. Jak vidíte, jsou k dispozici stav předsnímkových a posnímkových skriptů.

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Kliknutím na spustit nasazení aktualizací, zadáte do skriptů před a po další podrobnosti. Odkaz na zdroj skriptu v době běhu je k dispozici.

![Výsledky spustit nasazení](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Předávání parametrů

Při konfiguraci před a po skripty, můžete předat parametry stejně jako plánování runbooku. Parametry jsou definované v době vytvoření nasazení aktualizace. Skripty před a po podporuje následující typy:

* [znak]
* [bajtů]
* [int]
* [dlouhé]
* [desítkové]
* [jednotné]
* [double]
* [DateTime]
* [string]

Pokud potřebujete odlišný typ objektu, lze jej přetypovat na jiný typ pomocí vlastní logiky v sadě runbook.

Kromě standardní sada runbook parametry je k dispozici další parametr. Tento parametr je **SoftwareUpdateConfigurationRunContext**. Tento parametr je řetězec formátu JSON, a pokud definujete ve skriptu před nebo po parametru, je automaticky předána v nasazení aktualizací. Parametr obsahuje informace o nasazení aktualizace, která je podmnožinou vrácených podle informací [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) v následující tabulce se dozvíte, vlastnosti, které jsou k dispozici v proměnné:

## <a name="stopping-a-deployment"></a>Zastavuje se nasazení

Pokud chcete zastavit nasazení založené na skriptu Pre musíte [throw](automation-runbook-execution.md#throw) výjimku. Pokud není vyvolat výjimku, nasazení a Pozálohovacího skriptu bude stále spuštěn. [Ukázkového runbooku](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) ve galerii ukazuje, jak to udělat. Tady je fragment kódu z dané sady runbook.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.  
        throw $summary.Summary
    }
}
```

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext properties

|Vlastnost  |Popis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Název konfigurace aktualizace softwaru        |
|SoftwareUpdateConfigurationRunId     | Jedinečné id spuštění.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností související s konfigurací aktualizací softwaru         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Operační systémy, je určená pro nasazení aktualizace         |
|SoftwareUpdateConfigurationSettings.duration     | Maximální doba trvání nasazení aktualizace spustit jako `PT[n]H[n]M[n]S` podle ISO8601, také nazývané "okno údržby"          |
|SoftwareUpdateConfigurationSettings.Windows     | Kolekce vlastností související s počítači s Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Seznam aktualizací KB, které jsou vyloučeny z aktualizace nasazení        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Klasifikacích aktualizací vybrané pro nasazení aktualizace        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Restartování nastavení pro nasazení aktualizace        |
|azureVirtualMachines     | Seznam ID prostředků: pro virtuální počítače Azure v nasazení aktualizací        |
|nonAzureComputerNames|Seznam počítačů mimo Azure plně kvalifikovaných názvů domén v nasazení aktualizací|

V následujícím příkladu je řetězec formátu JSON, který byl poskytnut **SoftwareUpdateConfigurationRunContext** parametr:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Kompletní příklad se všechny vlastnosti najdete na následujících stránkách: [Konfigurace aktualizace softwaru - Get podle názvu](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` Objekt může obsahovat duplicitní položky pro počítače. To může způsobit skripty před a po spuštění více než jednou ve stejném počítači. Chcete-li vyřešit toto chování, použijte `Sort-Object -Unique` vybrat pouze jedinečné názvy virtuálních počítačů ve vašem skriptu.

## <a name="samples"></a>Ukázky

Ukázky pro skripty před a po najdete v [Galerie centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), nebo importovat na webu Azure portal. V části Import prostřednictvím portálu, ve vašem účtu Automation **automatizace procesů**vyberte **Galerie Runbooků**. Použití **Update Management** filtru.

![Seznam galerie](./media/pre-post-scripts/runbook-gallery.png)

Nebo můžete vyhledat je podle názvu skriptu jak je znázorněno v následujícím seznamu:

* Správa aktualizací - zapnout na virtuálních počítačích
* Správa aktualizací - vypněte virtuální počítače
* Správa aktualizací – spustit skript místně
* Update Management – šablony Předzálohovacího nebo Pozálohovacího skriptů
* Správa aktualizací - spuštění skriptu pomocí spuštění příkazu

> [!IMPORTANT]
> Jakmile dokončíte import sady runbook, je nutné **publikovat** ještě dříve, než je možné. Provedete to, který najít sady runbook ve vašem účtu Automation vyberte **upravit**a klikněte na tlačítko **publikovat**.

Ukázky jsou založeny na základní šablony, která je definována v následujícím příkladu. Tato šablona slouží k vytvoření vlastní sady runbook pomocí skriptů před a po. Nezbytnou logiku pro ověřování pomocí Azure a zpracování `SoftwareUpdateConfigurationRunContext` parametru jsou zahrnuty.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-machines"></a>Interakce s počítače

Úlohy před a po spuštění jako sady runbook ve vašem účtu Automation a ne přímo na počítačích ve vašem nasazení. Úlohy před a po také spustit v rámci Azure a nebudete mít přístup pro počítače mimo Azure. Následující části vysvětlují, jak můžete pracovat s počítače přímo, jestli se virtuální počítač Azure nebo počítač mimo Azure:

### <a name="interacting-with-azure-machines"></a>Interakce s počítači Azure

Před a po úkoly jsou spuštěny pod sady runbook a nativně nespouštějte na virtuální počítače Azure ve vašem nasazení. K interakci s virtuální počítače Azure, musíte mít následující položky:

* Účet Spustit jako
* Sady runbook, který chcete spustit

Chcete-li komunikovat s počítači Azure, měli byste použít [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) rutiny pro interakci s virtuální počítače Azure. Příklad toho, jak to provést, podívejte se na příklad runbooku [Update Management – spustit skript pomocí příkazu Spustit](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interakce s počítače mimo Azure

Úlohy před a po spuštění v rámci Azure a nebudete mít přístup pro počítače mimo Azure. K interakci s počítači mimo Azure, musíte mít následující položky:

* Účet Spustit jako
* Funkce hybrid Runbook Worker nainstalován na počítači
* Sady runbook, kterou chcete spustit místně
* Nadřízený runbook

K interakci s počítači mimo Azure, nadřazená sada runbook běží v rámci Azure. Tato sada runbook volá podřízeného runbooku se [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) rutiny. Je nutné zadat `-RunOn` parametr a zadejte název funkce Hybrid Runbook Worker pro spuštění skriptu. Příklad toho, jak to provést, podívejte se na příklad runbooku [Update Management – spustit skript místně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Přerušit opravy nasazení

Pokud váš skript pre vrátí chybu, můžete chtít přerušení vašeho nasazení. Chcete-li to provést, musíte [throw](/powershell/module/microsoft.powershell.core/about/about_throw) chybu ve skriptu pro jakékoli logiky, která bude představovat selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Známé problémy

* Pro parametry nelze předat objekty nebo pole, při použití skriptů před a po. Runbook se nezdaří.

## <a name="next-steps"></a>Další postup

Pokračujte ke kurzu se naučíte spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

