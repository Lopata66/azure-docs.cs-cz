---
title: Vysoká dostupnost s Apache Kafka - Azure HDInsight
description: Zjistěte, jak zajistit vysokou dostupnost s využitím Apache Kafka ve službě Azure HDInsight. Naučte se obnovit rovnováhu replik oddílů v Kafka, aby byly v různých doménách selhání v rámci oblasti Azure, která obsahuje HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: a90be471ca068869ee26fb02bba77dfdd476a44e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435293"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Vysoká dostupnost dat s využitím Apache Kafka ve službě HDInsight

Zjistěte, jak nakonfigurovat repliky oddílů pro témata Apache Kafka tak, aby využívaly výhod základní konfigurace hardwarového racku. Tato konfigurace zajišťuje dostupnost dat uložených v Apache Kafka ve službě HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Poruchy a aktualizace domén s Apache Kafka

Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

Každá oblast Azure má určitý počet domén selhání. Seznam domén a počet domén selhání, které obsahují, najdete v dokumentaci [Skupiny dostupnosti](../../virtual-machines/windows/availability.md#availability-sets).

> [!IMPORTANT]  
> Kafka nemá o doménách selhání žádné informace. Když vytvoříte téma v Kafka, může uložit všechny repliky oddílů ve stejné doméně selhání. K vyřešení tohoto problému služba HDInsight poskytuje [nástroj pro obnovení rovnováhy oddílů Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Kdy obnovit rovnováhu replik oddílů

K zajištění nejvyšší dostupnost dat Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

* Při vytvoření nového tématu nebo oddílu

* Při vertikálním navýšení kapacity clusteru

## <a name="replication-factor"></a>Faktor replikace

> [!IMPORTANT]  
> Doporučujeme použít oblast Azure, která obsahuje tři domény selhání, a použít faktor replikace 3.

Pokud musíte použít oblast, která obsahuje jenom dvě domény selhání, použijte faktor replikace 4, abyste zajistili rovnoměrné rozložení replik napříč těmito dvěma doménami selhání.

Příklad vytváření témat a nastavení faktoru replikace najdete v dokumentu [Start with Apache Kafka na hdinsightu.](apache-kafka-get-started.md)

## <a name="how-to-rebalance-partition-replicas"></a>Jak obnovit rovnováhu replik oddílů

Pomocí [nástroje pro vyvážení oddílů Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) můžete znovu vyvážit vybraná témata. Tento nástroj se musí spustit z relace SSH na hlavní uzel clusteru Kafka.

Další informace o připojení ke službě HDInsight pomocí SSH najdete v dokumentu [Použití SSH s HDInsightem](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Další kroky

* [Škálovatelnost Apache Kafka na HDInsight](apache-kafka-scalability.md)
* [Zrcadlení s Apache Kafka na HDInsight](apache-kafka-mirroring.md)
