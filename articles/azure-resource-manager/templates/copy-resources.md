---
title: Nasazení více instancí prostředků
description: K nasazení typu prostředku mnohokrát použijte operaci kopírování a pole v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 38b5bcd38e0dc8ba8c758e9aa8371857541ba55e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210824"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Iterace prostředků v šablonách Azure Resource Manager

V tomto článku se dozvíte, jak vytvořit v šabloně Azure Resource Manager více než jednu instanci prostředku. Přidáním prvku **kopírování** do části Resources (prostředky) vaší šablony můžete dynamicky nastavit počet nasazených prostředků. Nemusíte se také vyhnout opakování syntaxe šablony.

Můžete také použít kopírování s [vlastnostmi](copy-properties.md) a [proměnnými](copy-variables.md).

Pokud potřebujete určit, jestli je prostředek nasazený vůbec, viz [Podmínka elementu](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iterace prostředku

Element Copy má následující obecný formát:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Vlastnost **Name** je libovolná hodnota, která identifikuje smyčku. Vlastnost **Count** určuje počet iterací, které chcete pro daný typ prostředku.

Vlastnosti **Mode** a **BatchSize** můžete použít k určení, jestli se prostředky nasazují paralelně nebo v sekvenci. Tyto vlastnosti jsou popsány v [sériové nebo paralelní](#serial-or-parallel).

Následující příklad vytvoří počet účtů úložiště, které jsou zadány v parametru **storageCount** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Všimněte si, že název každého prostředku obsahuje funkci `copyIndex()`, která vrací aktuální iteraci ve smyčce. `copyIndex()` je založen na nule. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex())]",
```

Vytvoří tyto názvy:

* storage0
* storage1
* storage2.

Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Počet iterací je stále specifikován v elementu Copy, ale hodnota copyIndex je posunuta podle zadané hodnoty. V následujícím příkladu:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Vytvoří tyto názvy:

* storage1
* storage2
* storage3

Operace kopírování je užitečná při práci s poli, protože můžete iterovat přes každý prvek v poli. Použijte funkci `length` v poli k určení počtu iterací a `copyIndex` k načtení aktuálního indexu v poli.

Následující příklad vytvoří jeden účet úložiště pro každý název zadaný v parametru.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="serial-or-parallel"></a>Sériové nebo paralelní

Ve výchozím nastavení Správce prostředků vytvoří paralelní prostředky. Neplatí pro počet paralelně nasazených prostředků, s výjimkou celkového limitu 800 prostředků v šabloně. Pořadí, ve kterém jsou vytvořeny, není zaručeno.

Můžete ale chtít určit, že se prostředky nasazují v pořadí. Například při aktualizaci produkčního prostředí můžete aktualizace rozložit, aby se v jednom okamžiku aktualizovalo jenom určité číslo. Pro sériové nasazení více než jedné instance prostředku nastavte `mode` na **sériové** a `batchSize` na počet instancí, které se nasadí v jednom okamžiku. V případě sériového režimu Správce prostředků ve smyčce vytvoří závislost na dřívějších instancích, takže nespustí jednu dávku, dokud se předchozí dávka nedokončí.

Pokud například chcete sériové nasazení účtů úložiště vytvořit dvakrát, použijte:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Vlastnost Mode také akceptuje **paralelní**, což je výchozí hodnota.

## <a name="depend-on-resources-in-a-loop"></a>Závislá na prostředcích ve smyčce

Určíte, že prostředek bude nasazen po jiném prostředku pomocí elementu `dependsOn`. Chcete-li nasadit prostředek, který závisí na kolekci prostředků ve smyčce, zadejte název smyčky kopírování v elementu dependsOn. Následující příklad ukazuje, jak nasadit tři účty úložiště před nasazením virtuálního počítače. Nezobrazuje se úplná definice virtuálního počítače. Všimněte si, že element Copy má název nastaven na `storagecopy` a element dependsOn pro virtuální počítač je také nastaven na hodnotu `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iterace pro podřízený prostředek

U podřízeného prostředku nemůžete použít kopírovací smyčku. Chcete-li vytvořit více než jednu instanci prostředku, který obvykle definujete jako vnořený v rámci jiného prostředku, je nutné místo toho vytvořit tento prostředek jako prostředek nejvyšší úrovně. Pomocí vlastností typ a název můžete definovat relaci s nadřazeným prostředkem.

Předpokládejme například, že obvykle definujete datovou sadu jako podřízený prostředek v rámci objektu pro vytváření dat.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Pokud chcete vytvořit více než jednu datovou sadu, přesuňte ji mimo datovou továrnu. Datová sada musí být na stejné úrovni jako objekt pro vytváření dat, ale je stále podřízeným prostředkem objektu pro vytváření dat. Můžete zachovat vztah mezi datovou sadou a datovou továrnou prostřednictvím vlastností typu a názvu. Vzhledem k tomu, že typ již nelze odvodit z jeho pozice v šabloně, je nutné zadat plně kvalifikovaný typ ve formátu: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pokud chcete vytvořit relaci nadřazený-podřízený s instancí datové továrny, zadejte název datové sady, která zahrnuje název nadřazeného prostředku. Použijte formát: `{parent-resource-name}/{child-resource-name}`.

Následující příklad ukazuje implementaci:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="copy-limits"></a>Omezení kopírování

Počet nemůže být větší než 800.

Počet nemůže být záporné číslo. Pokud nasadíte šablonu s Azure PowerShell 2,6 nebo novějším, Azure CLI 2.0.74 nebo novějším nebo REST API verze **2019-05-10** nebo novější, můžete nastavit počet na nula. Starší verze prostředí PowerShell, rozhraní příkazového řádku a REST API pro počet nepodporují nulu.

Pomocí [úplného nasazení režimu](deployment-modes.md) s kopírováním buďte opatrní. Pokud znovu nasadíte v režimu úplného nasazení do skupiny prostředků, všechny prostředky, které nejsou zadané v šabloně po vyřešení smyčky kopírování, se odstraní.

## <a name="example-templates"></a>Příklad šablony

Následující příklady znázorňují běžné scénáře pro vytvoření více než jedné instance prostředku nebo vlastnosti.

|Šablony  |Popis  |
|---------|---------|
|[Kopírovat úložiště](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Nasadí více než jeden účet úložiště s číslem indexu v názvu. |
|[Úložiště sériového kopírování](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Nasadí několik účtů úložiště v jednom okamžiku. Název zahrnuje číslo indexu. |
|[Kopírování úložiště pomocí pole](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Nasadí několik účtů úložiště. Název obsahuje hodnotu z pole. |
|[Nasazení virtuálního počítače s proměnným počtem datových disků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Nasadí několik datových disků s virtuálním počítačem. |
|[Více pravidel zabezpečení](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Nasadí několik pravidel zabezpečení do skupiny zabezpečení sítě. Vytvoří pravidla zabezpečení z parametru. Pro parametr viz [více souborů parametrů NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Další kroky

* Kurz najdete v tématu [kurz: vytvoření více instancí prostředků pomocí šablon Správce prostředků](template-tutorial-create-multiple-instances.md).
* Pro jiné použití kopie elementu, viz [iterace vlastnosti v Azure Resource Manager šablony](copy-properties.md) a [iterace proměnných v šablonách Azure Resource Manager](copy-variables.md).
* Informace o použití kopírování s vnořenými šablonami naleznete v tématu [using Copy](linked-templates.md#using-copy).
* Pokud se chcete dozvědět o oddílech šablony, přečtěte si téma [vytváření Azure Resource Manager šablon](template-syntax.md).
* Informace o tom, jak šablonu nasadit, najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).

