---
title: Referenční příručka pro Návrhář zobrazení dlaždice ve službě Azure Monitor | Dokumentace Microsoftu
description: Pomocí návrháře zobrazení ve službě Azure Monitor můžete vytvořit vlastní zobrazení, které se zobrazují na webu Azure Portal a obsahovat nejrůznější vizualizace dat v pracovním prostoru Log Analytics. Tento článek je referenční příručka k nastavení pro dlaždice, které jsou k dispozici do vlastních zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 9c0283081bd7245b1b886ed82ba03130a7a3bf2c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470252"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referenční příručka pro Návrhář zobrazení dlaždice ve službě Azure Monitor
Pomocí návrháře zobrazení ve službě Azure Monitor můžete vytvořit řadu vlastních zobrazení na webu Azure Portal, který může pomoci vám vizualizovat data ve vašem pracovním prostoru Log Analytics. Tento článek je referenční příručka k nastavení pro dlaždice, které jsou k dispozici do vlastních zobrazení.

Další informace o návrháři zobrazení najdete v tématu:

* [Zobrazit návrháře](view-designer.md): Poskytuje přehled o Návrhář zobrazení a postupy pro vytváření a úpravu vlastní zobrazení.
* [Referenční informace k části vizualizace](view-designer-parts.md): Poskytuje referenční příručka k nastavení části vizualizace, které jsou k dispozici do vlastních zobrazení.


K dispozici Návrhář zobrazení dlaždic jsou popsány v následující tabulce:  

| Dlaždice | Popis |
|:--- |:--- |
| [Číslo](#number-tile) |Počet záznamů z dotazu. |
| [Dvě čísla](#two-numbers-tile) |Počty záznamy ze dvou různých dotazů. |
| [Prstencový](#donut-tile) | Graf, který je založena na dotazu, s souhrnnou hodnotu v centru. |
| Spojnicový graf a popisek | Spojnicový graf, který je založen na dotaz a popisek s souhrnnou hodnotu. |
| [Spojnicový graf](#line-chart-tile) |Spojnicový graf, která je založena na dotazu. |
| [Dvě časové osy](#two-timelines-tile) | Sloupcový graf s dvou řad, každou na základě samostatný dotaz. |

Následující části popisují typy dlaždic a jejich vlastnosti podrobně.

> [!NOTE]
> Dlaždice v zobrazení jsou založeny na [protokolu dotazy](../log-query/log-query-overview.md) ve vašem pracovním prostoru Log Analytics. Aktuálně nepodporují [prostředků dotazy napříč](../log-query/cross-workspace-query.md) k načtení dat ze služby Application Insights.

## <a name="number-tile"></a>Dlaždici s číslem
**Číslo** dlaždici se zobrazuje počet záznamů z protokolu dotazu a popisek.

![Dlaždici s číslem](media/view-designer-tiles/tile-number.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| **Dlaždice** | |
| Legenda |Text, který se zobrazí pod hodnotou. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="two-numbers-tile"></a>Dlaždici dvou čísel.
Tuto dlaždici se zobrazuje počet záznamů z dotazů na dva různé protokoly a popisek pro každý.

![Dlaždici dvou čísel.](media/view-designer-tiles/tile-two-numbers.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| **První dlaždice** | |
| Legenda |Text, který se zobrazí pod hodnotou. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| **Druhá dlaždice** | |
| Legenda |Text, který se zobrazí pod hodnotou. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="donut-tile"></a>Prstencový dlaždice
**Prstencový** dlaždici se zobrazuje jedno číslo, které shrnuje hodnotu sloupce v dotazu protokolu. Prstencový graficky zobrazuje výsledky z prvních tří záznamů.

![Prstencový dlaždice](media/view-designer-tiles/tile-donut.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| **Prstencový** | |
| Dotaz |Dotaz, který se spouští pro prstencovém. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. |
| **Prstencový** |**> System center** |
| Text |Text, který se zobrazí v části v prstencovém hodnotu. |
| Operace |Operace, která se provádí na vlastnost value slouží ke shrnutí jako jedinou hodnotu.<ul><li>Součet: Přidejte hodnoty všech záznamů s hodnotou vlastnosti.</li><li>Procento: Procento sečtené hodnoty ze záznamů s hodnotou vlastnosti v porovnání s sečtené hodnoty všechny záznamy.</li></ul> |
| Hodnoty výsledku, které se zobrazí ve středu |Volitelně vyberte znaménko plus (+) přidejte jednu nebo více hodnot. Výsledky dotazu jsou omezené na záznamy s hodnotami vlastností, které zadáte. Pokud jsou přidány žádné hodnoty, jsou zahrnuty všechny záznamy v dotazu. |
| **Prstencový** |**> Další možnosti** |
| Barvy |Barva, která se zobrazí pro každé tři hlavní vlastnosti. Chcete-li určit alternativní barvy pro konkrétní hodnoty vlastností, použijte *rozšířené mapování barev*. |
| Rozšířené mapování barev |Zobrazí barva, která představuje konkrétní hodnoty vlastností. Pokud je hodnota, kterou zadáte v první tři, zobrazí se místo standardní barvu Alternativní barva. Pokud není vlastnost v první tři, barva se nezobrazí. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="line-chart-tile"></a>Dlaždice diagram řádku
Tato dlaždice není spojnicový graf zobrazující více řad z dotazu protokolu v čase. 

![Dlaždice graf a popisek řádku](media/view-designer-tiles/tile-line-chart.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští pro spojnicový graf. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osu x používá tento časový interval. Pokud dotaz nepoužívá *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| **Spojnicový graf** |**> Osa y** |
| Použít logaritmické měřítko |Vyberte tento odkaz použít logaritmické měřítko osy y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží k zobrazení popisků v grafu určující typy hodnot a volitelně pro převod hodnoty. **Typ jednotky** Určuje kategorii jednotky a definuje **aktuální typ jednotky** hodnoty, které jsou k dispozici. Pokud vyberete hodnotu v **převést na** pak číselné hodnoty jsou převedeny z **aktuální jednotky** typ, který **převést na** typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisku osy y *jednotky* typu. Pokud není zadán žádný popisek, pouze *jednotky* typ se zobrazí. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="line-chart-and-callout-tile"></a>Dlaždice graf a popisek řádku
Tuto dlaždici má oba řádek graf této zobrazí více řad z protokolu dotaz nad čas a popisek s souhrnnou hodnotu. 

![Dlaždice graf a popisek řádku](media/view-designer-tiles/tile-line-chart-callout.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští pro spojnicový graf. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osu x používá tento časový interval. Pokud dotaz nepoužívá *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| **Spojnicový graf** |**> Popis obrázku** |
| Název popisku | Text, který se zobrazí nad hodnotou popisku. |
| Název řady |Hodnota vlastnosti řady má být použit jako hodnota popisku. Pokud je k dispozici žádné řady, použijí se všechny záznamy z dotazu. |
| Operace |Operace, která se provádí na vlastnost value slouží ke shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vrácené dotazem.</li><li>Poslední vzorek: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Min: Minimální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> Osa y** |
| Použít logaritmické měřítko |Vyberte tento odkaz použít logaritmické měřítko osy y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží zobrazení popisků, které označují typy hodnot a volitelně pro převedení hodnot. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotky* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typ, který *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisku osy y *jednotky* typu. Pokud není zadán žádný popisek, pouze *jednotky* typ se zobrazí. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="two-timelines-tile"></a>Dvě časové osy dlaždice
**Dvě časové osy** dlaždici se zobrazuje v čase jako sloupcové grafy výsledků dvou dotazů na protokoly. Popisek se zobrazí u každé datové řady. 

![Dvě časové osy dlaždice](media/view-designer-tiles/tile-two-timelines.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části dlaždice. |
| Popis |Text, který se zobrazí v části název této dlaždice. |
| První graf | |
| Legenda |Text, který se zobrazí pod popisek pro první řady. |
| Barva |Barva, která se používá pro sloupce v první řadě. |
| Dotaz na graf |Dotaz, který se spouští pro první řady. Počet záznamů v každém časovém intervalu je reprezentován sloupců v grafu. |
| Operace |Operace, která se provádí na vlastnost value slouží ke shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vrácené dotazem.</li><li>Poslední vzorek: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li></ul> |
| **Druhý graf** | |
| Legenda |Text, který se zobrazí pod popisek druhé řady. |
| Barva |Barva, která se používá pro sloupce v druhé řady. |
| Dotaz na graf |Dotaz, který se spouští pro druhé řady. Počet záznamů v každém časovém intervalu je reprezentován sloupců v grafu. |
| Operace |Operace, která se provádí na vlastnost value slouží ke shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vrácené dotazem.</li><li>Poslední vzorek: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu. |
| **Upřesňující** |**> Ověření toku dat** |
| Povoleno |Pokud byste měli povolit ověření toku dat pro dlaždice, vyberte tento odkaz. Tento přístup poskytuje alternativní zprávu, pokud data nejsou dostupná. Obvykle použít přístup k poskytování zprávy během doby dočasné, při zobrazení je nainstalovaný a data bude k dispozici. |
| Dotaz |Dotaz, který určuje, jestli je k dispozici pro zobrazení dat se spouští. Pokud dotaz nebyly vráceny žádné výsledky, zobrazí se zpráva místo hodnotu hlavního dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz ověření toku dat nevrátí žádná data. Pokud zadáte žádná zpráva *provádění vyhodnocení* se zobrazí stavová zpráva. |


## <a name="next-steps"></a>Další postup
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) pro podporu dotazů v dlaždicích.
* Přidat [části vizualizace](view-designer-parts.md) do vlastních zobrazení.
