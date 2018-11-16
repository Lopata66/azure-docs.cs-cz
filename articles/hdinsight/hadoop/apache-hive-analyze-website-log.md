---
title: Použití Hivu se službou Apache Hadoop pro analýzu protokolů webu – Azure HDInsight
description: Zjistěte, jak pomocí Apache Hive s HDInsight analýza webových protokolů. Budete pomocí souboru protokolu jako vstup do tabulky HDInsight a použít HiveQL pro dotaz na data.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 4f4067c73cac4597da3099212c9c04c2544a0b2d
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634341"
---
# <a name="use-apache-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Použití Apache Hivu se službou HDInsight se systémem Windows k analýze protokolů z webů
Zjistěte, jak použít HiveQL s HDInsight k analýze protokolů na webu. Analýza protokolu webu je možné segmentovat cílovou skupinu podle podobných aktivit, kategorizace návštěvníkům webu podle demografické údaje a přečtěte si obsah, zobrazení, webů, které pocházejí z a tak dále.

> [!IMPORTANT]
> Kroky v tomto dokumentu fungovat jenom s clustery HDInsight se systémem Windows. HDInsight je dostupná jenom ve Windows pro verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

V této ukázce použití clusteru služby HDInsight k analýze soubory protokolů webů k získání přehledu o četnosti návštěv na web z externích webů za den. Také vygenerování souhrnu chyb na webových stránkách, které se uživatelé setkávají. Získáte informace o těchto tématech:

* Připojte se k Azure Blob storage, který obsahuje soubory protokolů webů.
* Vytvoření tabulky HIVE k dotazování těchto protokolů.
* Vytváření dotazů HIVU analyzovat data.
* Použijte Microsoft Excel k připojení k HDInsight (s použitím otevřít databázové připojení (ODBC) pro načtení analyzovaná data.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Požadavky
* Musí být zřízený cluster Hadoop v Azure HDInsight. Pokyny najdete v tématu [zřizování clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Musíte mít aplikaci Microsoft Excel 2013 nebo nainstalována aplikace Excel 2010.
* Musíte mít [ovladače Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886) k importu dat z Hive do aplikace Excel.

## <a name="to-run-the-sample"></a>Ke spuštění ukázky
1. Z [webu Azure portal](https://portal.azure.com/), z úvodního (Pokud je připnutý clusteru existuje), klikněte na dlaždici clusteru, na kterém chcete spustit vzorku.
2. Z okna clusteru v rámci **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**a potom z **řídicí panel clusteru** okna, klikněte na tlačítko **clusteru HDInsight Řídicí panel**. Alternativně můžete přímo otevřete řídicí panel pomocí následující adresy URL:

         https://<clustername>.azurehdinsight.net

    Po zobrazení výzvy ověřování pomocí uživatelského jména správce a heslo, které jste použili při zřizování clusteru.
3. Z webové stránky, které se otevře, klikněte na tlačítko **Galerie spuštěno získání** kartu a potom v části **řešení s ukázkovými daty** kategorii, klikněte na tlačítko **analýza protokolu webu** vzorku.
4. Postupujte podle pokynů k dispozici na webové stránce a dokončete ukázku.

## <a name="next-steps"></a>Další postup
Vyzkoušejte následující ukázka: [analýza dat ze senzorů pomocí Hivu se službou HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
