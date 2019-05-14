---
title: Diagnostika problémů s výkonem pomocí Azure Application Insights | Dokumentace Microsoftu
description: Kurz popisující, jak v aplikaci vyhledat a diagnostikovat problémy s výkonem pomocí Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f906ab5db35ce8b239eceac9cdc8244f230f5a77
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596022"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Vyhledání a diagnostika problémů s výkonem pomocí Azure Application Insights

Azure Application Insights shromažďuje telemetrii z vaší aplikace a pomáhá tak analyzovat její provoz a výkon.  Tyto informace můžete využít k identifikaci problémů, ke kterým může docházet, nebo k identifikaci zlepšení aplikace, která by měla největší dopad na uživatele.  Tento kurz vás provede procesem analýzy výkonu serverových komponent vaší aplikace i z pohledu uživatele.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace výkonu operací na straně serveru
> * Analýza operací serveru a určení původní příčiny pomalého výkonu
> * Identifikace nejpomalejších operací na straně klienta
> * Analýza podrobností o zobrazení stránek pomocí dotazovacího jazyka


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
- Nasadit aplikaci .NET do Azure a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Povolit Application Insights Profiler](../../azure-monitor/app/profiler.md#installation) pro vaši aplikaci.

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identifikace pomalých operací serveru
Application Insights shromažďuje podrobnosti o výkonu různých operací ve vaší aplikaci. Díky identifikaci operací, které trvají nejdéle, můžete diagnostikovat potenciální problémy nebo lépe cílit probíhající vývoj a vylepšit tak celkový výkon aplikace.

1. Vyberte **Application Insights** a pak vyberte své předplatné.  
1. Panel **Výkon** otevřete výběrem možnosti **Výkon** v nabídce **Prozkoumat** nebo kliknutím na graf **Doba odezvy serveru**.

    ![Výkon](media/tutorial-performance/performance.png)

2. Na panelu **Výkon** se zobrazí počet a průměrná doba trvání jednotlivých operací aplikace.  Tyto informace můžete využít k identifikaci operací, které mají největší dopad na uživatele. V tomto příkladu jsou vzhledem k dlouhé době trvání a počtu volání vhodnými kandidáty na prošetření operace **GET Customers/Details** a **GET Home/Index**.  Jiné operace můžou mít delší dobu trvání, ale volají se jen zřídka, takže by jejich vylepšení mělo minimální vliv.  

    ![Panel Výkon](media/tutorial-performance/performance-blade.png)

3. Graf aktuálně ukazuje průměrnou dobu trvání vybraných operací v průběhu času. Pokud chcete najít problémy s výkonem, můžete přepnout na 95. percentil. Připnutím na graf přidejte operace, které vás zajímají.  Ukazuje se, že by bylo vhodné prošetřit několik vrcholů.  Proveďte další izolaci zúžením časového okna grafu.

    ![Připnutí operací](media/tutorial-performance/pin-operations.png)

4.  Panel Výkon na pravé straně ukazuje distribuci doby trvání různých požadavků pro vybranou operaci.  Zmenšete časové okno tak, aby začínalo přibližně na 95. percentilu. Na kartě přehledu Nejčastější 3 závislosti můžete na první pohled zjistit, že na pomalé transakce mají pravděpodobně vliv externí závislosti.  Kliknutím na tlačítko s počtem ukázek zobrazíte seznam ukázek. Pak můžete vybrat libovolnou ukázku a zobrazit podrobnosti o transakci.

    ![Distribuce doby trvání](media/tutorial-performance/duration-distribution.png)

5.  Na první pohled vidíte, na celkovou dobu trvání transakce má největší vliv volání tabulky Azure Fabrikamaccount. Také vidíte, že výjimka způsobila jeho selhání. Kliknutím na libovolnou položku v seznamu můžete na pravé straně zobrazit její podrobnosti. [Další informace o prostředí pro diagnostiku transakcí](../../azure-monitor/app/transaction-diagnostics.md)

    ![Detaily operace](media/tutorial-performance/operation-details.png)
    

6.  **Profiler** pomáhá s podrobnější diagnostikou na úrovni kódu díky zobrazení skutečného kódu spuštěného pro příslušnou operaci a času, který zabraly jednotlivé kroky. Vzhledem k tomu, že se profiler spouští pravidelně, některé operace nemusejí mít trasování.  V průběhu času by trasování mělo mít více operací.  Pokud chcete pro operaci spustit profiler, klikněte na **Trasování Profileru**.
5.  Trasování zobrazí pro každou operaci jednotlivé události, takže můžete diagnostikovat původní příčinu v průběhu celé operace.  Klikněte na jeden z horních příkladů s nejdelší dobou trvání.
6.  Kliknutím na **Zobrazit kritickou cestu** zvýrazněte konkrétní cestu událostí, které se nejvíce podílejí na celkové době trvání operace.  V tomto příkladu vidíte, že nejpomalejší volání pochází z metody *FabrikamFiberAzureStorage.GetStorageTableData*. Částí, která trvá nejdéle, je metoda *CloudTable.CreateIfNotExist*. Pokud se tento řádek kódu provádí při každém volání funkce, budou se spotřebovávat zbytečná síťová volání a prostředky procesoru. Nejlepším způsobem, jak kód opravit, je vložit tento řádek do některé metody po spuštění, která se provede pouze jednou. 

    ![Podrobnosti v profileru](media/tutorial-performance/profiler-details.png)

7.  **Tip k výkonu** v horní části obrazovky podporuje vyhodnocení, že důvodem nadměrné doby trvání je čekání.  Kliknutím na odkaz **čekáním** otevřete dokumentaci k interpretaci různých typů událostí.

    ![Tip k výkonu](media/tutorial-performance/performance-tip.png)

8.  Pokud potřebujete další analýzu, můžete kliknout na **Stáhnout trasování .etl** a stáhnout trasování do sady Visual Studio.

## <a name="use-analytics-data-for-server"></a>Použití analytických dat pro server
Application Insights Analytics poskytuje bohatý dotazovací jazyk umožňující analýzu všech dat shromážděných službou Application Insights.  Můžete ho použít k provádění hloubkové analýzy dat o požadavcích a výkonu.

1. Vraťte se na panel s podrobnostmi o operaci a klikněte na tlačítko Analytics.

    ![Tlačítko Analytics](media/tutorial-performance/server-analytics-button.png)

2. Na panelu se otevře Application Insights Analytics s dotazem pro každé zobrazení.  Tyto dotazy můžete spustit tak, jak jsou, nebo je upravit podle vlastních potřeb.  První dotaz zobrazí dobu trvání této operace v průběhu času.

    ![Analýza](media/tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identifikace pomalých operací klienta
Kromě identifikace procesů serveru, které je potřeba optimalizovat, dokáže Application Insights provádět analýzu i z pohledu klientských prohlížečů.  To vám může pomoct identifikovat potenciální vylepšení komponent klienta a dokonce i identifikovat problémy s různými prohlížeči nebo umístěními.

1. V části **Prozkoumat** vyberte **Prohlížeč** a otevřete souhrn prohlížeče.  Ten poskytuje vizuální souhrn různých telemetrií vaší aplikace z pohledu prohlížeče.

    ![Souhrn prohlížeče](media/tutorial-performance/browser-summary.png)

2.  Posuňte se dolů k části **Které moje stránky jsou nejpomalejší?**.  V této části se zobrazí seznam stránek ve vaší aplikaci, jejichž načtení trvalo klientům nejdéle.  Tyto informace můžete použít k určení priority stránek, které mají nejvýraznější dopad na uživatele.
3.  Kliknutím na jednu ze stránek otevřete panel **Zobrazení stránky**.  V tomto příkladu ukazuje stránka **/FabrikamProd** nadměrnou průměrnou dobu trvání.  Panel **Zobrazení stránky** obsahuje podrobnosti o této stránce, včetně rozpisu různých rozsahů doby trvání.

    ![Zobrazení stránky](media/tutorial-performance/page-view.png)

4.  Klikněte na nejdelší dobu trvání a prozkoumejte podrobnosti o příslušných požadavcích.  Pak klikněte na jednotlivý požadavek a zobrazte podrobnosti o klientovi požadujícím příslušnou stránku, včetně typu prohlížeče a jeho umístění.  Tyto informace vám můžou pomoct s určením, jestli neexistují problémy s výkonem souvisejíc s konkrétními typy klientů.

    ![Podrobnosti o požadavku](media/tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Použití analytických dat pro klienta
Propojením shromážděných dat o výkonu serveru zpřístupňuje Application Insights veškerá data klientů pro hloubkovou analýzu pomocí Analytics.

1. Vraťte se do souhrnu prohlížeče a klikněte na ikonu Analytics.

    ![Ikona Analytics](media/tutorial-performance/client-analytics-icon.png)

2. Na panelu se otevře Application Insights Analytics s dotazem pro každé zobrazení. První dotaz zobrazí dobu trvání různých zobrazení stránek v průběhu času.

    ![Analýza](media/tutorial-performance/client-analytics.png)

3.  Inteligentní diagnostika je funkce Application Insights Analytics, která v datech identifikuje jedinečné vzory.  Když kliknete na tečku inteligentní diagnostiky ve spojnicovém grafu, spustí se stejný dotaz bez záznamů, které anomálii způsobily.  Podrobnosti o těchto záznamech se zobrazí v části dotazu s komentáři, takže můžete identifikovat vlastnosti zobrazení stránek, které způsobují nadměrnou dobu trvání.

    ![Inteligentní diagnostika](media/tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Další postup
Nyní, když jste se naučili, jak identifikovat výjimky za běhu, přejděte k dalšímu kurzu, ve kterém zjistíte, jak jako reakci na chyby vytvářet upozornění.

> [!div class="nextstepaction"]
> [Upozornění na stav aplikace](../../azure-monitor/learn/tutorial-alert.md)
