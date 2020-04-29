---
title: Kurz – použití souboru parametrů k nasazení šablony
description: Použijte soubory parametrů, které obsahují hodnoty, které se mají použít k nasazení šablony Azure Resource Manager.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b91041b96a3819dbace3898d92226f0351f0f973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411508"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Kurz: použití souborů parametrů k nasazení šablony ARM

V tomto kurzu se naučíte používat [soubory parametrů](parameter-files.md) k uložení hodnot, které předáte během nasazování. V předchozích kurzech jste v příkazu nasazení použili vložené parametry. Tento přístup pracoval při testování šablony Azure Resource Manager (ARM), ale při automatizaci nasazení může být snazší předat sadu hodnot pro vaše prostředí. Soubory parametrů usnadňují zabalení hodnot parametrů pro konkrétní prostředí. V tomto kurzu vytvoříte soubory parametrů pro vývojová a produkční prostředí. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se značek](template-tutorial-add-tags.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Vaše šablona má mnoho parametrů, které můžete během nasazení zadat. Na konci předchozího kurzu vaše šablona vypadala takto:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Tato šablona funguje dobře, ale nyní chcete snadno spravovat parametry, které předáváte pro šablonu.

## <a name="add-parameter-files"></a>Přidat soubory parametrů

Soubory parametrů jsou soubory JSON se strukturou, která je podobná vaší šabloně. V souboru zadejte hodnoty parametrů, které chcete předat během nasazování.

V VS Code vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy. Parameters. dev. JSON**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Tento soubor je vaším souborem parametrů pro vývojové prostředí. Všimněte si, že pro účet úložiště používá Standard_LRS, pojmenuje prostředky s předponou pro **vývoj** a nastaví značku **prostředí** na **dev**.

Znovu vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy. Parameters. prod. JSON**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Tento soubor je vaším souborem parametrů pro produkční prostředí. Všimněte si, že používá Standard_GRS pro účet úložiště, názvy prostředků s předponou **Contoso** a nastaví značku **prostředí** na **produkční**. V reálném produkčním prostředí byste také chtěli použít službu App Service s jinou skladovou jednotkou než bezplatnou, ale pro tento kurz bude tato SKU nadále používat.

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Jako konečný test šablony vytvoříme dvě nové skupiny prostředků. Jednu pro vývojové prostředí a jednu pro produkční prostředí.

Nejdřív nasadíme do vývojového prostředí.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) rozhraní příkazového řádku Azure CLI.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Nyní nasadíme do produkčního prostředí.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, použijte k zobrazení protokolů ladění přepínač **ladění** s příkazem nasazení.  Můžete také použít **podrobný** přepínač k zobrazení úplných protokolů ladění.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupin prostředků z Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. V tomto kurzu se zobrazí dvě nové skupiny prostředků, které jste nasadili.
1. Vyberte buď skupinu prostředků, a zobrazte nasazené prostředky. Všimněte si, že odpovídají hodnotám, které jste zadali v souboru parametrů pro toto prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků. Pokud jste tuto řadu dokončili, máte tři skupiny prostředků pro odstranění – myResourceGroup, myResourceGroupDev a myResourceGroupProd.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Gratulujeme, dokončili jste tento Úvod k nasazování šablon do Azure. Dejte nám vědět, pokud máte komentáře a návrhy v části věnované zpětné vazbě. Děkujeme!

Další série kurzů se podrobněji seznámí s nasazením šablon.

> [!div class="nextstepaction"]
> [Nasazení místní šablony](./deployment-tutorial-local-template.md)
