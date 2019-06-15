---
title: Co jsou zásobníku technologie Apache Hadoop a Apache Spark? – Azure HDInsight
description: Úvod do HDInsight a Apache Hadoop a Apache Sparku a technologiích a komponent, včetně Kafka, Hive, Storm a HBase pro analýzy velkých objemů dat.
keywords: azure hadoop, hadoop azure, hadoop úvod, úvod hadoop, technologie hadoop, úvod do technologie hadoop, úvod k technologii hadoop, co je cluster hadoop, co je hadoop cluster, k čemu slouží hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 06/11/2019
ms.openlocfilehash: 42bfa571818efa4d4a5ad96f62a47fb2b80dca3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057561"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co je Apache Hadoop v Azure HDInsight?

Tento článek obsahuje úvod k Apache Hadoop ve službě Azure HDInsight. Azure HDInsight je plně spravovaná opensourcová, opensourcová analytická služba v cloudu pro podniky. Můžete použít open source architektury, jako jsou Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R a další. 


## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Co je HDInsight a technologie Hadoop?

[Apache Hadoop](https://hadoop.apache.org/) byl původní open source architekturou pro distribuované zpracování a analýzy velkých datových sad v clusterech. Součástí technologie hadoop souvisejícího softwaru a nástrojů, včetně Apache Hive, Apache HBase, Spark, Kafka a řada dalších.


Azure HDInsight je Cloudová distribuce komponent Hadoop. Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. Můžete využít nejoblíbenější opensourcové architektury, jako jsou Hadoop, Spark, Hive, LLAP, Kafka, Storm, R a další. Tyto architektury podporují širokou škálu scénářů, jako jsou ETL (extrakce, transformace a načítání), datové sklady, strojové učení a IoT.

K dispozici komponenty technologie Hadoop v HDInsight najdete v tématu [komponenty a verze, které jsou k dispozici s HDInsight](../hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Co jsou velké objemy dat?

Velké objemy dat se shromažďují v narůstajícím množství, s vyšší rychlostí a stále větší pestrostí formátů. Může se to týkat historických dat (tj. uložených) nebo dat v reálném čase (tj. streamovaných ze zdroje). Informace o nejběžnějších případech použití velkých objemů dat najdete v části [Scénáře použití služby HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-hadoop-on-hdinsight"></a>Proč bych měl/a používat Hadoop ve službě HDInsight?

Tato část uvádí schopnosti služby Azure HDInsight.


|Schopnost  |Popis  |
|---------|---------|
|Nativní pro cloud     |     Azure HDInsight umožňuje vytvářet optimalizované clustery pro [Hadoop](apache-hadoop-linux-tutorial-get-started.md),  [Spark](../spark/apache-spark-jupyter-spark-sql.md),  [Interaktivní dotazy (LLAP)](../interactive-query/apache-interactive-query-get-started.md),  [Kafka](../kafka/apache-kafka-get-started.md),  [Storm](../storm/apache-storm-tutorial-get-started-linux.md),  [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) a  [ML Services](../r-server/r-server-get-started.md) v Azure. HDInsight poskytuje také komplexní smlouvu SLA pro všechny vaše produkční úlohy.  |
|Škálovatelnost a nízké náklady     | HDInsight umožňuje [vertikálně navyšovat nebo snižovat kapacitu](../hdinsight-administer-use-portal-linux.md#scale-clusters)  úloh.  [Vytváření clusterů na vyžádání](../hdinsight-hadoop-create-linux-clusters-adf.md)  vám umožní snížit náklady a platit jenom za to, co používáte. Můžete také sestavovat datové kanály pro zprovoznění úloh. Oddělený výpočetní výkon a úložiště poskytují lepší výkon a flexibilitu. |
|Bezpečnost a dodržování předpisů    | HDInsight umožňuje chránit datové prostředky vašeho podniku pomocí služby [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [šifrování](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) a integrace se službou [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).        |
|Monitorování    | Azure HDInsight se integruje s [protokoly Azure monitoru](../hdinsight-hadoop-oms-log-analytics-tutorial.md) a poskytuje jednotné rozhraní, pomocí kterého můžete monitorování všech vašich clusterů.        |
|Globální dostupnost | Služba HDInsight je dostupná ve více  [oblastech](https://azure.microsoft.com/regions/services/)  než jakákoli jiná nabídka analýz velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech. |  
|Produktivita     |  Azure HDInsight umožňuje používat bohaté nástroje zvyšující produktivitu pro Hadoop a Spark s oblíbeným vývojovým prostředím. Mezi tato vývojová prostředí patří [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [VSCode](../hdinsight-for-vscode.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) a [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) pro zajištění podpory jazyků Scala, Python, R, Java a .NET. Odborníci přes data můžou také spolupracovat s využitím oblíbených poznámkových bloků, jako jsou [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) a [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Rozšiřitelnost     |  Clustery HDInsight můžete rozšířit instalací komponent (Hue, Presto atd.) pomocí [akcí skriptů](../hdinsight-hadoop-customize-cluster-linux.md), [přidáním hraničních uzlů](../hdinsight-apps-use-edge-node.md) nebo [integrací s dalšími aplikacemi certifikovanými pro práci s velkými objemy dat](../hdinsight-apps-install-applications.md). HDInsight umožňuje bezproblémovou integraci s nejoblíbenějšími řešeními pro velké objemy dat prostřednictvím nasazení [jedním kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scénáře použití služby HDInsight

Azure HDInsight je možné použít v nejrůznějších scénářích velkých objemů dat. Může se jednat o historická data (data, která jsou už shromážděná a uložená) nebo o data v reálném čase (data proudící přímo ze zdroje). Scénáře zpracování těchto dat můžeme shrnout do následujících kategorií: 

### <a name="batch-processing-etl"></a>Dávkové zpracování (ETL)

Extrakce, transformace a načítání (ETL) je proces, při kterém se nestrukturovaná nebo strukturovaná data extrahují z heterogenních zdrojů dat. Potom se transformují do strukturovaného formátu a načítají do úložiště dat. Transformovaná data je možné použít pro datové vědy nebo datové sklady.

### <a name="data-warehousing"></a>Datové sklady

Pomocí služby HDInsight můžete provádět interaktivní dotazy v petabajtovém měřítku nad strukturovanými i nestrukturovanými daty v jakémkoli měřítku. Můžete také sestavovat modely, které je propojí s nástroji BI. Další informace najdete v [tomto příběhu zákazníka](https://customers.microsoft.com/story/milliman). 

![Architektura HDInsight: Datové sklady](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "HDInsight pro datové sklady architektury")

### <a name="internet-of-things-iot"></a>Internet věcí (IoT)

Pomocí služby HDInsight můžete zpracovávat streamovaná data přijímaná v reálném čase z různých zařízení. Pokud chcete získat další informace, [přečtěte si tento blogový příspěvek z Azure, který oznamuje verzi Public Preview pro Apache Kafka v HDInsightu se Spravovanými disky Azure](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Architektura HDInsight: Internet of Things](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png "architektury HDInsight IoT") 

### <a name="data-science"></a>Datové vědy

Pomocí služby HDInsight můžete sestavovat aplikace, které z dat extrahují důležité informace. Navíc můžete pomocí služby Azure Machine Learning předvídat budoucí trendy pro vaši firmu. Další informace najdete v [tomto příběhu zákazníka](https://customers.microsoft.com/story/pros).

![Architektura HDInsight: Pro datové vědy](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight data science architektury")

### <a name="hybrid"></a>Hybridní

Pomocí služby HDInsight můžete rozšířit svou stávající místní infrastrukturu velkých dat do Azure a využívat možnosti pokročilých analýz v cloudu.

![Architektura HDInsight: Hybridní](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "HDInsight hybridní architektury")

## <a name="cluster-types-in-hdinsight"></a>Typy clusterů ve službě HDInsight

HDInsight zahrnuje specifické typy clusterů a možnosti přizpůsobení clusterů, jako je například možnost přidávání komponent, nástrojů a jazyků. HDInsight nabízí následující typy clusteru:

|Typ clusteru | Popis |
|---|---|
|[Apache Hadoop](https://wiki.apache.org/hadoop)|Rozhraní, který používá HDFS, YARN Správa prostředků a jednoduché programování MapReduce model pro zpracování a analýzu dat dávek paralelně.|
|[Apache Spark](https://spark.apache.org/)|Open source, paralelní zpracování rozšiřovatelnou platformu, která podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Přečtěte si téma [Co je Apache Spark v prostředí HDInsight?](../spark/apache-spark-overview.md)|
|[Apache HBase](https://hbase.apache.org/)|Databáze NoSQL postavená na Hadoop poskytující náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a polostrukturovaných dat – případně až miliardy řádků krát miliony sloupců. Přečtěte si téma [Co je HBase v HDInsight?](../hbase/apache-hbase-overview.md)|
|[ML Services](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server)|Server pro hostování a správu paralelních, distribuovaných procesů r. Poskytuje datovým vědcům, statistikům a programátorům v R přístup ke škálovatelným, distribuovaným analytickým metodám služby HDInsight na vyžádání. Viz [Přehled ML Services ve službě HDInsight](../r-server/r-server-overview.md).|
|[Apache Storm](https://storm.incubator.apache.org/)|V reálném čase distribuovaný výpočetní systém pro rychlé zpracování velkých datových proudů. Storm je poskytován jako spravovaný cluster v prostředí HDInsight. Viz [Analýza dat snímačů v reálném čase pomocí nástrojů Storm a Hadoop](../storm/apache-storm-sensor-data-analysis.md).|
|[Apache Interactive Query](https://cwiki.apache.org/confluence/display/Hive/LLAP)|Ukládání v mezipaměti pro interaktivnější a rychlejší dotazy Hive. Viz [Použití Interactive Query ve službě HDInsight](../interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](https://kafka.apache.org/)|Open source platforma, která slouží k vytváření aplikací a datových proudů datové kanály. Kafka také poskytuje funkce propojující fronty zpráv, pomocí kterých můžete publikovat datové streamy a přihlašovat se k jejich odběru. Viz [Úvod k Apache Kafka ve službě HDInsight](../kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Opensourcové komponenty ve službě HDInsight

Azure HDInsight umožňuje vytvářet clustery pomocí open source architektury, jako jsou Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase a R. Tyto clustery ve výchozím nastavení, součástí další opensourcové komponenty, které jsou zahrnuty v clusteru, jako například [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [ HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://wiki.apache.org/hadoop/MapReduce), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programovací jazyky v prostředí HDInsight

Clustery HDInsight, včetně clusterů Spark, HBase, Kafka, Hadoop a dalších, podporují celou řadu programovacích jazyků. Některé z nich ale nejsou ve výchozím nastavení nainstalované. Pro knihovny, moduly nebo balíčky, které nejsou ve výchozím nastavení nainstalované, [použijte akci skriptu pro instalaci součásti](../hdinsight-hadoop-script-actions-linux.md).

|Programovací jazyk  |Informace  |
|---------|---------|
|Výchozí podpora programovacích jazyků     | Ve výchozím nastavení podporují clustery prostředí HDInsight tyto jazyky:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Přejít</li></ul>  |
|Jazyky Java virtual machine (JVM)     | V prostředí Java Virtual Machine (JVM) je možné spouštět celou řadu jiných jazyků, než je Java. Pokud ale některé z těchto jazyků spustíte, může být v clusteru potřeba nainstalovat další součásti. Clustery prostředí HDInsight podporují následující jazyky založené na JVM: <ul><li>Clojure</li><li>Jython (Python pro jazyk Java)</li><li>Scala</li></ul>     |
|Jazyky pro Hadoop     | Clustery HDInsight podporují následující jazyky, které jsou určené konkrétně pro technologii Hadoop: <ul><li>Pig Latin pro úlohy Pig</li><li>HiveQL pro úlohy Hive a SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Vývojářské nástroje pro HDInsight

Vývojářské nástroje pro HDInsight, včetně nástrojů IntelliJ, Eclipse, Visual Studio Code a Visual Studio, můžete díky bezproblémové integraci s Azure použít k vytváření a odesílání úloh a dotazů na data HDInsight.

* [Sada Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin)

* [Sada Azure Toolkit pro Eclipse](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-eclipse-tool-plugin)

* [Azure HDInsight Tools pro VS Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode?branch=pr-en-us-22999)

* [Nástroje Azure Data Lake pro Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence ve službě HDInsight

Známé nástroje business intelligence (BI) načítají, analyzují a vykazují data integrovaná v prostředí HDInsight buď pomocí doplňku Power Query, nebo ovladače Microsoft Hive ODBC Driver:

* [Apache Spark BI s využitím nástrojů pro vizualizaci dat ve službě Azure HDInsight](../spark/apache-spark-use-bi-tools.md)

* [Vizualizace dat pomocí Microsoft Power BI v Azure HDInsight s Apache Hive](apache-hadoop-connect-hive-power-bi.md)

* [Vizualizace dat Interactive Query Hive pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](apache-hadoop-connect-excel-power-query.md) (vyžaduje Windows)

* [Připojení Excelu k systému Apache Hadoop pomocí ovladače ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md) (vyžaduje Windows)

* [Použití služby SQL Server Analysis Services se službou HDInsight](https://docs.microsoft.com/previous-versions/msp-n-p/dn749857(v=pandp.10))

* [Použití služby SQL Server Reporting Services se službou HDInsight](https://docs.microsoft.com/previous-versions/msp-n-p/dn749856(v=pandp.10))

## <a name="next-steps"></a>Další postup

* [Vytvořit cluster Apache Hadoop v HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
