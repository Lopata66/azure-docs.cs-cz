---
title: Analýzy v Azure HDInsight Hadoop pomocí Hivu - vědecké zpracování týmových dat
description: Příklady vědecké zpracování týmových dat, které se provedou pomocí Hivu v Azure HDInsight Hadoop pro prediktivní analýzy.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d3c3278a058162632a6ba7ea9731e5f233190700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60804671"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Pomocí Hivu v Azure HDInsight Hadoop data science návody 

Tyto kurzy použití Hivu s clusterem HDInsight Hadoop pro prediktivní analýzy. Postupujte podle kroků uvedených v vědecké zpracování týmových dat. Přehled vědeckého zpracování týmových dat, naleznete v tématu [vědecké zpracování dat](overview.md). Úvod do Azure HDInsight, naleznete v tématu [Úvod do služby Azure HDInsight, technologie hadoop a clustery systému Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Další data science návody, které jsou spouštěny vědecké zpracování týmových dat jsou seskupené podle **platformy** , které používají. Zobrazit [názorné postupy vědeckého zpracování týmových dat provádění](walkthroughs.md) pro rozpis tyto příklady.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Předpovídání spropitného pro taxikáře používání Hive s HDInsight Hadoopem

[Clusterů systému HDInsight Hadoop použijte](hive-walkthrough.md) návod používá data z New Yorku taxi předpovědět: 

- Určuje, zda je placené tip 
- Distribuce částky tip

Tento scénář je implementována pomocí Hivu s [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Zjistíte, jak ukládat, prozkoumejte a funkce zpětnou analýzu dat z veřejně dostupné NYC taxi cesty a tarif datové sady. Můžete také používat Azure Machine Learning pro sestavování a nasazování modelů.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Předpověď kliknutí oznámení o inzerovaném programu pomocí Hivu se službou HDInsight Hadoop

[Použití Azure HDInsight Hadoop clusterů v datové sadě 1 TB](hive-criteo-walkthrough.md) názorný postup využívá veřejně dostupné [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) klikněte na datovou sadu, která předpovědět, jestli jde placenou tip a rozsah částky očekávání. Tento scénář je implementována pomocí Hivu s [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumejte, funkce technik a dolů ukázková data. Využívá Azure Machine Learning k vytváření, trénování a stanovíte jeho skóre binární klasifikační model predikci, jestli uživatel klikne na reklamu. Dojde k závěru návodu ukazuje, jak publikování některého z těchto modelů jako webové služby.


## <a name="next-steps"></a>Další postup

Diskuzi o klíčové součásti, které tvoří vědecké zpracování týmových dat, naleznete v tématu [přehled vědeckého zpracování týmových dat](overview.md).

Diskuzi o životní cyklus vědeckého zpracování týmových dat, který vám pomůže strukturovat vaše projekty datových věd, naleznete v tématu [životní cyklus vědeckého zpracování týmových dat](lifecycle.md). Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. 

