---
title: Exportování skupin prostředků Azure, které obsahují rozšíření virtuálních počítačů
description: Exportujte Správce prostředků šablony, které obsahují rozšíření virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253907"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportování skupin prostředků, které obsahují rozšíření virtuálních počítačů

Skupiny prostředků Azure je možné exportovat do nové šablony Správce prostředků, kterou je možné znovu nasadit. Proces exportu interpretuje existující prostředky a vytvoří šablonu Správce prostředků, která při nasazení výsledků do podobné skupiny prostředků. Při použití možnosti exportu skupiny prostředků u skupiny prostředků, která obsahuje rozšíření virtuálních počítačů, je potřeba zvážit několik položek, jako je například kompatibilita rozšíření a chráněné nastavení.

Tento dokument podrobně popisuje, jak proces exportu skupiny prostředků funguje v souvislosti s rozšířeními virtuálních počítačů, včetně seznamu podporovaných rozšíření, a podrobnostmi o manipulaci s zabezpečenými daty.

## <a name="supported-virtual-machine-extensions"></a>Podporovaná rozšíření virtuálních počítačů

K dispozici je řada rozšíření virtuálních počítačů. Do šablony Správce prostředků se nedají exportovat všechna rozšíření pomocí funkce skriptu Automation. Pokud rozšíření virtuálního počítače není podporováno, je nutné jej ručně umístit zpět do exportované šablony.

Následující rozšíření lze exportovat pomocí funkce skriptu automatizace.

| Linka ||||
|---|---|---|---|
| Zálohování Acronis | Služby Datadog agenta pro Windows | Opravy operačního systému pro Linux | Snímek virtuálního počítače Linux
| Acronis Backup Linux | Rozšíření Docker | Agent Puppet |
| BG – informace | Rozšíření DSC | Site 24x7 Apm Insight |
| BMC CTM agent Linux | DynaTrace Linux | Site 24x7 Linux Server |
| Okna agenta BMC CTM | DynaTrace okna | Site 24x7 Windows Server |
| Klient pro klienta | HPE Security Application Defender | Trend Micro DSA |
| Vlastní skript | IaaS antimalware | Trend Micro DSA Linux |
| Rozšíření vlastních skriptů | Diagnostika IaaS | Přístup k virtuálnímu počítači pro Linux |
| Vlastní skript pro Linux | Klient pro Linux | Přístup k virtuálnímu počítači pro Linux |
| Agent služby Datadog Linux | Diagnostika Linux | VM Snapshot |

## <a name="export-the-resource-group"></a>Export skupiny prostředků

Pokud chcete exportovat skupinu prostředků do opakovaně použitelné šablony, proveďte následující kroky:

1. Přihlášení k webu Azure Portal
2. V nabídce centra klikněte na skupiny prostředků.
3. Vyberte cílovou skupinu prostředků ze seznamu.
4. V okně Skupina prostředků klikněte na skript Automation.

![Export šablony](./media/export-templates/template-export.png)

Skript Azure Resource Manager Automation vytvoří šablonu Správce prostředků, soubor parametrů a několik ukázkových skriptů nasazení, jako je PowerShell a Azure CLI. V tuto chvíli je možné vyexportovaná šablona stáhnout pomocí tlačítka pro stažení, přidat jako novou šablonu do knihovny šablon nebo ji znovu nasadit pomocí tlačítka nasadit.

## <a name="configure-protected-settings"></a>Konfigurovat chráněná nastavení

Mnoho rozšíření virtuálních počítačů Azure zahrnuje konfiguraci chráněného nastavení, která šifruje citlivá data, jako jsou přihlašovací údaje a konfigurační řetězce. Chráněná nastavení nejsou exportována pomocí skriptu Automation. V případě potřeby musí být do exportované šablony znovu vložena chráněná nastavení.

### <a name="step-1---remove-template-parameter"></a>Krok 1 – odebrání parametru šablony

Při exportu skupiny prostředků je vytvořen jeden parametr šablony, který poskytuje hodnotu pro exportované chráněné nastavení. Tento parametr lze odebrat. Pokud chcete parametr odebrat, Prohlédněte si seznam parametrů a odstraňte parametr, který vypadá podobně jako tento příklad JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2 – získání vlastností chráněných nastavení

Vzhledem k tomu, že každé chráněné nastavení má sadu požadovaných vlastností, je třeba shromáždit seznam těchto vlastností. Každý parametr konfigurace chráněného nastavení najdete ve [schématu Azure Resource Manager na GitHubu](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Toto schéma obsahuje jenom sady parametrů pro rozšíření uvedená v tomto dokumentu v části Přehled. 

V rámci úložiště schématu vyhledejte požadovaná rozšíření pro tento příklad `IaaSDiagnostics`. Po vyhledání rozšíření `protectedSettings` objektu si poznamenejte každý parametr. V příkladu rozšíření `IaasDiagnostic` jsou parametry vyžadovat `storageAccountName`, `storageAccountKey`a `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3 – opětovné vytvoření chráněné konfigurace

V exportované šabloně vyhledejte `protectedSettings` a nahraďte exportovaný objekt Protected Setting novým, který obsahuje požadované parametry rozšíření a hodnotu pro každý z nich.

V příkladu rozšíření `IaasDiagnostic` by nová konfigurace chráněného nastavení vypadala jako v následujícím příkladu:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Konečný prostředek rozšíření vypadá podobně jako v následujícím příkladu JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Pokud používáte parametry šablony k poskytnutí hodnot vlastností, je nutné je vytvořit. Při vytváření parametrů šablony pro hodnoty chráněného nastavení nezapomeňte použít typ parametru `SecureString`, aby byly citlivé hodnoty zabezpečené. Další informace o použití parametrů najdete v tématu [vytváření Azure Resource Manager šablon](../../resource-group-authoring-templates.md).

V příkladu rozšíření `IaasDiagnostic` by se v části Parameters šablony Správce prostředků vytvořily následující parametry.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

V tuto chvíli je možné šablonu nasadit pomocí jakékoli metody nasazení šablony.
