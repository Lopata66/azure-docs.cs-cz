---
title: Připojení prostředí k Power BI-Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak připojit Azure Time Series Insights, abyste Power BI mohli sdílet, sestavovat a zobrazovat data napříč vaší organizací.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75863838"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Vizualizace dat z Time Series Insights v Power BI

Azure Time Series Insights je platforma pro ukládání, správu, dotazování a vizualizaci dat časových řad v cloudu. [Power BI](https://powerbi.microsoft.com) je nástroj pro obchodní analýzu s bohatou schopností vizualizace, který umožňuje sdílet přehledy a výsledky napříč vaší organizací. Obě služby se teď dají integrovat tak, aby co nejlépe Time Series Insights základní možnosti vizualizace i Power BI.

Dozvíte se, jak provést tyto akce:

* Připojení Time Series Insights k Power BI pomocí cloudového konektoru
* Vytvářejte vizuály s daty v Power BI
* Publikování sestavy pro Power BI a sdílení se zbytkem vaší organizace

V tomto případě se naučíte vizualizovat data časových řad prostřednictvím Azure Time Series Insights a vylepšit je pomocí funkcí pro zajištění snadného sdílení dat Power BI.

Nezapomeňte si zaregistrovat [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Stáhněte a nainstalujte si nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Mít nebo vytvořit [instanci služby Azure Time Series Insights Preview](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Konektor Power BI je v prostředích s průběžnými *platbami* , které jsou nakonfigurované pro **záložní úložiště**, v Time Series Insights Preview.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Připojení dat z Time Series Insights k Power BI

Pokud chcete Power BI prostředí Time Series Insights připojit, postupujte takto:

1. Otevřít Time Series Insights Explorer
1. Exportovat data jako dotaz nebo jako nezpracovaná data
1. Otevřít Power BI Desktop
1. Načíst z vlastního dotazu

### <a name="export-data-into-power-bi-desktop"></a>Export dat do Power BI plochy

Jak začít:

1. Otevřete Průzkumníka Time Series Insights Preview a dohlédněte si data.
1. Po vytvoření zobrazení, které jste splnili, přejděte do rozevírací nabídky **Další akce** a vyberte **připojit k Power BI**.

    [![Export Průzkumníku Preview z Time Series Insights](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Nastavte parametry v této kartě:

   1. Zadejte relativní časový rámec, který chcete zobrazit. Pokud máte s existujícím zobrazením spokojeni, ponechte to jako **stávající časový rámec**.
   
   1. Vyberte mezi **agregovanými** a **nezpracovanými událostmi**. 
   
       > [!NOTE]
       > Data můžete kdykoli agregovat později v Power BI, ale po agregaci nelze vrátit zpět na nezpracovaná data. 
       
       > [!NOTE]
       > Pro data na úrovni nezpracované události existuje limit počtu událostí 100 – K.

       [![Připojit](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Pokud jste instanci Time Series Insights pro **záložní úložiště**nenakonfigurovali, zobrazí se upozornění.

       [![Upozornění na teplé úložiště](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > V Azure Portal můžete nakonfigurovat stávající instanci pro **teplé úložiště** .

1. Vyberte **Kopírovat dotaz do schránky**.
1. Nyní spusťte Power BI Desktop.
1. V Power BI Desktop na kartě **Domů** vyberte v levém horním rohu **získat data** a pak **Další**.

    [![Rozevírací seznam domů](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Vyhledejte **Time Series Insights**, vyberte **Azure Time Series Insights (beta)** a pak se **Připojte**.

    [![Připojit Power BI k Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Případně přejděte na kartu **Azure** , vyberte **Azure Time Series Insights (beta)** a pak se **Připojte**.
    
1. Zobrazí se dialogové okno s žádostí o oprávnění k připojení k prostředkům třetích stran. Vyberte **pokračovat**.

    [![Zvolit vytvořit vlastní dotaz](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. V rozevírací nabídce v části **zdroj dat**vyberte možnost **vytvořit vlastní dotaz**. Vložte ze schránky do volitelného pole **vlastní dotaz (volitelné)** níže a potom stiskněte **OK**.

    [![Předejte vlastní dotaz a vyberte OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Tabulka dat se teď načte. Stisknutím klávesy **Load** se načtěte do Power BI.

    [![Zkontrolujte načtená data v tabulce a vyberte načíst.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Pokud jste dokončili tento postup, přeskočte dopředu k další části.

## <a name="create-a-report-with-visuals"></a>Vytvoření sestavy s vizuály

Teď, když jste data importovali do Power BI, je čas sestavovat sestavu s vizuály.

1. Na levé straně okna se ujistěte, že jste vybrali zobrazení **sestavy** .

    [![Vybrat zobrazení sestavy](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Ve sloupci **vizualizace** vyberte svůj vizuál výběru. Vyberte například **Spojnicový graf**. Tím se na plátno přidá prázdný spojnicový graf.
 
1.  V seznamu **pole** vyberte možnost **časové razítko** a přetáhněte ji do pole **osa** pro zobrazení položek podél osy X.

1.  V seznamu **pole** vyberte **TimeSeriesId** a přetáhněte ji do pole **hodnoty** , abyste zobrazili položky podél osy Y.

    [![Vytvoření spojnicového grafu](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Pokud chcete na plátno přidat další graf, vyberte kdekoliv na plátně mimo spojnicový graf a tento postup opakujte.

    [![Vytvoření dalších grafů ke sdílení](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Jakmile sestavu vytvoříte, můžete ji publikovat ve službě Power BI Reporting Services.

## <a name="advanced-editing"></a>Rozšířené úpravy

Pokud jste již datovou sadu načetli v Power BI, ale chcete upravit dotaz (například parametry data a času nebo ID prostředí), můžete to provést prostřednictvím funkce Rozšířený editor Power BI. Další informace najdete v [dokumentaci k Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Jako přehled:

1. V Power BI Desktop vyberte **Upravit dotazy**.
1. Stiskněte **Rozšířený editor**.

    [![Upravit dotazy v Rozšířený editor](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Upravte datovou část JSON podle potřeby.
1. Vyberte **Hotovo** a pak **zavřete & použít** v **okně editoru Power Query**.

Rozhraní nyní bude odrážet požadované změny, které jste použili.  

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [Power BI konceptech konektoru](https://docs.microsoft.com/power-bi/desktop-query-overview) pro Azure Time Series Insights.

* Přečtěte si další informace o [Power BI desktopu](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Přečtěte si Time Series Insights v Průzkumníkovi [GA](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) a [Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
