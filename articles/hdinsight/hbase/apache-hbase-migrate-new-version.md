---
title: Migrace clusteru HBase na novou verzi – Azure HDInsight
description: Jak migrovat clustery HBase na novou verzi.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: ac7984c50e6adec888c112cc260cf2e6af02fc97
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114457"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrace clusteru Apache HBase na novou verzi.

Clustery založené na úlohách, například [Apache Spark](https://spark.apache.org/) a [Apache Hadoop](https://hadoop.apache.org/), jsou jednoduché k upgradu – viz [clusteru HDInsight Upgrade na novější verzi](../hdinsight-upgrade-cluster.md):

1. Zálohování dat přechodné (místně uložené).
2. Odstranění existujícího clusteru.
3. Vytvoření nového clusteru ve stejné podsíti virtuální sítě.
4. Umožňuje importovat přechodné data.
5. Spuštění úlohy a pokračovat ve zpracování na novém clusteru.

Upgrade [Apache HBase](https://hbase.apache.org/) clusteru jsou potřeba další kroky, jak je popsáno v tomto článku.

> [!NOTE]  
> Prostoje při upgradu by měl být minimální v řádu minut. Tento výpadek je způsobeno kroky k vyprázdnění všech dat v paměti, pak čas ke konfiguraci a znovu spusťte služby v novém clusteru. Vaše výsledky se budou lišit v závislosti na počtu uzlů, velikost dat a jiné proměnné.

## <a name="review-apache-hbase-compatibility"></a>Kontrola kompatibility Apache HBase

Před upgradem Apache HBase, ujistěte se, že jsou kompatibilní verze HBase na zdrojových a cílových clusterech. Další informace najdete v tématu [komponenty a verze k dispozici s HDInsight Apache Hadoopu](../hdinsight-component-versioning.md).

> [!NOTE]  
> Důrazně doporučujeme, abyste si přečetli matici kompatibility verzí v [HBase knihy](https://hbase.apache.org/book.html#upgrading).

Tady je příklad verze kompatibility matici, kde Y označuje kompatibilitu a N označuje potenciální nekompatibility:

| Typ kompatibility | Hlavní verze| Podverze | Oprava |
| --- | --- | --- | --- |
| Kompatibilita přenosový Klient-Server | Ne | Ano | Ano |
| Servery kompatibility | Ne | Ano | Ano |
| Kompatibilita formátů souborů | Ne | Ano | Ano |
| Kompatibilita s klientským rozhraní API | Ne | Ano | Ano |
| Binární kompatibilita s klientským | Ne | Ne | Ano |
| **Serverové omezené kompatibilitu s rozhraními API** |  |  |  |
| Stable | Ne | Ano | Ano |
| Vyvíjejí | Ne | Ne | Ano |
| Nestabilní | Ne | Ne | Ne |
| Kompatibilita závislostí | Ne | Ano | Ano |
| Provozní kompatibility | Ne | Ne | Ano |

> [!NOTE]  
> Jakékoli zásadní nekompatibility by měl popsané v zpráva k vydání verze HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade se stejnou hlavní verzi Apache HBase

Následující příklad je pro upgrade na 3.6 (jsou součástí Apache HBase 1.1.2) se stejnou hlavní verzí HBase HDInsight 3.4. Jiné verze upgradu jsou podobné, dokud nejsou žádné problémy s kompatibilitou mezi zdrojovým a cílovým verze.

1. Ujistěte se, že je vaše aplikace kompatibilní s novou verzí, jak je znázorněno v HBase kompatibility matice a release notes. Otestujte aplikaci v clusteru se systémem cílovou verzi sady HDInsight a HBase.

2. [Nastavit nový cluster HDInsight cílové](../hdinsight-hadoop-provision-linux-clusters.md) pomocí stejného účtu úložiště, ale s jiným kontejnerem název:

    ![Použijte stejný účet úložiště, ale vytvoření různých kontejneru](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Vyprázdnění váš zdrojový cluster HBase. To je cluster, ze kterého provádíte upgrade. HBase zapisuje příchozí data do úložiště v paměti volá _paměťového úložiště_. Po paměťového úložiště dosáhne určité velikosti, paměťového úložiště vyprazdňuje na disk pro dlouhodobé uložení v účtu úložiště clusteru. Při odstraňování původní cluster, memstores jsou recyklovány, přijít o data. Chcete-li ručně vyprázdnit paměťového úložiště pro každou tabulku na disk, spusťte následující skript. Nejnovější verze tohoto skriptu je na Azure [Githubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Zastavte příjem pro původní cluster HBase.
5. Chcete-li zajistit všechna poslední data v metody vyprazdňuje, spusťte znovu skript pro předchozí.
6. Přihlaste se k [Apache Ambari](https://ambari.apache.org/) v původním clusteru (https://OLDCLUSTERNAME.azurehdidnsight.net) a zastavit služby HBase. Po zobrazení výzvy potvrďte, že chcete zastavit služby, zaškrtněte políčko Zapnout režim údržby pro HBase. Další informace o připojení k a pomocí nástroje Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![V Ambari klikněte na kartu služby, potom HBase v nabídce vlevo, pak v části Akce službu zastavit](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Zkontrolujte zapnout v režimu údržby pro HBase zaškrtávací políčko a potom potvrdit](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Přihlaste se k Ambari na novém clusteru HDInsight. Změnit `fs.defaultFS` HDFS nastavení tak, aby odkazoval na název kontejneru původní cluster používat. Toto nastavení je v části **HDFS > Konfigurace > Upřesnit > Upřesnit základního webu**.

    ![V Ambari klikněte na kartu služby, potom HDFS v nabídce vlevo, klikněte na kartu Konfigurace klikněte na kartu Upřesnit pod](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![V Ambari změňte název kontejneru](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Pokud nepoužíváte clustery HBase pomocí funkce rozšířeného zapisuje, tento krok přeskočte. Je potřeba pouze pro clustery HBase pomocí funkce rozšířeného zapisuje.**
   
   Změňte cestu hbase.rootdir tak, aby odkazoval na kontejner k původnímu clusteru.

    ![V Ambari změňte název kontejneru pro hbase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
9. Uložte provedené změny.
10. Je určeno Ambari, restartujte všechny požadované služby.
11. Bod aplikace do nového clusteru.

    > [!NOTE]  
    > Statické DNS pro vaši aplikaci se změní při upgradu. Místo pevného kódování této služby DNS, můžete nakonfigurovat záznam CNAME v nastavení DNS pro název domény, které odkazuje na název clusteru. Další možností je použít konfigurační soubor pro vaši aplikaci, kterou můžete aktualizovat bez opětovného nasazení.

12. Začněte tak příjem dat, pokud chcete zobrazit, pokud všechno funguje podle očekávání.
13. Pokud nový cluster nachází uspokojivé kvality, odstraňte původní cluster.

## <a name="next-steps"></a>Další postup

Další informace o [Apache HBase](https://hbase.apache.org/) a inovace clusterů HDInsight, naleznete v následujících článcích:

* [Upgrade clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Monitorování a správa Azure HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop komponenty a verze](../hdinsight-component-versioning.md)
* [Optimalizace konfigurace pomocí nástroje Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
