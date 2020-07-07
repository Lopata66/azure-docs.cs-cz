---
title: 'Rychlý Start: vytvoření sdíleného dotazu se šablonami'
description: V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření sdíleného dotazu pro diagram prostředků, který počítá virtuální počítače podle operačního systému.
ms.date: 07/06/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 3cdcff4898a8644008193943a243be4a2ef9e8c4
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85969801"
---
# <a name="quickstart-create-a-shared-query-by-using-an-azure-resource-manager-template"></a>Rychlý Start: vytvoření sdíleného dotazu pomocí šablony Azure Resource Manager

Dotazy na grafy prostředků se dají uložit jako _soukromý dotaz_ nebo do _sdíleného dotazu_. Privátní dotaz je uložený v profilu portálu jednotlivců a není viditelný pro ostatní. Sdílený dotaz je objekt Správce prostředků, který se dá sdílet s ostatními prostřednictvím oprávnění a přístupu založeného na rolích. Sdílený dotaz nabízí běžné a konzistentní provádění zjišťování prostředků. Tento rychlý Start používá šablonu Azure Resource Manager (šablonu ARM) k vytvoření sdíleného dotazu.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-shared-query"></a>Vytvořit sdílený dotaz

V tomto rychlém startu vytvoříte sdílený dotaz s názvem _počet virtuálních počítačů podle operačního systému_. Pokud chcete tento dotaz vyzkoušet v sadě SDK nebo na portálu pomocí Průzkumníka grafů prostředků, přečtěte si téma [Počet ukázek – počet virtuálních počítačů podle typu operačního systému](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

Prostředek definovaný v šabloně je:

- [Microsoft. ResourceGraph/– dotazy](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Nasazení šablony

> [!NOTE]
> Služba Azure Resource Graph je zadarmo. Další informace najdete v tématu [Přehled Azure Resource graphu](./overview.md).

1. Vyberte následující obrázek, přihlaste se k webu Azure Portal a otevřete šablonu:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení šablony ARM pro vytvoření sdíleného dotazu do Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Vyberte nebo zadejte následující hodnoty:

   | Name | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **vytvořit novou**, zadejte název a pak vyberte **OK**. |
   | Umístění | Vyberte oblast. Například **USA – střed**. |
   | Název dotazu | Ponechejte výchozí **počet virtuálních počítačů v operačním systému**. |
   | Dotaz na kód | Ponechte výchozí hodnotu.`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Popis dotazu | Ponechte výchozí hodnotu, kterou **tento sdílený dotaz počítá s počtem prostředků virtuálního počítače a shrnuje podle typu operačního systému.** |
   | Souhlasím s podmínkami a ujednáními uvedenými nahoře | Vybrali |

1. Vyberte **Koupit**.

Některé další zdroje informací:

- Další šablony ukázek najdete v tématu [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Odkaz na šablonu zobrazíte tak, že přejdete na [odkaz šablony Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Informace o vývoji šablon ARM najdete v [dokumentaci Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Pokud chcete zjistit nasazení na úrovni předplatného, přečtěte si téma [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Chcete-li spustit nový sdílený dotaz, postupujte podle následujících kroků:

1. Na panelu hledání na portálu vyhledejte dotazy na **grafy prostředků** a vyberte je.

1. Vyberte sdílený dotaz s názvem **počet virtuálních počítačů podle operačního systému**a pak vyberte kartu **výsledky** na stránce **Přehled** .

Alternativně lze sdílený dotaz otevřít z Průzkumníka grafu prostředků:

1. Na panelu hledání na portálu vyhledejte **Průzkumník diagramů prostředků** a vyberte ho.

1. Vyberte tlačítko **Otevřít dotaz** .

1. Změňte **typ** na _sdílené dotazy_. Pokud v seznamu nevidíte **počet virtuálních počítačů podle OS** , použijte k omezení výsledků pole Filtr. Jakmile se sdílený dotaz **počet virtuálních počítačů podle operačního systému** zobrazí, vyberte jeho název.

1. Po načtení dotazu vyberte tlačítko **Spustit dotaz** . Výsledky se zobrazují na kartě **výsledky** níže.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořený sdílený dotaz, postupujte podle následujících kroků:

1. Na panelu hledání na portálu vyhledejte dotazy na **grafy prostředků** a vyberte je.

1. Nastavte zaškrtávací políčko vedle sdíleného dotazu s názvem **počet virtuálních počítačů podle operačního systému**.

1. Vyberte tlačítko **Odstranit** podél horní části stránky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili sdílený dotaz Resource Graph.

Další informace o sdílených dotazech najdete v kurzu pro:

> [!div class="nextstepaction"]
> [Správa dotazů v Azure Portal](./tutorials/create-share-query.md)