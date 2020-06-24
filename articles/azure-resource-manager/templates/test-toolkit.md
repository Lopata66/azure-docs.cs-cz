---
title: Sada nástrojů pro test šablon ARM
description: Popisuje, jak spustit sadu nástrojů pro test šablon ARM ve vaší šabloně. Sada nástrojů vám umožní zjistit, jestli jste implementovali Doporučené postupy.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255916"
---
# <a name="use-arm-template-test-toolkit"></a>Použít sadu nástrojů pro test šablon ARM

[Sada testů šablon ARM](https://aka.ms/arm-ttk) kontroluje, jestli vaše šablona používá Doporučené postupy. Pokud vaše šablona není kompatibilní s doporučenými postupy, vrátí seznam upozornění s navrhovanými změnami. Pomocí sady nástrojů test Toolkit se můžete dozvědět, jak se vyhnout běžným problémům při vývoji šablon.

Sada testů poskytuje [sadu výchozích testů](test-cases.md). Tyto testy jsou doporučení, ale ne požadavky. Můžete určit, které testy jsou relevantní pro vaše cíle, a přizpůsobit, které testy se mají spustit.

Tento článek popisuje, jak spustit sadu testů a jak přidat nebo odebrat testy. Popisy výchozích testů naleznete v tématu sady [nástrojů testové případy](test-cases.md).

Sada nástrojů je sada PowerShellových skriptů, které se dají spouštět z příkazu v PowerShellu nebo rozhraní příkazového řádku.

## <a name="download-test-toolkit"></a>Stáhnout sadu testů

Chcete-li použít sadu nástrojů test Toolkit, můžete buď rozvětvit a klonovat [úložiště](https://aka.ms/arm-ttk) obsahující skripty, nebo [Stáhnout nejnovější soubor. zip](https://aka.ms/arm-ttk-latest).

V závislosti na zásadách spouštění počítače, na kterém spouštíte skript, se může zobrazit chyba týkající se spouštění skriptů z Internetu. Musíte buď změnit [zásady spouštění](/powershell/module/microsoft.powershell.core/about/about_execution_policies) , nebo [odblokovat soubory skriptu](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Spuštění v PowerShellu

Před spuštěním testů Importujte modul.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Chcete-li spustit testy v **prostředí PowerShell**, použijte následující příkaz:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Spustit na Linux

Před spuštěním testů nainstalujte [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux).

Chcete-li spustit testy na **platformě Linux** v bash, použijte následující příkaz:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

Můžete také spustit test na pwsh.exe.

## <a name="run-on-macos"></a>Spustit na macOS

Před spuštěním testů nainstalujte [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos). 

Nainstalujte `coreutils`:

```bash
brew install coreutils
```

Chcete-li spustit testy na **MacOS**, použijte následující příkaz:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Výsledný formát

Testy, které jsou předávány, jsou zobrazeny **zeleně** a uvozeny **[+]**.

Testy, které selžou, se zobrazují **červeně** a jsou uvozeny **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Zobrazit výsledky testu":::

Výsledky textu jsou:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Parametry testu

Když zadáte parametr **-TemplatePath** , sada Toolkit v této složce vyhledá šablonu s názvem azuredeploy.jszapnutou nebo maintemplate.js. Nejprve testuje tuto šablonu a pak testuje všechny ostatní šablony ve složce a jejích podsložkách. Ostatní šablony jsou testovány jako propojené šablony. Pokud vaše cesta obsahuje soubor s názvem [CreateUiDefinition.jsv](../managed-applications/create-uidefinition-overview.md), spustí testy, které jsou relevantní pro definici uživatelského rozhraní.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Chcete-li otestovat jeden soubor v této složce, přidejte parametr **-File** . Složka však musí mít stále hlavní šablonu s názvem azuredeploy.jsnebo maintemplate.jsna.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Ve výchozím nastavení se spustí všechny testy. Chcete-li určit jednotlivé testy, které mají být spuštěny, použijte parametr **-test** . Zadejte název testu. Názvy naleznete v tématu [testové případy pro sadu nástrojů](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Přizpůsobení testů

V případě šablon ARM spustí sada nástrojů všechny testy ve složce **\arm-ttk\testcases\deploymentTemplate**. Pokud chcete trvale odebrat test, odstraňte ho ze složky.

U souborů [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) spustí všechny testy ve složce **\arm-ttk\testcases\CreateUiDefinition**.

Chcete-li přidat vlastní test, vytvořte soubor s konvencí pojmenování: **Your-Custom-Test-Name.test.ps1**.

Test může získat šablonu jako parametr objektu nebo řetězcový parametr. Obvykle použijete jeden nebo druhý, ale můžete použít obojí.

Parametr Object použijte v případě, že potřebujete získat část šablony a iterovat prostřednictvím jejích vlastností. Test, který používá parametr objektu, má následující formát:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Objekt šablony má následující vlastnosti:

* $schema
* Contentversion –
* parameters
* proměnné
* resources
* činnosti

Můžete například získat kolekci parametrů s `$TemplateObject.parameters` .

Použijte parametr String, pokud potřebujete provést operaci s řetězcem pro celou šablonu. Test, který používá parametr String, má následující formát:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Například můžete spustit regulární výraz řetězcového parametru a zjistit, zda je použita konkrétní syntaxe.

Další informace o implementaci testu najdete v dalších testech v této složce.

## <a name="integrate-with-azure-pipelines"></a>Integrace s Azure Pipelines

Můžete přidat sadu testů do vašeho kanálu Azure. Pomocí kanálu můžete spustit test pokaždé, když je šablona aktualizována, nebo ji spustit jako součást procesu nasazení.

Nejjednodušší způsob, jak přidat sadu testů do vašeho kanálu, je rozšíření jiných výrobců. K dispozici jsou následující dvě rozšíření:

* [Spuštění testů TTK ARM](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Tester šablon ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Nebo můžete implementovat vlastní úkoly. Následující příklad ukazuje, jak stáhnout sadu testů.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

Další příklad ukazuje, jak spustit testy.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Další kroky

Další informace o výchozích testech naleznete v tématu [testové případy pro sadu nástrojů](test-cases.md).
