---
title: V Azure HDInsight plánování kapacity clusteru
description: Jak určit clusteru služby HDInsight pro kapacitu a výkon.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: hrasheed
ms.openlocfilehash: b35c5073f2b19523010528800c2a989d5de5b448
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848009"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Plánování kapacity pro clustery HDInsight

Před nasazením clusteru služby HDInsight, plánování kapacity požadované clusteru tak, že určíte potřebný výkon a škálování. Díky tomu plánování optimalizovat použitelnost a náklady. Některá rozhodnutí kapacita clusteru nelze změnit po nasazení. Pokud změníte parametry výkonu, můžete clusteru zrušena a znovu vytvořen bez ztráty dat uložených.

Jsou klíčové otázky týkající se plánování kapacity:

* V které geografické oblasti měli byste nasadit cluster?
* Jak velké úložiště potřebujete?
* Jaký typ clusteru byste měli nasadit?
* Jaké velikosti a typu virtuálního počítače (VM) používejte uzly clusteru?
* Počet pracovních uzlů by měl mít cluster?

## <a name="choose-an-azure-region"></a>Vyberte oblast Azure

Oblasti Azure Určuje, kde je váš cluster fyzicky zřízený. Abyste minimalizovali latenci čtení a zápisy, cluster by téměř vaše data.

HDInsight je k dispozici v mnoha oblastech Azure. Nejbližší oblast, najdete v tématu *HDInsight* položku *Analytics* v [dostupné produkty podle oblasti](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Vyberte umístění úložiště a jeho velikost

### <a name="location-of-default-storage"></a>Umístění výchozí úložiště

Výchozím úložištěm, účtu služby Azure Storage nebo Azure Data Lake Storage, musí být ve stejném umístění jako cluster. Azure Storage je dostupné ve všech umístěních. V některých oblastech je k dispozici data Lake Storage Gen1 – viz aktuální Data Lake Storage dostupnost v rámci *úložiště* v [Azure produkty k dispozici podle oblasti](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Umístění existujících dat

Pokud už máte účet úložiště nebo Data Lake Storage, který obsahuje vaše data a chcete používat toto úložiště jako výchozím úložištěm vašeho clusteru, musíte nasadit clusteru na stejném místě.

### <a name="storage-size"></a>Velikost úložiště

Po nasazení clusteru služby HDInsight můžete připojit další účty Azure Storage nebo přístup k jiné úložiště Data Lake. Všechny vaše účty úložiště se musí nacházet ve stejném umístění jako cluster. Data Lake Storage může být v jiném umístění, i když to může způsobit určitou latenci čtení a zápis dat.

Azure Storage má některé [limity kapacity](../azure-subscription-service-limits.md#storage-limits), zatímco Gen1 úložiště Data Lake je prakticky neomezené.

Cluster můžete přistupovat kombinaci různých účtů úložiště. Typické příklady zahrnují:

* Pokud je množství dat může potenciálně překročit kapacitu úložiště jednoho kontejneru objektů blob úložiště.
* Míru přístupu do kontejneru objektů blob při může překročit prahovou hodnotu, kde omezování dojde k.
* Pokud chcete, aby data, jste již nahráli do kontejneru objektů blob, který je k dispozici pro cluster.
* Když chcete izolovat různé části úložiště z důvodů zabezpečení nebo jak zjednodušit správu.

Pro cluster s uzly 48 doporučujeme 4 až 8 účty úložiště. I když už může být dostatečné celkový úložiště, každý účet úložiště poskytuje větší šířku pásma sítě pro výpočetní uzly. Pokud máte více účtů úložiště, použijte náhodný název pro každý účet úložiště, bez předpony. Účelem náhodných názvů snižuje pravděpodobnost vzniku kritických bodů úložiště (omezení využití sítě) nebo režimu běžné chyby ve všech účtech. Pro lepší výkon pomocí pouze jednoho kontejneru účtu úložiště.

## <a name="choose-a-cluster-type"></a>Vybrat typ clusteru

Určuje typ clusteru úloh clusteru HDInsight je nakonfigurován ke spuštění, například [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/), nebo [ Apache Spark](https://spark.apache.org/). Podrobný popis typy clusteru k dispozici, najdete v části [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Každý typ clusteru má topologii nasazení, která obsahuje požadavky na velikost a počet uzlů.

## <a name="choose-the-vm-size-and-type"></a>Zvolte velikost virtuálního počítače a typ

Každý typ clusteru má sadu typy uzlů a u každého typu uzlu má specifické nastavení pro jejich velikost virtuálního počítače a typ.

K určení velikosti clusteru optimální pro vaši aplikaci, můžete srovnávací testy kapacita clusteru a zvětšit velikost, jak je uvedeno. Například můžete použít simulované zatížení nebo *testovací dotaz*. S Simulovaná zatížením spustíte očekávané zatížení na jinou velikost clustery postupně zvyšuje velikost, dokud nebude dosaženo požadovaný výkon. Testovací dotaz může být vložen pravidelně mezi ostatní produkční dotazy, které ukazují, jestli cluster má dostatek prostředků.

Velikost virtuálního počítače a typ se určuje podle procesoru, výkon, velikost paměti RAM a latence sítě:

* Procesor: Velikost virtuálního počítače určuje počet jader. Více jader, vyšší stupeň paralelní zpracování každého uzlu dosáhnout. Některé typy virtuálních počítačů navíc mají rychlejší jader.

* Paměť RAM: Velikost virtuálního počítače také určuje množství paměti RAM ve virtuálním počítači k dispozici. Pro úlohy, které ukládat data v paměti pro zpracování, místo čtení z disku, zkontrolujte navyšte kapacitu pracovních uzlů k dispozici dostatek paměti k datům.

* Síť: Pro většinu typů clusteru je dat zpracovaných branami clusteru není na místním disku, ale v služby externí úložiště, jako je Data Lake Storage nebo Azure Storage. Vezměte v úvahu šířka pásma sítě a propustnosti mezi uzlu virtuálního počítače a služby úložiště. Šířku pásma sítě dostupnou pro virtuální počítač se obvykle zvyšuje s větší velikosti. Podrobnosti najdete v tématu [velikosti virtuálních počítačů přehled](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Zvolte možnost škálování clusteru

Škálování clusteru se určuje podle množství jeho uzly virtuálních počítačů. Pro všechny typy clusteru jsou typy uzlů, které mají určité měřítko a typy uzlů, které podporují horizontální navýšení kapacity. Cluster například může vyžadovat přesně tři [Apache ZooKeeper](https://zookeeper.apache.org/) uzly nebo dva hlavní uzly. Pracovní uzly, které provést zpracování dat, distribuované může využívat horizontální navýšení kapacity, tak, že přidáte pracovní uzly.

V závislosti na typu vašeho clusteru zvýšit počet pracovních uzlů přidá další výpočetní kapacity (například více jader), ale může také přidat do celkové množství paměti požadované pro celý cluster pro podporu v paměti úložiště dat, zpracování. Stejně jako volba velikosti virtuálního počítače a typ výběru správné clusteru škálování je obvykle spojit empirických, simulované zatížení nebo testovací dotazy.

Můžete horizontálně navýšit požadavkům zatížení ve špičce, pak škálovat zase snížit, když už nejsou potřeba tato další uzly clusteru. Další informace najdete v tématu [clusterů HDInsight škálování](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Životního cyklu clusteru

Bude vám účtována životnosti clusteru. Pokud jsou pouze konkrétní časy, že potřebujete clusteru nahoru a spuštěná, můžete si [vytváření clusterů na vyžádání pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Můžete také vytvořit Powershellové skripty, které zřízení a odstranění clusteru a poté naplánujte tyto skripty pomocí [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Při odstranění clusteru se odstraní také jeho výchozí metastore Hive. Pokud chcete zachovat metastoru pro další opakované vytváření clusteru, použijte externím úložištěm metadat jako je Azure Database nebo [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolovat chyby clusteru úloh

Někdy chyby lze kvůli paralelní provádění více mapy a snížit komponenty v clusteru s více uzly. Pomáhají izolovat problém, zkuste distribuované otestovat spuštěním souběžné víc úloh na jeden uzel clusteru, pak rozbalte tento přístup jak souběžně spustit více úloh v clusterech obsahující více než jeden uzel. Chcete-li vytvořit cluster HDInsight jeden uzel v Azure, použijte *pokročilé* možnost.

Můžete také nainstalovat prostředí s jedním uzlem vývoj v místním počítači a testovat řešení existuje. Hortonworks poskytuje jedním uzlem místní vývojové prostředí pro řešení založená na systému Hadoop, který je užitečný pro počáteční vývoj, testování konceptu, služby a testování. Další informace najdete v tématu [Hortonworks Sandbox](https://hortonworks.com/products/hortonworks-sandbox/).

K jeho identifikaci v místním clusteru jedním uzlem můžete znovu spustit úlohy a upravit vstupní data nebo použít menší datové sady. Jak spouštět úlohy závisí na platformu a typ aplikace.

## <a name="quotas"></a>Kvóty

Po určení cílovou velikost virtuálního počítače clusteru, škálování a typ, zkontrolujte aktuální kapacitní omezení kvóty předplatného. Při dosažení limitu kvóty, nebudete moci nasadit nových clusterů nebo horizontální navýšení kapacity stávajících clusterů přidáním dalších uzlů pracovního procesu. Pouze kvótu je kvótu jader procesoru, která existuje na úrovni oblasti pro každé předplatné. Například může mít vaše předplatné limit 30 jader v oblasti USA – východ. Pokud potřebujete požádat o zvýšení kvóty, proveďte následující kroky:

1. Přejít na web Azure Portal
1. Klikněte na **Nápověda a podpora** v levé dolní části stránky.
1. Klikněte na **nová žádost o podporu**.
1. Na **nová žádost o podporu** stránce v části **Základy** kartu, vyberte následující možnosti:
   - **Typ problému**: **Omezení služby a předplatného (kvóty)**
   - **Předplatné**: předplatné, které chcete upravit
   - **Typ kvóty**: **HDInsight**
    
     ![Vytvořit žádost o podporu o navýšení kvóty pro jádra HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Klikněte na **Další**.
1. Na **podrobnosti** stránky, zadejte popis problému, vyberte závažnost problému a vyberte upřednostňovaný způsob kontaktu.
1. Klikněte na tlačítko **Další: Zkontrolovat a vytvořit**.
1. Na **revize + vytvořit** klikněte na tlačítko **vytvořit**.

> [!NOTE]  
> Pokud je potřeba zvýšit kvótu jader HDInsight do privátní oblasti [odeslat žádost o seznam povolených](https://aka.ms/canaryintwhitelist).

Je možné [obraťte se na podporu požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Ale existují některá omezení pevné kvóty, například v rámci jednoho předplatného Azure může mít maximálně 10 000 jader. Podrobnosti o těchto omezeních najdete v tématu [předplatného Azure a limity, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Další postup

* [Nastavení clusterů v HDInsight se Apache Hadoop, Spark, Kafka a další](hdinsight-hadoop-provision-linux-clusters.md): Zjistěte, jak vytvořit a nakonfigurovat clusterů v HDInsight se Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, služby ML nebo Storm.
* [Monitorování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Další informace o klíčových scénářích monitorování pro cluster HDInsight, které můžou ovlivnit kapacitu vašeho clusteru.
