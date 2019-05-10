---
title: Zkoumání dat pomocí Průzkumníka služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí Průzkumníka služby Azure Time Series Insights ve webovém prohlížeči se krátce zobrazit globální přehled o velké objemy dat a ověření IoT prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0f22a0245d002b94d9fc0004214c37944350e262
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412953"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník služby Azure Time Series Insights

Tento článek popisuje funkce a možnosti Obecné dostupnosti Azure Time Series Insights [explorer webové aplikace](https://insights.timeseries.azure.com/). V Průzkumníku Time Series Insights předvádí možnosti služeb výkonné datové vizualizace poskytovaných službou a je přístupná v rámci svého vlastního prostředí.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o vašich dat, která umožňuje rychle ověřit vaše řešení IoT a vyhnout se nákladným prostojům důležitých zařízení. Můžete odhalovat skryté trendy, detekovat anomálie a provádět analýzy hlavních příčin téměř v reálném čase. V Průzkumníku Time Series Insights je momentálně ve verzi public preview.

> [!TIP]
> Prohlídku s průvodcem prostřednictvím ukázkovém prostředí najdete v článku [rychlý start Azure čas Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-using-the-time-series-insights-explorer-br"></a>Další informace o dotazování dat pomocí Průzkumníka služby Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Podívejte se na předchozí video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Začínáme s Azure akcelerátorů řešení IoT pomocí služby TSI"</a>.

## <a name="prerequisites"></a>Požadavky

Než použijete Průzkumníka služby Time Series Insights, musíte mít:

- Vytvoření prostředí Time Series Insights. Další informace najdete v tématu [jak začít pracovat s Time Series Insights](./time-series-insights-get-started.md).
- [Poskytnutí přístupu](time-series-insights-data-access.md) ke svému účtu v prostředí.
- Přidat [služby IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) nebo [centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) zdroje událostí do něj.

## <a name="explore-and-query-data"></a>Prozkoumejte a dotazování dat

Během několika minut připojit váš zdroj událostí do prostředí Time Series Insights vám umožní zkoumat a dotazovat data časových řad.

1. Pokud chcete spustit, otevřete [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com/) ve webovém prohlížeči a vyberte prostředí v levé části okna. Všechna prostředí, ke kterým máte přístup k jsou uvedeny v abecedním pořadí.

1. Po výběru prostředí použít **FROM** a **na** konfigurace v horní části stránky, nebo klikněte na tlačítko a přetáhněte požadované časové období.  Klikněte na lupu v pravé, horní části nebo klikněte pravým tlačítkem myši nad vybraný časový rozsah a vyberte **hledání**.  

1. Můžete také dostupnost automaticky aktualizovat po minutách, tak, že vyberete **automaticky na** tlačítko. **Automaticky na** tlačítka se vztahuje pouze na graf dostupnosti není obsahu hlavní vizualizace.

1. Všimněte si ikony cloudu Azure přejdete na vaše prostředí na webu Azure Portal.

   [![Prostředí Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. V dalším kroku se zobrazí graf, který zobrazuje počet všech událostí během vybraný časový rozsah.  Zde máte několik ovládacích prvků:

    **Podmínky, Editor Panel**:  Místo termín je, kde dotaz vašem prostředí.  Se nachází na levé straně obrazovky:
      - **Míra**:  Tento rozevírací seznam ukazuje všechny číselné sloupce (**zdvojnásobí**)
      - **Rozděleno podle**: Tento rozevírací seznam zobrazuje zařazené do kategorií sloupce (**řetězce**)
      - Můžete povolit schodovou interpolaci, zobrazí minimální a maximální a upravit osu y z ovládacích panelů vedle k měření.  Kromě toho můžete upravit, jestli se data zobrazená počet, průměr nebo součet data.
      - Můžete přidat až pět podmínky zobrazíte na stejnou osu x.  Použití **Rozbalovat podrobnosti kopírování** přidat další období nebo klikněte na tlačítko **přidat** tlačítko Přidat novou podmínku.

        [![Panel podmínek editoru](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikátu**:  Predikát umožňuje rychle vyfiltrovat události pomocí sady operandy uvedených níže. Pokud spustíte hledání kliknutím vyberete /, predikátu se automaticky aktualizují na základě danému hledání. Operand podporované typy patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, časový interval       |         |
         |`=`, `!=`, `<>`     | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL        |         |
         |IN     | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL        |  Všechny operandy musí být stejného typu nebo konstanta NULL.        |
         |HAS     | String        |  Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a NULL nejsou povoleny.       |

      - **Příklady dotazů**

         [![Příklady dotazů](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. **Velikost intervalu** posuvník nástroj umožňuje přiblížit nebo intervaly oddálit za stejný časový interval.  To zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky, které ukazují smooth trendy dolů řezy malá jako na milisekundy, což umožní zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z výběru. Vyrovnávání rozlišení, rychlost dotazů a členitosti.

1. **Čas štětce** nástroj umožňuje snadno přejít z jednoho období do jiného umístění intuitivního uživatelského prostředí front a System center pro bezproblémové přesouvání mezi časových rozsahů.

1. **Uložit** příkaz umožňuje uložit aktuální dotaz a povolte její sdílení s ostatními uživateli prostředí. Pomocí **otevřít**, můžete zobrazit všechny uložené dotazy a jiných uživatelů v prostředí máte přístup k žádné sdílené dotazy.

   [![Dotazy](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Vizualizace dat

1. **Perspektiva** nástroj poskytuje současně zobrazit až čtyři jedinečný dotazů. Tlačítko pro zobrazení perspektivy najdete v pravém horním rohu grafu.  

   [![Perspektiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. **Grafu** umožňuje vizuálně procházejte data. Nástroje grafu patří:

    - **Vyberte a klikněte na příkaz**, která umožňuje výběr konkrétní časový rozsah nebo jedné datové řady.  
    - Během doby span výběr, můžete přiblížit nebo prozkoumat události.  
    - V rámci datové řady můžete řadu rozdělit podle jiného sloupce, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, odešlete zprávu ping onu nebo prozkoumat události z vybraného řady.
    - V oblasti filtrů nalevo od grafu můžete zobrazit všechny řady zobrazených dat a změnit pořadí podle hodnoty nebo název, zobrazit všechny datové řady nebo libovolné připnuté nebo nepřipnuté řady.  Můžete také vybrat jednu řadu dat a řadu rozdělit podle jiného sloupce, přidat řadu jako nový termín, zobrazit jenom vybrané řady, vyloučit vybrané řady, připnout onu nebo prozkoumat události z vybraného řady.
    - Při prohlížení současně více sad podmínek, zásobník, zrušit seskupení, zobrazit další data o datové řady a použít stejné osy y napříč všemi podmínkami pomocí tlačítka v pravém horním rohu grafu.

    [![Nástroje grafu](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. **Heatmapu** umožňuje rychle odhalovat případné problémy jedinečný nebo neobvyklé datové řady v daný dotaz. Pouze jeden hledaný termín lze vizualizovat jako heatmapu.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. **Události**:  Pokud zvolíte zkoumat události při výběru nebo pravým tlačítkem myši nad panel událostí je k dispozici.  Tady můžete zobrazit všechny nezpracovaných událostí a export událostí jako soubory JSON nebo CSV. Time Series Insights ukládá všechny nezpracovaná data.

    [![Události](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Klikněte na tlačítko **statistiky** kartu po zkoumání události k vystavení vzory a statistiky sloupce.  

    - **Vzory**: Tato funkce proaktivně zobrazí nejvíce statisticky významná vzory ve vybrané datové oblasti. To vám přišla odpadne podívat se na tisíce událostí, abyste pochopili, jaké vzorce zaručujete nejvíce času i energie. Kromě toho Time Series Insights vám umožní přejít přímo do těchto statisticky významná vzory pokračovat provádění analýzy. Tato funkce je také užitečné pro následné vyšetřování historická data.

    - **Statistiky sloupce**:  Statistiky sloupce poskytují grafů a tabulek, které rozdělení dat každého sloupce vybrané datové řady za vybrané časové období.  

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Právě jste viděli různé funkce a možnosti dostupné v rámci webové aplikace Průzkumníka Time Series Insights.

## <a name="next-steps"></a>Další postup

- Další informace o [diagnostiku a řešení problémů](time-series-insights-diagnose-and-solve-problems.md) ve vašem prostředí Time Series Insights.

- Využijte s průvodcem [rychlý start Azure čas Series Insights](time-series-quickstart.md) tour.
