---
title: Spravovat rozpočty Cloudyn v Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže rychle vytvořit rozpočtů a zahájení správy ve službě Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: e6afa7eb91daec95c44d76007ea793fca90b61bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009112"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Správa Azure rozpočtů s Cloudyn

Nastavení rozpočtů a na základě rozpočtu výstrahy zobrazíte nápovědu, jak zlepšit cloudu zásad správného řízení a zodpovědnost. Tento článek vám pomůže rychle vytvořit rozpočtů a zahájení správy ve službě Cloudyn.

Když máte účet organizace nebo MSP, můžete přiřadit různé organizační jednotky, oddělení nebo jiná entita náklady na měsíční kvóty rozpočtu struktury entity hierarchické náklady. Pokud máte účet Premium, můžete použít funkce správy rozpočtu, který se následně použije na vaše cloudové výdaje. Jsou všechny rozpočty přiřadit ručně.

Založené na přiřazené rozpočty, můžete nastavit prahové hodnoty výstrahy podle procenta z rozpočtu, který je zpracován a definovat závažnost jednotlivých prahovou hodnotu.

Rozpočet sestavy zobrazit přiřazené rozpočtu. Uživatelé můžou zobrazit po jejich útraty přes, v části nebo nominální s jejich využití v čase. Když vyberete **zobrazit/skrýt pole** v horní části stránky sestavy rozpočtu, můžete zobrazit náklady, rozpočet, celkové náklady nebo celkový rozpočet.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace o rozpočtech ve službě Cost Management najdete v tématu [vytvořit a spravovat rozpočty](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Vytvoření rozpočtů

Při vytváření rozpočtu, nastavte pro fiskální rok a platí pro konkrétní entitu.

Vytvoření rozpočtu a přiřaďte ho do entity:

1. Přejděte do **náklady** &gt; **Cost Management** &gt; **rozpočtu**.
2. Na stránce Správa rozpočtu v rámci **entity**, vyberte entitu, ve kterém chcete vytvořit rozpočtu.
3. V roce rozpočtu vyberte v roce, ve kterém chcete vytvořit rozpočtu.
4. Pro každý měsíc nastavte hodnotu rozpočtu. Jakmile budete hotovi, klikněte na tlačítko **Uložit**.
V tomto příkladu měsíční rozpočet. června 2018 nastavena na 135,000 $. Celkový rozpočet rok je 1,615,000.00 $.
![Vytvořit stránku rozpočtu, kde nastavujete rozpočet pro každý měsíc](./media/manage-budgets/set-budget.png)


Import souboru pro roční rozpočtu:

1. V části **akce**vyberte **exportovat** stáhnout prázdnou šablonu sdíleného svazku clusteru používat jako váš základ pro rozpočtu.
2. Zadejte soubor CSV s položky vašeho rozpočtu a uloží do místního prostředí.
3. V části **akce**vyberte **Import**.
4. Vyberte uložený soubor a potom klikněte na tlačítko **OK**.

Export rozpočtu dokončené jako soubor CSV, v části **akce**vyberte **exportovat** ke stažení souboru.

## <a name="view-budget-in-reports"></a>Zobrazit rozpočtu v sestavách

Po dokončení se zobrazí vašemu rozpočtu v Většina sestav nákladů v části **náklady** &gt; **Cost Analysis** a v sadě Visual Studio náklady. Sestava rozpočtu v čase. Můžete také naplánovat sestavy na základě prahových hodnot rozpočtu pomocí **akce**.

Tady je příklad sestavy analýzy nákladů. Zobrazuje celkový rozpočet a náklady na typy úloh a využití od začátku roku.

![Příklad sestavy analýzy nákladů s rozpočtu](./media/manage-budgets/cost-analysis-budget-example.png)

V tomto příkladu se předpokládá aktuální datum je 22. června. Bezplatně. června 2018 se $71,611.28 ve srovnání s měsíční rozpočtu 135,000 $. Náklady jsou mnohem nižší než měsíční rozpočtu, protože stále existují osm dní útraty do konce měsíce.

Dalším způsobem, jak zobrazit sestavu je podívat se na celkové náklady vs vašemu rozpočtu. V části zobrazíte souhrnné náklady **zobrazit/skrýt pole**vyberte **celkové náklady** a **celkový rozpočet**. Tady je příklad ukazující celkové náklady od začátku roku.

![Příklad sbírají náklady a celkový rozpočet je znázorněno v sadě Visual Studio náklady. Sestava rozpočtu v čase](./media/manage-budgets/accumulated-budget.png)

Někdy v budoucnu vaše celkové náklady může překročit vašemu rozpočtu. Můžete snadněji uvidíte, že pokud změníte zobrazení grafu _řádku_ typu.

![Rozpočtu ukazuje spojnicový graf v náklady podle měsíců](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Vytvářet upozornění na rozpočet filtr

V předchozím příkladu vidíte, že celkové náklady dosaženy rozpočtu. Automatické rozpočtu výstrahy můžete vytvořit tak, aby se oznámení, když výdaje přístupy nebo překračuje vašemu rozpočtu. Výstraha představuje v podstatě naplánované sestavy s prahovou hodnotou. Zahrnout metriky pro prahovou hodnotu pro výstrahu rozpočtu:

- Zbývající náklady a rozpočet – chcete-li určit prahové hodnoty měny
- Procento nákladů a rozpočet – k určení procentuální hodnota prahová hodnota

Pojďme se podívat na příklad.

V sadě Visual Studio náklady. Rozpočet přes čas sestavy, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Na kartě prahovou hodnotu vyberte metriku prahovou hodnotu. Například **rozpočet vs procento nákladů**. Vyberte typ výstrahy a zadejte hodnotu v procentech rozpočtu. Pokud chcete upozorňování pouze jednou, vyberte **počet po sobě jdoucích výstrah** a pak zadejte _1_. Klikněte na **Uložit**.

![Vytváření upozornění na rozpočet Save plánu nebo momentální tohoto pole sestavy](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první kurz pro Cloudyn, přečtěte si ho na [kontrola využití a nákladů](tutorial-review-usage.md).
- Další informace o [sestavy jsou k dispozici ve službě Cloudyn](use-reports.md).
