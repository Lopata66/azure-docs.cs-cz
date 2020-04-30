---
title: Syntaxe a výrazy šablon
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: baddedae1b918502e579d2ed230e0779960f45e7
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203824"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe a výrazy v šablonách Azure Resource Manager

Základní syntaxí šablony je JSON. Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí závorkami `[` a `]` v uvedeném pořadí. Hodnota výrazu se vyhodnocuje po nasazení šablony. Výraz může vrátit řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

Výraz šablony nemůže být delší než 24 576 znaků.

## <a name="use-functions"></a>Použití funkcí

Azure Resource Manager poskytuje [funkce](template-functions.md) , které můžete použít v šabloně. Následující příklad ukazuje výraz, který používá funkci ve výchozí hodnotě parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Syntaxe `resourceGroup()` v rámci výrazu volá jednu z funkcí, které správce prostředků poskytují pro použití v rámci šablony. V tomto případě je to funkce [Resource](template-functions-resource.md#resourcegroup) . Stejně jako v jazyce JavaScript jsou volání funkcí formátována `functionName(arg1,arg2,arg3)`jako. Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků vyřeší **proměnné (' var1 ')** a **proměnné (' var1 ')** jako stejné. Je-li tato funkce vyhodnocena, pokud funkce Express nemění velká a malá písmena (například toUpper nebo toLower), funkce zachovává případ. Některé typy prostředků můžou mít požadavky na případy, které jsou oddělené od způsobu, jakým se funkce vyhodnocují.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Většina funkcí funguje stejně, ať už je nasazená do skupiny prostředků, předplatného, skupiny pro správu nebo tenanta. Následující funkce mají omezení na základě rozsahu:

* [resourceGroup](template-functions-resource.md#resourcegroup) skupina prostředků se dá použít jenom v nasazeních do skupiny prostředků.
* [ResourceID](template-functions-resource.md#resourceid) -lze použít v jakémkoli oboru, ale platné parametry se mění v závislosti na rozsahu.
* [předplatné](template-functions-resource.md#subscription) – dá se použít jenom v nasazeních do skupiny prostředků nebo předplatného.

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li, aby byl řetězcový literál začínat levou hranatou `[` závorkou `]`a končit pravou závorkou, ale nebyl interpretován jako výraz, přidejte další hranatou závorku, `[[`která zahájí řetězec s. Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží `[test value]`na.

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží `[test] value`na.

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Při předávání hodnot parametrů závisí použití řídicích znaků na místě, kde je zadána hodnota parametru. Pokud nastavíte výchozí hodnotu v šabloně, budete potřebovat další levou hranatou závorku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Použijete-li výchozí hodnotu, šablona se vrátí `[test value]`.

Nicméně pokud předáte hodnotu parametru prostřednictvím příkazového řádku, znaky jsou interpretovány doslova. Nasazování předchozí šablony pomocí:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Vrací objekt `[[test value]`. Místo toho použijte:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Stejné formátování platí při předávání hodnot ze souboru parametrů. Znaky jsou interpretovány doslova. Při použití s předchozí šablonou se vrátí `[test value]`následující soubor parametrů:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Hodnoty Null

Chcete-li nastavit vlastnost na hodnotu null, můžete použít **hodnotu null** nebo **[JSON (' null ')]**. [Funkce JSON](template-functions-object.md#json) vrátí prázdný objekt, když zadáte `null` jako parametr. V obou případech šablony Správce prostředků považují za, jako by vlastnost nebyla přítomna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
