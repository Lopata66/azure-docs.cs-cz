---
title: Povolit výpisy haldy pro Apache Hadoop služby ve službě HDInsight – Azure
description: Povolte výpisy haldy pro Apache Hadoop služby z clusterů HDInsight se systémem Linux pro účely ladění a analýzy.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 90de0b4bfad4c5096ebc38eb3d31fc41bca6649b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494853"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Povolit výpisy haldy pro Apache Hadoop služby v HDInsight se systémem Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisy haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v době, kdy byl vytvořen výpis paměti. To je užitečné pro diagnostiku problémů, ke kterým dochází v době běhu.

## <a name="whichServices"></a>Orgány

Můžete povolit výpisy paměti haldy pro následující služby:

* **Apache hcatalog** – tempelton
* **Apache podregistr** – hiveserver2, metastore, derbyserver
* **MapReduce** – jobhistoryserver
* **Apache nitě** -ResourceManager, NodeManager, timelineserver
* **Apache HDFS** – datanode, secondarynamenode, namenode

Můžete také povolit výpisy paměti haldy pro mapu a snížit procesy spuštěné službou HDInsight.

## <a name="configuration"></a>Principy konfigurace výpisu paměti haldy

Výpisy haldy jsou povolené předáním možností (někdy označované jako výslovný nebo parametry) do JVM při spuštění služby. U většiny [Apache Hadoop](https://hadoop.apache.org/) Services můžete upravit skript prostředí používaný ke spuštění služby a předat tyto možnosti.

V každém skriptu je k dispozici export pro **\*\_výslovný**, který obsahuje možnosti předané JVM. Například v **Hadoop-env.sh** skriptu obsahuje řádek, který začíná na `export HADOOP_NAMENODE_OPTS=`, i možnosti služby NameNode.

Mapování a snížení procesů se mírně liší, protože tyto operace jsou podřízeným procesem služby MapReduce. Každá mapa nebo omezení procesu běží v podřízeném kontejneru a existují dvě položky, které obsahují JVM možnosti. Obojí je obsaženo v **souboru mapred-site. XML**:

* **MapReduce. admin. map. Child. Java. výslovný**
* **MapReduce. admin. redukovat. Child. Java. výslovný**

> [!NOTE]  
> K úpravě nastavení skriptů a mapred-site. XML doporučujeme použít [Apache Ambari](https://ambari.apache.org/) , protože Ambari zpracovává replikaci změn napříč uzly v clusteru. Konkrétní postup najdete v části [použití Apache Ambari](#using-apache-ambari) .

### <a name="enable-heap-dumps"></a>Povolení výpisů paměti haldy

Následující možnost povolí výpisy paměti haldy, když dojde k OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

**+** označuje, že je tato možnost povolená. Ve výchozím nastavení je tato možnost zakázána.

> [!WARNING]  
> Ve výchozím nastavení nejsou ve výchozím nastavení pro služby Hadoop ve službě HDInsight povoleny výpisy haldy, protože soubory s výpisem paměti můžou být velké. Pokud je povolíte pro řešení potíží, zakažte je po reprodukování problému a shromáždění souborů výpisu paměti.

### <a name="dump-location"></a>Umístění výpisu paměti

Výchozím umístěním pro soubor s výpisem paměti je aktuální pracovní adresář. Můžete určit, kde je soubor uložený, pomocí následující možnosti:

    -XX:HeapDumpPath=/path

Například použití `-XX:HeapDumpPath=/tmp` způsobí, že se výpisy paměti ukládají do adresáře adresáře/TMP.

### <a name="scripts"></a>Skripty

Skript můžete také aktivovat, když dojde k **OutOfMemoryError** . Například spuštění oznámení, abyste věděli, že došlo k chybě. Pro aktivaci skriptu na __OutOfMemoryError__použijte následující možnost:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Vzhledem k tomu, že Apache Hadoop je distribuovaný systém, musí být všechny použité skripty umístěné na všech uzlech v clusteru, na kterém je služba spuštěná.
> 
> Skript musí být také v umístění, které je přístupné pro účet, ve kterém služba běží, a musí poskytovat oprávnění k provedení. Můžete například chtít, aby se skripty ukládaly do `/usr/local/bin` a pomocí `chmod go+rx /usr/local/bin/filename.sh` udělovat oprávnění ke čtení a spouštění.

## <a name="using-apache-ambari"></a>Použití Apache Ambari

Chcete-li upravit konfiguraci služby, použijte následující postup:

1. Otevřete webové uživatelské rozhraní Ambari pro váš cluster. Adresa URL je https://YOURCLUSTERNAME.azurehdinsight.net.

    Po zobrazení výzvy proveďte ověření v lokalitě pomocí názvu účtu HTTP (výchozí: admin) a hesla pro váš cluster.

   > [!NOTE]  
   > K uživatelskému jménu a heslu se může Ambari považovat za druhý čas. Pokud ano, zadejte stejný název účtu a heslo.

2. Pomocí seznamu na levé straně vyberte oblast služby, kterou chcete upravit. Příklad: **HDFS**. V prostřední oblasti vyberte kartu **Konfigurace** .

    ![Obrázek webu Ambari s vybranou kartou HDFS Configurations](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Pomocí položky **Filter...** entry zadejte **výslovný**. Zobrazí se pouze položky, které obsahují tento text.

    ![Seznam filtrovaných konfigurací Apache Ambari](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Vyhledejte položku **\*\_výslovný** pro službu, pro kterou chcete povolit výpisy paměti haldy, a přidejte možnosti, které chcete povolit. Na následujícím obrázku jsem přidal `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` do položky **HADOOP\_NAMENODE\_výslovný** :

    ![Apache Ambari Hadoop-namenode-výslovný](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Při povolování výpisů paměti haldy pro mapu nebo omezení podřízeného procesu vyhledejte pole s názvem **MapReduce. admin. map. Child. Java. výslovný** a **MapReduce. admin. redukovat. Child. Java. výslovný**.

    Pomocí tlačítka **Uložit** uložte změny. Můžete zadat krátkou poznámku popisující změny.

5. Po použití změn se vedle jedné nebo více služeb zobrazí ikona **požadovaná k restartování** .

    ![ikona požadovaného restartování a tlačítko pro restartování](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Vyberte každou službu, která vyžaduje restart, a pomocí tlačítka **Akce služby** **zapněte režim údržby**. Režim údržby zabraňuje generování výstrah ze služby, když ji restartujete.

    ![Zapnout nabídku režimu údržby HDI](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Po povolení režimu údržby použijte k **restartování** služby tlačítko **restartovat** .

    ![Apache Ambari restartovat všechny ovlivněné položky](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Položky tlačítka pro **restartování** se mohou lišit pro jiné služby.

8. Po restartování služeb můžete **režim údržby**vypnout pomocí tlačítka **Akce služby** . Tento Ambari obnoví monitorování výstrah pro službu.

