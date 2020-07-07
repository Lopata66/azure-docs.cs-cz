---
title: Vytvoření několika instancí prostředku
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru k vytvoření více instancí prostředků Azure.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d770632958e046eb2b3494999d564bb4a6956b04
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054470"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Kurz: vytvoření více instancí prostředků pomocí šablon ARM

Přečtěte si, jak iterovat v šabloně Azure Resource Manager (ARM), abyste vytvořili víc instancí prostředku Azure. V tomto kurzu upravíte šablonu tak, aby vytvořila tři instance účtu úložiště.

![Azure Resource Manager vytvoří diagram více instancí.](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. [Vytvoření šablon ARM](use-vs-code-to-create-template.md)najdete v tématu použití Visual Studio Code.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. Z Visual Studio Code vyberte **soubor** > **otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Šablona obsahuje definici prostředku Microsoft.Storage/storageAccounts. Porovnejte tuto šablonu s [referenčními informacemi k šablonám](/azure/templates/Microsoft.Storage/storageAccounts). Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
5. Vyberte **soubor** > **Uložit jako** a uložte soubor jako **azuredeploy.js** do svého místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Stávající šablona vytvoří jeden účet úložiště. Upravíte šablonu tak, aby vytvořila tři účty úložiště.

Z Visual Studio Code proveďte následující čtyři změny:

![Azure Resource Manager vytvoří více instancí](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Přidejte prvek `copy` k definování prostředků účtu úložiště. V elementu copy určíte počet iterací a proměnnou pro tuto smyčku. Početní hodnota musí být kladný integer a nesmí překročit 800.
2. Funkce `copyIndex()` vrátí aktuální iteraci ve smyčce. Tento index použijete jako předponu názvu. `copyIndex()` je založen na nule. Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Například *copyIndex(1)*.
3. Odstraňte prvek **proměnné**, protože se už nepoužívá.
4. Odstraňte prvek **výstupu**. Už není zapotřebí.

Hotová šablona vypadá takto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Další informace o vytváření více instancí najdete v tématu [nasazení více instancí prostředku nebo vlastnosti v šablonách ARM](./copy-resources.md) .

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí.  Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky. Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí příkazu **ls** a příkazu **Cat** ověřit, jestli se soubor úspěšně nahrál.

1. Z Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Chcete-li vypsat všechny tři účty úložiště, vynechejte --název parametru:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

Porovnejte názvy účtů úložiště s názvem definice v šabloně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  V rámci skupiny prostředků se zobrazí celkem tři prostředky.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit více instancí účtu úložiště.  V dalším kurzu se zaměříte na vývoj šablony s více prostředky a více typy prostředků. Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvořit závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md)
