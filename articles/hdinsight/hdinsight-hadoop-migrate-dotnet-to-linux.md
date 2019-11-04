---
title: Použití .NET se systémem Hadoop MapReduce v HDInsight na platformě Linux – Azure
description: Naučte se používat aplikace .NET pro streamování MapReduce na HDInsight se systémem Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498215"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrace řešení .NET pro HDInsight se systémem Windows na HDInsight se systémem Linux

Clustery HDInsight se systémem Linux používají [Mono (https://mono-project.com)](https://mono-project.com) ke spouštění aplikací .NET. Mono umožňuje používat komponenty .NET, jako jsou aplikace MapReduce, se systémem Linux HDInsight. V tomto dokumentu se dozvíte, jak migrovat řešení .NET vytvořená pro clustery HDInsight se systémem Windows pro práci s mono v HDInsight se systémem Linux.

## <a name="mono-compatibility-with-net"></a>Kompatibilita mono s .NET

Mono verze 4.2.1 je součástí HDInsight verze 3,6. Další informace o verzi mono, která je součástí služby HDInsight, najdete v tématu [verze komponent HDInsight](hdinsight-component-versioning.md).

Další informace o kompatibilitě mezi mono a .NET naleznete v tématu [Kompatibilita Mono (https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentu.

> [!IMPORTANT]  
> Rozhraní SCP.NET je kompatibilní s mono. Další informace o použití SCP.NET s mono najdete v tématu [použití sady Visual Studio k C# vývoji topologií pro Apache Storm v HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizovaná analýza přenositelnosti

[Analyzátor přenositelnosti .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) se dá použít k vygenerování sestavy nekompatibilit mezi vaší aplikací a mono. Pomocí následujících kroků nakonfigurujte analyzátor tak, aby zkontroloval vaši aplikaci pro přenositelnost mono:

1. Nainstalujte [analyzátor přenositelnosti .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Během instalace vyberte verzi sady Visual Studio, která se má použít.

2. V aplikaci Visual Studio 2015 vyberte možnost __analyzovat__ > __Nastavení analyzátoru přenositelnosti__a ujistěte se, že je v části __mono__ zaškrtnuto políčko __4,5__ .

    ![4,5 pro nastavení analyzátoru se vrátil oddíl mono.](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Kliknutím na __OK__ uložte konfiguraci.

3. Vyberte __analyzovat__ > __analyzovat přenositelnost sestavení__. Vyberte sestavení, které obsahuje vaše řešení, a pak vyberte __otevřít__ pro zahájení analýzy.

4. Po dokončení analýzy vyberte __analyzovat__ > __Zobrazit sestavy analýzy__. Ve __výsledcích analýzy přenositelnosti__otevřete sestavu výběrem __otevřít sestavu__ .

    ![Dialogové okno výsledků analyzátoru přenositelnosti](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analyzátor nemůže zachytit všechny problémy s vaším řešením. Například cesta k souboru `c:\temp\file.txt` se považuje za OK, pokud je mono spuštěný ve Windows. Stejná cesta není platná na platformě Linux.

## <a name="manual-portability-analysis"></a>Analýza ručních přenositelností

Proveďte ruční audit kódu pomocí informací v [přenositelnosti aplikace (https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentu.

## <a name="modify-and-build"></a>Upravit a sestavit

Můžete pokračovat v používání sady Visual Studio k sestavení řešení .NET pro HDInsight. Musíte ale zajistit, aby byl projekt nakonfigurovaný tak, aby používal .NET Framework 4,5.

## <a name="deploy-and-test"></a>Nasazení a testování

Po úpravě řešení pomocí doporučení z analyzátoru přenositelnosti .NET nebo z ruční analýzy je potřeba ho otestovat v HDInsight. Testování řešení v clusteru HDInsight se systémem Linux může odhalit drobné problémy, které je třeba opravit. Při testování doporučujeme povolit další protokolování ve vaší aplikaci.

Další informace o přístupu k protokolům najdete v následujících dokumentech:

* [Přístup k protokolům aplikace Apache Hadoop nitě v HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Další kroky

* [Použití C# s MapReduce ve službě HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Použití C# uživatelsky definovaných funkcí s Apache Hive a Apache prasete](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Vývoj C# topologií pro Apache Storm v HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
