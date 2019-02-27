---
title: Azure Monitor dotazů zaznamená do monitorování clusterů Azure HDInsight
description: Zjistěte, jak spouštět dotazy na protokoly Azure monitoru a monitorovat úlohy spuštěné v clusteru služby HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: e1187867fc9da9a89f92d7b321c8703ee7a8a407
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889252"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Zaznamená dotaz Azure Monitor k monitorování clusterů HDInsight

Další informace o tom, jak použít protokoly Azure Monitor k monitorování clusterů Azure HDInsight některé základní scénáře:

* [Analyzovat metriky clusteru HDInsight](#analyze-hdinsight-cluster-metrics)
* [Vyhledat konkrétní zprávy protokolu](#search-for-specific-log-messages)
* [Vytvořit výstrahy pro události](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

* Musíte mít nakonfigurovaný cluster HDInsight používat protokoly Azure monitoru a přidá protokoly Azure monitoru specifických pro cluster HDInsight řešení do pracovního prostoru monitorování. Pokyny najdete v tématu [protokolů pomocí Azure monitoru s clustery HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analyzovat metriky clusteru HDInsight

Zjistěte, jak hledat konkrétní metriky pro váš cluster HDInsight.

1. Otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight z portálu Azure portal.
2. Vyberte **prohledávání protokolů** dlaždici.
3. Zadejte následující dotaz do vyhledávacího pole vyhledejte všechny metriky pro všechny dostupné metriky pro všechny clustery HDInsight umožňují používat protokoly Azure monitoru a pak vyberte **spustit**.

        search *

    ![Hledat všechny metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "hledat všechny metriky")

    Výstup by měl vypadat:

    ![Hledat všechny metriky výstup](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "hledat všechny metriky výstupu")

5. V levém podokně v části **typ**, vyberte metriku, kterou chcete Ponořte se hlouběji do a pak vyberte **použít**. Následující snímek obrazovky ukazuje `metrics_resourcemanager_queue_root_default_CL` je vybrán.

    > [!NOTE]  
    > Budete muset vybrat **[+] Další** tlačítko Najít metriky, které hledáte. Také **použít** tlačítko je v dolní části seznamu, takže musíte přejděte dolů a prohlédněte si ho.

    Všimněte si, že dotaz v textovém poli změní na popsaný v poli zvýrazněný na následujícím snímku obrazovky:

    ![Vyhledejte konkrétní metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "vyhledejte konkrétní metriky")

6. Chcete dostat hlouběji do této konkrétní metriky. Můžete například Upřesnit existující výstup na základě průměrné prostředky používané v 10minutových intervalech, zařazených do kategorií podle názvu clusteru pomocí následujícího dotazu:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Místo upřesnění, na základě průměrné použité prostředky, můžete použít následující dotaz k zpřesnění výsledků na základě když maximální prostředky, které byly použity (a také 90. a 95. percentil), v okně během 10 minut:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Vyhledat konkrétní zprávy protokolu

Zjistěte, jak se vás pod rouškou chybové zprávy během konkrétního časového okna. Zde uvedené kroky se mají zájem na jak vám můžou přijít kdykoliv chybová zpráva je pouze jeden z příkladů. Jakákoli vlastnost, která je k dispozici pro vyhledávání chyb, které se pokoušíte najít, můžete použít.

1. Otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight z portálu Azure portal.
2. Vyberte **prohledávání protokolů** dlaždici.
3. Zadejte následující dotaz pro vyhledání všech chybových zpráv pro všechny clustery HDInsight umožňují použít protokoly Azure monitoru a pak vyberte **spustit**. 

         search "Error"

    Zobrazí se výstup podobný následujícímu následující výstup:

    ![Vyhledat všechny chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "vyhledat všechny chyby výstupu")

4. V levém podokně v části **typ** kategorii, vyberte typ chyby, kterou chcete Ponořte se hlouběji do a potom vyberte **použít**.  Všimněte si, že výsledky jsou vylepšili o pouze zobrazit chyba typu, který jste vybrali.
5. Je můžete podrobněji analyzovat tento seznam určité chybě pomocí možnosti dostupné v levém podokně. Příklad:

    - Chcete-li zobrazit chybové zprávy z konkrétní pracovního uzlu:

        ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "vyhledejte konkrétní chyby výstupu")

    - Pokud chcete zobrazit v určitém čase došlo k chybě:

        ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "vyhledejte konkrétní chyby výstupu")

6. Pokud chcete zobrazit konkrétní chyba. Můžete vybrat **[+] zobrazit víc** podívat se na skutečné chybové zprávě.

    ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "vyhledejte konkrétní chyby výstupu")

## <a name="create-alerts-for-tracking-events"></a>Vytvoření výstrah pro sledování události

Prvním krokem k vytvoření upozornění je můžete přejít na dotaz a který se aktivuje upozornění. Můžete použít jakýkoli dotaz, který chcete vytvořit výstrahu.

1. Otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight z portálu Azure portal.
2. Vyberte **prohledávání protokolů** dlaždici.
3. Spusťte následující dotaz, na kterém chcete vytvořit upozornění a pak vyberte **spustit**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Dotaz obsahuje seznam neúspěšných aplikací spuštěných v clusterech HDInsight.

4. Vyberte **nové pravidlo upozornění** horní části stránky.

    ![Zadejte dotaz k vytvoření výstrahy](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "zadejte dotaz k vytvoření výstrahy")

5. V **vytvořit pravidlo** okno, zadejte dotaz a další podrobnosti a vytvořte výstrahu a vyberte **vytvořit pravidlo upozornění**.

    ![Zadejte dotaz k vytvoření výstrahy](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "zadejte dotaz k vytvoření výstrahy")

Chcete-li upravit nebo odstranit existující výstrahy:

1. Otevřete pracovní prostor Log Analytics z portálu Azure portal.
2. V nabídce vlevo vyberte **výstrah**.
3. Vyberte výstrahu, kterou chcete upravit nebo odstranit.
4. Máte následující možnosti: **Uložit**, **zahodit**, **zakázat**, a **odstranit**.

    ![Upravit upozornění odstranit protokoly sledování Azure HDInsight](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Další informace najdete v tématu [vytvoření, zobrazení a Správa upozornění na metriku pomocí Azure monitoru](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [OMS Log Analytics: Návrhář zobrazení](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Vytvořit, zobrazit a spravovat upozornění na metriku pomocí Azure monitoru](../azure-monitor/platform/alerts-metric.md)
