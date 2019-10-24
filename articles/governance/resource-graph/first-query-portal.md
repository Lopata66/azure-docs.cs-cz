---
title: Spuštění prvního dotazu pomocí Průzkumníka Azure Resource graphu
description: Tento článek vás provede jednotlivými kroky, jak spustit první dotaz z Azure Portal pomocí Průzkumníka Azure Resource graphu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: abee722e725b55933d7ff1acdcd1e9a2e701502b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752152"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu

Výkon Azure Resource graphu je k dispozici přímo v Azure Portal prostřednictvím Průzkumníka Azure Resource graphu. Průzkumník prostředků Resource Explorer poskytuje procházetelné informace o Azure Resource Manager typech prostředků a vlastnostech, které se dají dotazovat. Průzkumník diagramů prostředků také poskytuje čisté rozhraní pro práci s více dotazy, vyhodnocení výsledků a dokonce i převod výsledků některých dotazů do grafu, který je možné připnout na řídicí panel Azure.

Na konci tohoto rychlého startu použijete Azure Portal a Průzkumník diagramů prostředků ke spuštění prvního dotazu grafu prostředku a připnuté výsledky na řídicí panel.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Otevřete [Azure Portal](https://portal.azure.com) pro vyhledání a použití Průzkumníka grafu prostředků podle těchto kroků ke spuštění prvního dotazu na graf prostředku:

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **Průzkumník diagramů prostředků**.

1. V části **dotazu 1** v okně zadejte dotaz `Resources | project name, type | limit 5` a vyberte **Spustit dotaz**.

   > [!NOTE]
   > Jelikož tento příklad dotazu neposkytuje modifikátor řazení, jako je například `order by`, spuštění tohoto dotazu bude pravděpodobně zajišťovat jinou sadu prostředků na žádost.

1. Zkontrolujte odpověď na dotaz na kartě **výsledky** . výběrem karty **zprávy** zobrazíte podrobnosti o dotazu, včetně počtu výsledků a doby trvání dotazu. Případné chyby se zobrazí na této kartě.

1. Aktualizujte dotaz tak, aby `order by` vlastnost **Name** : `Resources | project name, type | limit 5 | order by name asc`. Pak vyberte **Spustit dotaz**.

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Tak se nejdřív omezí rozsah výsledků dotazu a ty se pak seřadí.

1. Aktualizujte dotaz tak, aby nejprve `order by` vlastnost **Name** a pak `limit` na horních 5 výsledků: `Resources | project name, type | order by name asc | limit 5`. Pak vyberte **Spustit dotaz**.

Pokud se konečný dotaz několikrát spustí, za předpokladu, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a podle očekávání – seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

### <a name="schema-browser"></a>Prohlížeč schémat

Prohlížeč schématu je umístěný v levém podokně Průzkumníka grafu prostředků. Tento seznam prostředků zobrazuje všechny _typy_ prostředků Azure, které podporuje Azure Resource Graph a které existují v tenantovi, ke kterému máte přístup. Rozbalením typu prostředku nebo podvlastnostmi se zobrazí podřízené vlastnosti, které lze použít k vytvoření dotazu grafu prostředku.

V poli dotaz vyberte umístění pro typ prostředku `where type =="<resource type>"`. Výběrem jedné z podřízených vlastností přidáte `where <propertyName> == "INSERT_VALUE_HERE"` do pole dotazu.
Prohlížeč schémat je skvělým způsobem, jak zjistit vlastnosti pro použití v dotazech. Nezapomeňte nahradit položku _INSERT \_VALUE \_HERE_ vlastní hodnotou, upravte dotaz pomocí podmínek, operátorů a funkcí, abyste dosáhli zamýšlených výsledků.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Vytvoření grafu z dotazu na graf prostředků

Pokud po spuštění posledního dotazu vyberete kartu **grafy** , zobrazí se zpráva, že sada výsledků dotazu není kompatibilní s vizualizací výsečového grafu. Dotazy, které uvádějí výsledky seznamu, se nedají provést v grafu, ale můžou se zadat i dotazy, které poskytují počty prostředků. Pomocí [ukázkového dotazu – počet virtuálních počítačů podle typu operačního systému](./samples/starter.md#count-virtual-machines-by-os-type)vytvoříme vizualizaci z dotazu na graf prostředků.

1. V části pro **dotaz 1** v okně zadejte následující dotaz a vyberte **Spustit dotaz**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Vyberte kartu **výsledky** a Všimněte si, že odpověď pro tento dotaz poskytuje počty.

1. Vyberte kartu **grafy** . Nyní je výsledkem dotazu vizualizace. Změňte typ z volby _typ grafu.._ . na _sloupcový graf_ nebo _prstencový graf_ pro experimentování s dostupnými možnostmi vizualizace.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Připnutí vizualizace dotazu na řídicí panel

Když máte výsledky z dotazu, který je možné vizuálně vyfiltrovat, můžete tuto vizualizaci dat připnout k některému z vašich řídicích panelů. Po spuštění dotazu výše použijte následující postup:

1. Vyberte **Uložit** a zadejte název virtuální počítače podle typu operačního systému. Potom v dolní části pravého podokna vyberte **Save (Uložit** ).

1. Výběrem **Spustit dotaz** spusťte dotaz nyní, který je uložený.

1. Na kartě **grafy** vyberte vizualizaci dat. Pak vyberte **Připnout na řídicí panel**.

1. Buď vyberte oznámení na portálu, které se zobrazí, nebo v levém podokně vyberte **řídicí panel** .

Dotaz je nyní k dispozici na řídicím panelu s názvem dlaždice, která odpovídá názvu dotazu. Pokud byl dotaz při připnutí neuložený, nazývá se místo toho dotaz 1.

Dotaz a výsledná vizualizace dat se spustí a aktualizuje pokaždé, když se řídicí panel načte, a poskytuje v reálném čase a dynamické poznatky k prostředí Azure přímo ve vašem pracovním postupu.

> [!NOTE]
> Dotazy, které jsou výsledkem seznamu, lze také připnout na řídicí panel. Tato funkce není omezena na vizualizace dat dotazů.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Příklady importu řídicích panelů Průzkumníka diagramů prostředků

Chcete-li poskytnout příklady dotazů na grafy prostředků a jak lze použít Průzkumníka grafu prostředků k vylepšení Azure Portalho pracovního postupu, vyzkoušejte tyto ukázkové řídicí panely.

- [Průzkumník grafu prostředků – ukázkový #1 řídicího panelu](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Example obrázek ukázkového řídicího panelu #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Průzkumník grafu prostředků – ukázkový #2 řídicího panelu](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Example obrázek ukázkového řídicího panelu #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Počty a grafy ve výše uvedených ukázkových snímcích obrazovky řídicího panelu se budou lišit v závislosti na prostředí Azure.

1. Vyberte a Stáhněte si ukázkový řídicí panel, který chcete vyhodnotit.

1. V Azure Portal v levém podokně vyberte **řídicí panel** .

1. Vyberte **nahrát**a pak vyhledejte a vyberte stažený ukázkový soubor řídicího panelu. Pak vyberte **otevřít**.

Automaticky se zobrazí importovaný řídicí panel. Protože teď existuje ve vašem Azure Portal, můžete podle potřeby prozkoumat a dělat změny nebo vytvořit nové řídicí panely z tohoto příkladu a sdílet je s vašimi týmy. Další informace o práci s řídicími panely najdete v tématu [Vytvoření a sdílení řídicích panelů v Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat ukázkové řídicí panely grafu prostředků z prostředí Azure Portal, můžete to udělat pomocí následujících kroků:

1. V levém podokně vyberte **řídicí panel** .

1. V rozevíracím seznamu řídicí panel vyberte ukázkový řídicí panel grafu prostředků, který chcete odstranit.

1. V nabídce řídicího panelu v horní části řídicího panelu vyberte **Odstranit** a potvrďte ji kliknutím na **OK** .

## <a name="next-steps"></a>Další kroky

- Získejte další informace o [dotazovacím jazyce](./concepts/query-language.md)
- Naučte se [prozkoumat prostředky](./concepts/explore-resources.md)
- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Zobrazit ukázky [Starter dotazy](./samples/starter.md)
- Zobrazit ukázky [Pokročilé dotazy](./samples/advanced.md)
- Váš názor na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)