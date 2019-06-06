---
title: Funkce šablon Azure Resource Manageru – řetězec | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru pro práci s řetězci.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: 82b9403a3d5a5b6938f5b95bbfce888d1e70e451
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431215"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Řetězec funkce pro šablony Azure Resource Manageru

Resource Manager poskytuje následující funkce pro práci s řetězci:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [Obsahuje](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [prázdný](#empty)
* [endsWith](#endswith)
* [první](#first)
* [Formát](#format)
* [identifikátor GUID](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Délka](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [nahradit](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [řetězec](#string)
* [dílčí řetězec](#substring)
* [Take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Vrátí reprezentaci ve formátu base64 vstupního řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |string |Hodnota vrátit jako reprezentace base64. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující reprezentaci ve formátu base64.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) ukazuje, jak použít funkci ve formátu base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Jedna dva tři |
| toJsonOutput | Object | {"jedna": "a", "2": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Převede reprezentace base64 do objektu JSON.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Value |Ano |string |Reprezentace base64 pro převod na objekt JSON. |

### <a name="return-value"></a>Návratová hodnota

Objekt JSON.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToJson převést hodnotu ve formátu base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Jedna dva tři |
| toJsonOutput | Object | {"jedna": "a", "2": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Převede na řetězec formátu base64.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base64Value |Ano |string |Reprezentace base64 převést na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Řetězec hodnoty převedeného ve formátu base64.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) používá funkci base64ToString převést hodnotu ve formátu base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Jedna dva tři |
| toJsonOutput | Object | {"jedna": "a", "2": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombinuje více řetězcových hodnot a vrací spojený řetězec nebo kombinuje několik polí a vrátí pole zřetězených.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |řetězec nebo pole |První hodnota pro zřetězení. |
| Další argumenty |Ne |string |Další hodnoty v postupném pořadí pro zřetězení. |

### <a name="return-value"></a>Návratová hodnota
Řetězec nebo pole zřetězených hodnot.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ukazuje, jak kombinovat dva řetězcové hodnoty a vrátí zřetězených řetězců.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| concatOutput | String | Předpona 5yj4yjf5mbg72 |

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ukazuje, jak kombinace dvou polí.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| Vrátí | Pole | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>Obsahuje

`contains (container, itemToFind)`

Kontroluje, zda pole obsahuje hodnotu, objekt obsahuje klíč nebo řetězec obsahuje daný podřetězec. Porovnání řetězců rozlišuje velká a malá písmena. Při testování, pokud objekt obsahuje klíč, porovnání ale velká a malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| container |Ano |pole, objekt nebo řetězec |Hodnota, která obsahuje hodnotu k vyhledání. |
| itemToFind |Ano |řetězec nebo int |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

**Hodnota TRUE** Pokud byla položka nalezena; v opačném případě **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ukazuje, jak používat obsahuje s různými typy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Převede hodnotu na identifikátor URI dat.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ano |string |Hodnota převedená na identifikátor URI dat. |

### <a name="return-value"></a>Návratová hodnota

Řetězec formátovaný jako data identifikátoru URI.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat. a převede na řetězec URI dat:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Ahoj světe! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Převede data identifikátor URI ve formátu hodnotu na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ano |string |Data, která hodnotu identifikátoru URI k převedení. |

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující převedenou hodnotu.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) převede hodnotu na identifikátor URI dat. a převede na řetězec URI dat:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Ahoj světe! |

## <a name="empty"></a>Prázdná

`empty(itemToTest)`

Určuje, zda je pole, objekt nebo řetězec prázdný.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| itemToTest |Ano |pole, objekt nebo řetězec |Hodnota ke kontrole, jestli je prázdný. |

### <a name="return-value"></a>Návratová hodnota

Vrátí **True** Pokud prázdný; v opačném případě je hodnota **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ověří, zda polí, objektu a řetězec jsou prázdné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Určuje, jestli řetězec končí určitou hodnotou. Porovnání nerozlišuje velká a malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položky, která má najít. |
| stringToFind |Ano |string |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

**Hodnota TRUE** Pokud poslední znak nebo znaky řetězce odpovídají hodnotě; v opačném případě **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje způsob použití funkce startsWith a endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>první

`first(arg1)`

Vrátí první znak řetězce, nebo první prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |Hodnota načíst první prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Řetězce prvního znaku, nebo první prvek v poli Typ (řetězec, int, pole nebo objekt).

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ukazuje, jak použít první funkce pomocí řetězec a pole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | String | jeden |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Umožňuje vytvořit formátovaný řetězec ze vstupních hodnot.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| formatString | Ano | string | Složený formátovací řetězec. |
| arg1 | Ano | řetězec, celé číslo nebo logickou hodnotu | Hodnota pro zahrnutí do formátovaného řetězce. |
| Další argumenty | Ne | řetězec, celé číslo nebo logickou hodnotu | Další hodnoty pro zahrnutí do formátovaného řetězce. |

### <a name="remarks"></a>Poznámky

Tato funkce slouží k formátování řetězce ve vaší šabloně. Použije formátování stejné možnosti jako [System.String.Format](/dotnet/api/system.string.format) metoda v rozhraní .NET.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje způsob použití funkce formátu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| formatTest | String | Dobrý den, uživateli. Naformátovaného čísla: 8,175,133 |

## <a name="guid"></a>identifikátor GUID

`guid(baseString, ...)`

Vytvoří hodnotu ve formátu globálně jedinečný identifikátor na základě hodnot, které jsou poskytovány jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |string |Hodnota určená ve funkci hash vytvořit identifikátor GUID. |
| Další parametry podle potřeby |Ne |string |Můžete přidat libovolný počet řetězců podle potřeby vytvořit hodnotu, která určuje úroveň jedinečnost. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečné, když budete chtít vytvořit hodnotu ve formátu globálně jedinečný identifikátor. Můžete zadat hodnoty parametrů, které omezit její obor jedinečnosti pro výsledek. Můžete určit, zda je název jedinečný na předplatné, skupinu prostředků nebo nasazení.

Vrácená hodnota není náhodný řetězec, ale spíše výsledek funkce hash na parametry. Vrácená hodnota je 36 znaků. Není globálně jedinečný. Chcete-li vytvořit nový identifikátor GUID, který není založen na touto hodnotou hash parametrů, použijte [newGuid](#newguid) funkce.

Následující příklady ukazují, jak vytvořit jedinečnou hodnotu pro běžně používané úrovně pomocí identifikátoru guid.

Jedinečný obor na předplatné

```json
"[guid(subscription().subscriptionId)]"
```

Jedinečný obor do skupiny prostředků

```json
"[guid(resourceGroup().id)]"
```

Jedinečný rozsah nasazení pro skupinu prostředků

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečný identifikátor.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) vrátí výsledky z identifikátoru guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Vrátí první pozici hodnoty v řetězci. Porovnání nerozlišuje velká a malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položky, která má najít. |
| stringToFind |Ano |string |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo představující pozici položky k vyhledání. Hodnota je založený na nule. Pokud položka není nalezena, vrátí se -1.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje způsob použití funkce indexOf a lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="last"></a>poslední

`last (arg1)`

Vrátí poslední znak řetězce nebo poslední prvek pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |Hodnota určená k načtení poslední prvek nebo znak. |

### <a name="return-value"></a>Návratová hodnota

Řetězce posledního znaku nebo typ (řetězec, int, pole nebo objekt) po posledním prvku v poli.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ukazuje, jak použít funkci poslední pole a řetězce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | String | tři |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Vrátí poslední pozice hodnoty v řetězci. Porovnání nerozlišuje velká a malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položky, která má najít. |
| stringToFind |Ano |string |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo, které představuje poslední pozici položky k vyhledání. Hodnota je založený na nule. Pokud položka není nalezena, vrátí se -1.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ukazuje způsob použití funkce indexOf a lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="length"></a>Délka

`length(string)`

Vrátí počet znaků v řetězci nebo prvky v poli.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| arg1 |Ano |poli nebo řetězci. |Pole, které chcete použít pro získání počet prvků, nebo řetězec určený pro maximální počet znaků. |

### <a name="return-value"></a>Návratová hodnota

Celé číslo 

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ukazuje způsob použití délku pole a řetězec:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

## <a name="newguid"></a>newGuid

`newGuid()`

Vrátí hodnotu ve formátu globálně jedinečný identifikátor. **Tuto funkci jde použít jenom ve výchozí hodnota pro parametr.**

### <a name="remarks"></a>Poznámky

Tato funkce ve výrazu můžete použít pouze pro výchozí hodnotu parametru. Pomocí této funkce, která je někde jinde v šabloně vrátí chybu. Funkce není povolena v další části šablony, protože pokaždé, když je volána vrací jinou hodnotu. Nasazování stejné šablony se stejnými parametry by vytvářejí spolehlivě stejné výsledky.

NewGuid funkce se liší od [guid](#guid) fungovat, protože nepřijímá žádné parametry. Při volání guid s stejný parametr vrací stejný identifikátor pokaždé, když. Když budete chtít spolehlivě generovat stejný identifikátor GUID pro konkrétní prostředí, použijte identifikátor guid. NewGuid použijte, když budete potřebovat jiný identifikátor pokaždé, když, jako je například nasazení prostředků do testovacího prostředí.

Pokud používáte [možnost znovu nasadit dříve úspěšné nasazení](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)a starší nasazení obsahuje parametr, který používá newGuid, již není parametr znovu. Namísto toho hodnoty parametru z předchozích nasazení je automaticky znovu použít v nasazení vrácení zpět.

V testovacím prostředí budete muset opakovaně nasazovat prostředky, které pouze za krátkou dobu. Namísto vytváření jedinečné názvy, můžete použít newGuid s [uniqueString](#uniquestring) vytvořit jedinečné názvy.

Buďte opatrní, znovu nasadit šablonu, která se spoléhá na funkce newGuid pro výchozí hodnotu. Když znovu nasadit a neposkytují hodnotu pro parametr funkce se již znovu. Pokud chcete aktualizovat existující prostředek, ne vytvořit nové, předejte hodnotu parametru z předchozích nasazení.

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 36 znaků ve formátu globálně jedinečný identifikátor.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje parametr s novým identifikátorem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší u každého nasazení ale bude vypadat podobně jako:

| Name | Typ | Hodnota |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Následující příklad používá funkci newGuid vytvořit jedinečný název pro účet úložiště. Tato šablona může fungovat pro testovací prostředí, kde účet úložiště existuje po krátkou dobu a není znovu nasadil.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší u každého nasazení ale bude vypadat podobně jako:

| Name | Typ | Hodnota |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Vrátí řetězec zarovnaný doprava přidáním znaků na levé straně až do dosažení celkové určené délky.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToPad |Ano |řetězec nebo int |Hodnota zarovnání vpravo. |
| totalLength |Ano |int |Celkový počet znaků ve vráceném řetězci. |
| paddingCharacter |Ne |jeden znak |Znak, který se má použít pro odsazení vlevo až do dosažení celkové délky. Výchozí hodnota je mezera. |

Pokud původní řetězec je delší než počet znaků pro vyplnění, přidají se žádné znaky.

### <a name="return-value"></a>Návratová hodnota

Řetězec s minimálně počet určených znaků.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) ukazuje, jak pro vyplnění hodnoty uživatelem zadaného parametru tak, že přidáte nulu, dokud nedosáhne celkový počet znaků. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>nahradit

`replace(originalString, oldString, newString)`

Vrátí nový řetězec se všemi instancemi jeden řetězec nahrazen jiným řetězcem.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| originalString |Ano |string |Hodnota, která obsahuje všechny instance jeden řetězec nahrazen jiným řetězcem. |
| Starý řetězec |Ano |string |Řetězec, který má být odebrána z původního řetězce. |
| nový řetězec |Ano |string |Řetězec, který přidáte místo odebrané řetězec. |

### <a name="return-value"></a>Návratová hodnota

Řetězec se nahradil znaky.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) ukážeme, jak odebrat všechny pomlčky z uživatelem zadaného řetězce a nahradí část řetězce jiným řetězcem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>Přeskočit

`skip(originalValue, numberToSkip)`

Vrátí řetězec, všechny znaky po zadaný počet znaků, nebo pole obsahující všechny prvky po zadaný počet prvků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |poli nebo řetězci. |Pole nebo řetězec použitý pro přeskočení. |
| numberToSkip |Ano |int |Počet elementů nebo znaků, které mají přeskočit. Pokud tato hodnota je 0 nebo méně, budou vráceny všechny elementy nebo znaků v hodnotě. Pokud je větší než délka pole nebo řetězec, vrátí se prázdná pole nebo řetězec. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) vynechá zadaný počet prvků v poli a zadaný počet znaků v řetězci.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["tři"] |
| stringOutput | String | dva tři |

## <a name="split"></a>split

`split(inputString, delimiter)`

Vrátí pole řetězců obsahující podřetězce vstupního řetězce, které jsou odděleny zadaného oddělovače.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| inputString |Ano |string |Řetězec, který se má rozdělit. |
| Oddělovač |Ano |řetězec nebo pole řetězců |Oddělovač, který má použít pro rozdělení řetězce. |

### <a name="return-value"></a>Návratová hodnota

Pole řetězců.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) rozdělí vstupní řetězec s čárkou a s čárkou nebo středníkem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| firstOutput | Pole | ["jedna", "dvě", "tři"] |
| secondOutput | Pole | ["jedna", "dvě", "tři"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Určuje, jestli řetězec začíná určitou hodnotou. Porovnání nerozlišuje velká a malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ano |string |Hodnota, která obsahuje položky, která má najít. |
| stringToFind |Ano |string |Hodnota k vyhledání. |

### <a name="return-value"></a>Návratová hodnota

**Hodnota TRUE** Pokud první znak nebo znaky řetězce odpovídají hodnotě; v opačném případě **False**.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ukazuje způsob použití funkce startsWith a endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Převede zadanou hodnotu na řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ano | Jakýkoli |Hodnota převedená na řetězec. Libovolný typ hodnoty lze převést, včetně objekty a pole. |

### <a name="return-value"></a>Návratová hodnota

Řetězec převedená hodnota.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) ukazuje, jak převést různé typy hodnot na řetězce:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| objectOutput | String | {"Hodnotaa": 10, "Hodnotab": "Ukázkovému textu"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>dílčí řetězec

`substring(stringToParse, startIndex, length)`

Vrátí dílčí řetězec, který začíná na pozici zadaného znaku a obsahuje zadaný počet znaků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToParse |Ano |string |Původní řetězec, ze které je dílčí řetězec extrahován. |
| Počáteční index |Ne |int |Počáteční znak pozice s nulovým základem pro dílčí řetězec. |
| Délka |Ne |int |Počet znaků pro dílčí řetězec. Musí odkazovat na umístění v rámci řetězce. Musí být nulová nebo větší. |

### <a name="return-value"></a>Návratová hodnota

Dílčí řetězec. Nebo prázdný řetězec, pokud délka je 0.

### <a name="remarks"></a>Poznámky

Funkce selže, když podřetězec přesahuje konec řetězce, nebo když délka je menší než nula. V následujícím příkladu se nezdaří s chybou "parametry index a délka musí odkazovat na umístění v rámci řetězce. Index parametru: "0", parametr délky: "11", délky řetězce: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extrahuje podřetězec z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| substringOutput | String | dva |

## <a name="take"></a>Take

`take(originalValue, numberToTake)`

Vrátí řetězec s zadaný počet znaků od začátku řetězce nebo pole obsahující zadaný počet prvků od začátku pole.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| původní hodnota |Ano |poli nebo řetězci. |Pole nebo řetězec, se prvky ze. |
| numberToTake |Ano |int |Počet elementů nebo znaků, které mají provést. Pokud tato hodnota je 0 nebo méně, je vrácena prázdná pole nebo řetězec. Pokud je větší než délka daného pole nebo řetězec, budou vráceny všechny prvky v poli nebo řetězci. |

### <a name="return-value"></a>Návratová hodnota

Pole nebo řetězec.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) má zadaný počet prvků v poli a znaků z řetězce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| arrayOutput | Pole | ["1", "dvě"] |
| stringOutput | String | zapnuté |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Převede zadaný řetězec na malá písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToChange |Ano |string |Hodnota k převedení na malá písmena. |

### <a name="return-value"></a>Návratová hodnota

Daný řetězec převést na malá písmena.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a velká písmena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| toLowerOutput | String | Jedna dva tři |
| toUpperOutput | String | JEDNA DVA TŘI |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Převede zadaný řetězec na velká písmena.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToChange |Ano |string |Hodnota převedená na velká písmena. |

### <a name="return-value"></a>Návratová hodnota

Daný řetězec převést na velká písmena.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) převede hodnotu parametru na malá písmena a velká písmena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| toLowerOutput | String | Jedna dva tři |
| toUpperOutput | String | JEDNA DVA TŘI |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Odebere všechny úvodní a koncové prázdné znaky ze zadaného řetězce.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ano |string |Hodnota, která mají být odebrány. |

### <a name="return-value"></a>Návratová hodnota

Řetězec bez úvodní a koncové prázdné znaky.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) ořízne prázdné znaky z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| Vrátí | String | Jedna dva tři |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Vytvoří deterministické hash řetězce na základě hodnot, které jsou poskytovány jako parametry. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseString |Ano |string |Hodnota ve funkci hash používá k vytvoření jedinečného řetězce. |
| Další parametry podle potřeby |Ne |string |Můžete přidat libovolný počet řetězců podle potřeby vytvořit hodnotu, která určuje úroveň jedinečnost. |

### <a name="remarks"></a>Poznámky

Tato funkce je užitečná při je potřeba vytvořit jedinečný název pro prostředek. Můžete zadat hodnoty parametrů, které omezit její obor jedinečnosti pro výsledek. Můžete určit, zda je název jedinečný na předplatné, skupinu prostředků nebo nasazení. 

Vrácená hodnota není náhodný řetězec, ale spíše výsledek funkce hash. Vrácená hodnota je 13 znaků. Není globálně jedinečný. Můžete zkombinovat hodnotu s předponou ze svých zásad vytváření názvů k vytvoření smysluplný název. Následující příklad ukazuje formátu vrácené hodnoty. Skutečné hodnoty se liší podle zadaných parametrů.

    tcvhiyu5h2o5o

Následující příklady ukazují, jak pomocí uniqueString můžete vytvořit jedinečnou hodnotu pro běžně používané úrovně.

Jedinečný obor na předplatné

```json
"[uniqueString(subscription().subscriptionId)]"
```

Jedinečný obor do skupiny prostředků

```json
"[uniqueString(resourceGroup().id)]"
```

Jedinečný rozsah nasazení pro skupinu prostředků

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Následující příklad ukazuje, jak vytvořit jedinečný název pro účet úložiště podle vaší skupiny prostředků. Do skupiny prostředků název není jedinečný, pokud vytvořen stejným způsobem.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Pokud je potřeba vytvořit nový jedinečný název pokaždé, když šablonu nasadit a nechcete aktualizovat prostředek, můžete použít [utcNow](#utcnow) funkci s uniqueString. Tento přístup můžete použít v testovacím prostředí. Příklad najdete v tématu [utcNow](#utcnow).

### <a name="return-value"></a>Návratová hodnota

Řetězec obsahující 13 znaků.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) vrátí výsledky z uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Vytvoří absolutní identifikátor URI kombinací baseUri a relativeUri řetězec.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| baseUri |Ano |string |Řetězec, který základní identifikátor uri. |
| relativeUri |Ano |string |Řetězec relativní identifikátor uri pro přidání do řetězce základní identifikátor uri. |

Hodnota **baseUri** parametr může obsahovat konkrétní soubor, ale používá se pouze základní cestu, při vytváření identifikátor URI. Například předávání `http://contoso.com/resources/azuredeploy.json` jako parametr výsledky baseUri v základní identifikátor URI z `http://contoso.com/resources/`.

### <a name="return-value"></a>Návratová hodnota

Řetězec představující absolutní identifikátor URI pro základní a relativní hodnoty.

### <a name="examples"></a>Příklady

Následující příklad ukazuje, jak vytvořit odkaz na vnořené šablony založené na hodnotě nadřazené šablony.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátor uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Zakóduje identifikátor URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ano |string |Hodnota ke kódování. |

### <a name="return-value"></a>Návratová hodnota

Kódovaný řetězec identifikátoru URI hodnotu.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátor uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Vrátí že hodnotu zakódovaného řetězce identifikátoru URI.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ano |string |Kódovaný identifikátor URI hodnota převedená na řetězec. |

### <a name="return-value"></a>Návratová hodnota

Dekódovaný řetězec identifikátoru URI kódovaný hodnotu.

### <a name="examples"></a>Příklady

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ukazuje, jak použít identifikátor uri, uriComponent a uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Vrátí aktuální hodnotu data a času (UTC) v zadaném formátu. Pokud je k dispozici žádný format, použije se formát ISO 8601 (RRRRMMDDThhmmssZ). **Tuto funkci jde použít jenom ve výchozí hodnota pro parametr.**

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| format |Ne |string |Kódovaný identifikátor URI hodnota převedená na řetězec. Použijte buď [standardní formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Poznámky

Tato funkce ve výrazu můžete použít pouze pro výchozí hodnotu parametru. Pomocí této funkce, která je někde jinde v šabloně vrátí chybu. Funkce není povolena v další části šablony, protože pokaždé, když je volána vrací jinou hodnotu. Nasazování stejné šablony se stejnými parametry by vytvářejí spolehlivě stejné výsledky.

Pokud používáte [možnost znovu nasadit dříve úspěšné nasazení](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)a starší nasazení obsahuje parametr, který používá utcNow, již není parametr znovu. Namísto toho hodnoty parametru z předchozích nasazení je automaticky znovu použít v nasazení vrácení zpět.

Buďte opatrní, znovu nasadit šablonu, která se spoléhá na funkce utcNow pro výchozí hodnotu. Když znovu nasadit a neposkytují hodnotu pro parametr funkce se již znovu. Pokud chcete aktualizovat existující prostředek, ne vytvořit nové, předejte hodnotu parametru z předchozích nasazení.

### <a name="return-value"></a>Návratová hodnota

Aktuální hodnota data a času UTC.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje různé formáty pro hodnoty data a času.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší u každého nasazení ale bude vypadat podobně jako:

| Name | Typ | Hodnota |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

Následující příklad ukazuje, jak použít hodnotu z funkce při nastavování určitou hodnotou značky.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

