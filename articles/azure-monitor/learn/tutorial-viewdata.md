---
title: Zobrazení nebo analýza shromážděných dat služby Azure Log Analytics | Microsoft Docs
description: Tento článek obsahuje kurz popisující vytváření prohledávání protokolů a analýzu dat uložených v prostředku Log Analytics pomocí portálu pro prohledávání protokolů.  Tento kurz zahrnuje spuštění několika jednoduchých dotazů, které vrátí různé typy dat, a analýzu výsledků.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c64c0837956c59cf7a810bb06ca63bc21c5158d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590196"
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Zobrazení nebo analýza shromážděných dat pomocí prohledávání protokolů v Log Analytics

V Log Analytics můžete využít prohledávání protokolů vytvářením dotazů pro analýzu shromážděných dat a předem vytvořené řídicí panely, které si můžete přizpůsobit pomocí grafických zobrazení nejdůležitějších hledání.  Nadefinovali jste shromažďování provozních dat z virtuálních počítačů Azure a protokolů aktivit a v tomto kurzu se teď naučíte:

> [!div class="checklist"]
> * Jednoduché hledání dat událostí a použití funkcí k úpravě a filtrování výsledků 
> * Práce s daty výkonu

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač [připojený k pracovnímu prostoru služby Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  

Vytvářet a upravovat dotazy a také interaktivně pracovat s vrácenými daty můžete jedním ze dvou způsobů.  Pro základní dotazy použijte stránku Prohledávání protokolů na webu Azure Portal. Pro pokročilé dotazování můžete použít portál pro pokročilou analýzu. Další informace o rozdílech mezi funkcemi na těchto dvou portálech najdete v tématu [Portály pro vytváření a úpravy dotazů na protokoly v Azure Log Analytics](../../azure-monitor/log-query/portals.md).

V tomto kurzu budeme pracovat s prohledáváním protokolů na webu Azure Portal. 

## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Otevření portálu pro prohledávání protokolů 
Začněte otevřením portálu pro prohledávání protokolů.   

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Monitor**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Monitor**.
2. V navigační nabídce služby Monitor vyberte **Log Analytics** a pak vyberte pracovní prostor.

## <a name="create-a-simple-search"></a>Vytvoření jednoduchého hledání
Nejrychlejší způsob načtení nějakých dat, se kterými můžeme pracovat, je použít tento jednoduchý dotaz, který vrátí všechny záznamy v tabulce.  Pokud ke svému pracovnímu prostoru máte připojené nějaké klienty Windows nebo Linuxu, budete mít data v tabulce Event (Windows) nebo Syslog (Linux).

Zadejte do vyhledávacího pole jeden z následujících dotazů a klikněte na tlačítko Vyhledat.  

```
Event
```
```
Syslog
```

Data se vrátí ve výchozím zobrazení seznamu a zobrazí se celkový počet vrácených záznamů.

![Jednoduchý dotaz](media/tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Zobrazí se pouze několik prvních vlastností každého záznamu.  Kliknutím na **zobrazit více** zobrazíte všechny vlastnosti konkrétního záznamu.

## <a name="filter-results-of-the-query"></a>Filtrování výsledků dotazu
Na levé straně obrazovky je podokno filtru, ve kterém můžete do dotazu přidat filtrování, aniž byste ho upravovali přímo.  Pro daný typ záznamu se zobrazí několik vlastností a výběrem jedné nebo několika hodnot vlastností můžete zúžit výsledky hledání.

Pokud pracujete s tabulkou **Event**, zaškrtněte políčko vedle textu **Error** (Chyba) v části **EVENTLEVELNAME** (NÁZEV ÚROVNĚ UDÁLOSTI).   Pokud pracujete s tabulkou **Syslog**, zaškrtněte políčko vedle textu **err** (Chyba) v části **SEVERITYLEVEL** (ÚROVEŇ ZÁVAŽNOSTI).  Tím se dotaz změní na jeden následujících a výsledky se omezí na chybové události.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtr](media/tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Přidejte do podokna filtru vlastnosti tím, že v nabídce vlastností jednoho ze záznamů vyberete **Přidat do filtrů**.

![Nabídka Přidat do filtrů](media/tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Stejný filtr můžete nastavit výběrem možnosti **Filtrovat** v nabídce vlastností záznamu s hodnotou, kterou chcete filtrovat.  

Možnost **Filtrovat** se zobrazí pouze po najetí myši na vlastnosti, jejichž název je modře.  To jsou *prohledávatelná* pole, která se indexují pro vyhledávací podmínky.  Pole zobrazená šedě jsou pole *vhodná pro volnotextové prohledávání*, u kterých je dostupná pouze možnost **Zobrazit odkazy**.  Tato možnost vrátí záznamy obsahující danou hodnotu v jakékoli vlastnosti.

Výsledky můžete seskupit podle jedné vlastnosti výběrem možnosti **Seskupit podle** v nabídce záznamu.  Tím se do vašeho dotazu přidá operátor [summarize](/azure/kusto/query/summarizeoperator), který zobrazí výsledky v grafu.  Seskupení můžete provést podle více než jedné vlastnosti, museli byste však upravit dotaz přímo.  Vyberte nabídku záznamu vedle vlastnosti **Computer** (Počítač) a vyberte **Seskupit podle Computer**.  

![Seskupení podle počítače](media/tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Práce s výsledky
Portál pro prohledávání protokolů obsahuje řadu funkcí pro práci s výsledky dotazu.  Výsledky můžete řadit, filtrovat a seskupovat a díky tomu můžete analyzovat data, aniž byste upravovali samotný dotaz.  Výsledky dotazu ve výchozím nastavení nejsou seřazené.

Pokud chcete data zobrazit v podobě tabulky, která nabízí další možnosti filtrování a řazení, klikněte na **Tabulka**.  

![Zobrazení tabulky](media/tutorial-viewdata/log-search-portal-table-01.png)

Kliknutím na šipku vedle záznamu zobrazíte podrobnosti o daném záznamu.

![Řazení výsledků](media/tutorial-viewdata/log-search-portal-table-02.png)

Výsledky můžete řadit podle jakéhokoli pole kliknutím na záhlaví příslušného sloupce.

![Řazení výsledků](media/tutorial-viewdata/log-search-portal-table-03.png)

Výsledky můžete filtrovat podle konkrétní hodnoty ve sloupci kliknutím na tlačítko filtru a zadáním podmínky filtru.

![Filtrování výsledků](media/tutorial-viewdata/log-search-portal-table-04.png)

Výsledky můžete seskupit podle konkrétního sloupce přetažením záhlaví sloupce do horní části výsledků.  Přetažením více sloupců do horní části můžete výsledky seskupit podle více polí.

![Seskupení výsledků](media/tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Práce s daty výkonu
Data výkonu agentů Windows i Linuxu se ukládají v pracovním prostoru služby Log Analytics v tabulce **Perf**.  Záznamy o výkonu vypadají stejně jako jakékoli jiné záznamy. Napíšeme jednoduchý dotaz, který stejně jako v případě událostí vrátí všechny záznamy o výkonu.

```
Perf
```

![Data výkonu](media/tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Vracení milionů záznamů pro všechny objekty a čítače výkonu však není moc užitečné.  Pomocí stejných metod jako výše můžete data filtrovat nebo zadejte následující dotaz přímo do pole prohledávání protokolů.  Tento dotaz vrátí pouze záznamy o využití procesoru pro počítače s Windows i Linuxem.

```
Perf | where ObjectName == "Processor"  | where CounterName == "% Processor Time"
```

![Využití procesoru](media/tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Tím se data omezí na konkrétní čítač, ale stále se nezobrazují v užitečném formátu.  Data můžete zobrazit ve spojnicovém grafu, ale nejprve je potřeba je seskupit podle polí Computer (Počítač) a TimeGenerated (Čas vygenerování).  Abyste mohli data seskupit podle více polí, musíte dotaz upravit přímo, takže ho upravte na následující dotaz.  Tento dotaz používá funkci [avg](/azure/kusto/query/avg-aggfunction) s vlastností **CounterValue** (Hodnota čítače) k výpočtu průměrných hodinových hodnot.

```
Perf  
| where ObjectName == "Processor"  | where CounterName == "% Processor Time"
| summarize avg(CounterValue) by Computer, TimeGenerated
```

![Graf dat výkonu](media/tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Když jsou teď data vhodně seskupená, můžete je zobrazit ve vizuálním grafu přidáním operátoru [render](/azure/kusto/query/renderoperator).  

```
Perf  
| where ObjectName == "Processor" | where CounterName == "% Processor Time" 
| summarize avg(CounterValue) by Computer, TimeGenerated 
| render timechart
```

![Spojnicový graf](media/tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit základní prohledávání protokolů pro analýzu dat událostí a výkonu.  Přejděte k dalšímu kurzu, kde se dozvíte, jak data vizualizovat vytvořením řídicího panelu.

> [!div class="nextstepaction"]
> [Vytváření a sdílená řídicích panelů Log Analytics](tutorial-logs-dashboards.md)
