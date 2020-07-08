---
title: Kurz – vytvoření šablony & nasazení
description: Vytvořte svou první šablonu Azure Resource Manager. V tomto kurzu se dozvíte o syntaxi souboru šablony a o tom, jak nasadit účet úložiště.
author: mumian
ms.date: 06/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1e286a3b59279ed9658a373210f1425ece05eff4
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102095"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Kurz: vytvoření a nasazení první šablony ARM

V tomto kurzu se seznámíte s Azure Resource Manager (ARM) šablonou. Ukazuje, jak vytvořit úvodní šablonu a jak ji nasadit do Azure. Seznámíte se se strukturou šablony a nástroji, které budete potřebovat pro práci se šablonami. Dokončení tohoto kurzu trvá přibližně **12 minut** , ale skutečný čas se bude lišit podle toho, kolik nástrojů potřebujete nainstalovat.

Tento kurz je první z řad. Jak budete postupovat podle řady, upravíte úvodní šablonu krok za krokem, dokud neprojdete všechny základní části šablony ARM. Tyto prvky jsou stavebními bloky pro mnohem složitější šablony. Doufáme, že na konci řady jste si jistí, že máte jistotu, že vytváříte vlastní šablony a jste připraveni automatizovat nasazení pomocí šablon.

Pokud se chcete dozvědět o výhodách používání šablon a proč byste měli automatizovat nasazení se šablonami, přečtěte si téma [Azure Resource Manager šablony](overview.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="get-tools"></a>Získat nástroje

Pojďme začít tím, že budete mít k dispozici nástroje, které potřebujete k vytváření a nasazování šablon.

### <a name="editor"></a>Editor

Šablony jsou soubory JSON. K vytváření šablon potřebujete dobrý Editor JSON. Doporučujeme Visual Studio Code s rozšířením nástroje Správce prostředků Tools. Pokud potřebujete nainstalovat tyto nástroje, přečtěte si téma [rychlý Start: vytvoření Azure Resource Manager šablon pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Nasazení z příkazového řádku

K nasazení šablony budete taky potřebovat buď Azure PowerShell, nebo rozhraní příkazového řádku Azure. Pokud používáte Azure CLI, musíte mít nejnovější verzi. Pokyny k instalaci najdete v těchto tématech:

- [Instalace prostředí Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace rozhraní příkazového řádku Azure CLI v systému Linux](/cli/azure/install-azure-cli-linux)
- [Instalace Azure CLI v macOS](/cli/azure/install-azure-cli-macos)

Po instalaci Azure PowerShell nebo rozhraní příkazového řádku Azure se ujistěte, že poprvé se přihlašujete. Nápovědu najdete v tématu věnovaném [přihlášení k prostředí PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Pokud používáte Azure CLI, ujistěte se, že máte verzi 2,6 nebo novější. Příkazy zobrazené v tomto kurzu nebudou fungovat, pokud používáte starší verze. Pokud chcete ověřit nainstalovanou verzi, použijte: `az --version` .

V pořádku budete připraveni začít se získáváním informací o šablonách.

## <a name="create-your-first-template"></a>Vytvoření první šablony

1. Otevřete Visual Studio Code s nainstalovaným rozšířením nástroje Správce prostředků Tools.
1. V nabídce **soubor** vyberte **nový soubor** a vytvořte nový soubor.
1. V nabídce **soubor** vyberte **Uložit jako**.
1. Pojmenujte soubor **azuredeploy** a vyberte příponu souboru **JSON** . Úplný název souboru **azuredeploy.jsv**.
1. Uložte soubor do pracovní stanice. Vyberte cestu, která se snadno pamatuje, protože tuto cestu zadáte později při nasazování šablony.
1. Zkopírujte následující kód JSON a vložte ho do souboru:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Toto prostředí VS Code vypadá takto:

    ![Šablona sady Visual Studio Code First Správce prostředků](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Tato šablona neimplementuje žádné prostředky. Začínáme prázdnou šablonou, takže se můžete seznámit s postupem nasazení šablony, přičemž se minimalizuje pravděpodobnost, že došlo k nějakému problému.

    Soubor JSON má tyto prvky:

    - **$Schema**: Určuje umístění souboru schématu JSON. Soubor schématu popisuje vlastnosti, které jsou k dispozici v rámci šablony. Například schéma definuje **prostředky** jako jednu z platných vlastností šablony. Nedělejte si starosti s tím, že datum schématu je 2019-04-01. Tato verze schématu je aktuální a zahrnuje všechny nejnovější funkce. Datum schématu se nezměnilo, protože od jeho úvodu nedošlo k žádným změnám.
    - **contentversion –**: Určuje verzi šablony (například 1.0.0.0). Pro tento prvek můžete zadat libovolnou hodnotu. Tuto hodnotu použijte k dokumentování významných změn v šabloně. Při nasazování prostředků pomocí šablony můžete tuto hodnotu použít k tomu, abyste se ujistili, že je používána pravá šablona.
    - **prostředky**: obsahuje prostředky, které chcete nasadit nebo aktualizovat. V současné době je prázdná, ale později přidáte prostředky.

1. Uložte soubor.

Blahopřejeme, vytvořili jste svou první šablonu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI, přihlaste se pomocí přihlašovacích údajů Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Pokud máte více předplatných Azure, vyberte předplatné, které chcete použít:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Když nasadíte šablonu, zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu k nasazení vytvořte skupinu prostředků buď pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. Vyberte karty v následující části kódu a vyberte si mezi Azure PowerShell a Azure CLI. Příklady rozhraní příkazového řádku v tomto článku se napíší pro prostředí bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell. Použijte skupinu prostředků, kterou jste vytvořili. Zadejte název nasazení, abyste ho mohli snadno identifikovat v historii nasazení. Pro usnadnění práce vytvořte také proměnnou, která ukládá cestu k souboru šablony. Tato proměnná usnadňuje spuštění příkazů nasazení, protože nemusíte znovu zadávat cestu pokaždé, když nasadíte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) rozhraní příkazového řádku Azure CLI.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Příkaz pro nasazení vrátí výsledky. Vyhledejte, pokud `ProvisioningState` chcete zjistit, zda nasazení proběhlo úspěšně.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Stav zřizování nasazení PowerShellu](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Stav zřizování nasazení Azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Pokud se nasazení nepovedlo, použijte k zobrazení protokolů ladění přepínač **ladění** s příkazem nasazení.  Můžete také použít **podrobný** přepínač k zobrazení úplných protokolů ladění.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **skupiny prostředků**.

1. V posledním postupu vyberte nasazení skupiny prostředků. Výchozí název je **myResourceGroup**. V rámci skupiny prostředků se nezobrazí žádný prostředek nasazený.

1. Všimněte si, že v pravém horním rohu přehledu se zobrazí stav nasazení. Vyberte **1 úspěšné**.

   ![Zobrazit stav nasazení](./media/template-tutorial-create-first-template/deployment-status.png)

1. Zobrazí se historie nasazení pro skupinu prostředků. Vyberte **blanktemplate**.

   ![Vybrat nasazení](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Zobrazí se souhrn nasazení. V tomto případě není k dispozici hodně prostředků, protože nebyly nasazeny žádné prostředky. Později v této sérii může být užitečné zkontrolovat souhrn v historii nasazení. Na levé straně si můžete zobrazit vstupy, výstupy a šablony použité během nasazování.

   ![Zobrazit souhrn nasazení](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní provádíte zastavování, možná budete chtít odstranit skupinu prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchou šablonu pro nasazení do Azure. V dalším kurzu přidáte účet úložiště do šablony a nasadíte ho do vaší skupiny prostředků.

> [!div class="nextstepaction"]
> [Přidat prostředek](template-tutorial-add-resource.md)
