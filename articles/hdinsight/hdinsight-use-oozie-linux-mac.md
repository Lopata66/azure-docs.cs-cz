---
title: Využijte pracovní postupy Hadoop Oozie v Azure HDInsight založených na Linuxu
description: Použití Hadoop Oozie v HDInsight se systémem Linux. Zjistěte, jak definovat pracovní postup Oozie a odešlete úlohu Oozie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 55db43bf3037fcba59e7ad783c6d8c06f1886bdb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142834"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Použití Apache Oozie s Hadoopem Apache k definování a spuštění workflowu v Azure HDInsight založených na Linuxu

Další informace o použití Apache Oozie s Apache Hadoop v Azure HDInsight. Oozie je pracovní postup a koordinaci systém, který spravuje úlohy platformy Hadoop. Oozie integrován do zásobníku Hadoop a podporuje následující úlohy:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie můžete také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

> [!NOTE]  
> Další možností pro definování pracovních postupů pomocí HDInsight je pomocí služby Azure Data Factory. Další informace o službě Data Factory najdete v tématu [použití Apache Pig a Apache Hivu se službou Data Factory][azure-data-factory-pig-hive]. Použití Oozie v clusterech s balíčkem Enterprise Security Package najdete v tématu [spustit Apache Oozie v HDInsight Hadoop clusterů s balíčkem Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop v HDInsight**. Zobrazit [Začínáme s HDInsight v Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Klient SSH**. Zobrazit [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Azure SQL Database**.  Zobrazit [vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started.md).  Tento článek používá databázi s názvem `oozietest`.

* [Schéma identifikátoru URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) jako primární úložiště vašich clusterů. To může být `wasb://` pro službu Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by `wasbs://` nebo `abfss://`, respektive najdete [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).


## <a name="example-workflow"></a>Ukázkový pracovní postup

Pracovní postup v tomto dokumentu obsahuje dvě akce. Definice pro úkoly, jako je například Hive, Sqoop, MapReduce nebo jiné procesy spuštění jsou akce:

![Diagram pracovního postupu][img-workflow-diagram]

1. Akce Hive spouští skript HiveQL k extrahování záznamů z `hivesampletable` , který je součástí HDInsight. Každý řádek dat popisuje návštěvu z určité mobilní zařízení. Formát záznamu se zobrazí jako následující text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skript Hive v tomto dokumentu se počítá celkový počet návštěv pro každou platformu, třeba na Android nebo iPhone a ukládá počty do nové tabulky Hive.

    Další informace o Hivu najdete v tématu [použití Apache Hivu se službou HDInsight][hdinsight-use-hive].

2. Sqoop akce exportuje obsah do nové tabulky Hive do tabulky vytvořené ve službě Azure SQL Database. Další informace o Sqoop najdete v tématu [použití Apache Sqoop s HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Podporované verze Oozie na clusterech HDInsight najdete v tématu [co je nového ve verzích clusterů Hadoop poskytovaných službou HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Vytvořte pracovní adresář

Oozie se očekává, že jste k uložení všech prostředků potřebných pro úlohu ve stejném adresáři. Tento příklad používá `wasbs:///tutorials/useoozie`. K vytvoření tohoto adresáře, proveďte následující kroky:

1. Upravit kód uvedený níže má nahradit `sshuser` s SSH uživatele název clusteru a nahraďte `clustername` s názvem clusteru.  Zadejte kód pro připojení ke clusteru HDInsight pomocí [pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Vytvořit adresář, použijte následující příkaz:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` Parametr způsobí vytvoření všechny adresáře v cestě. `data` Directory se používá k ukládání dat používaných `useooziewf.hql` skriptu.

3. Upravit kód uvedený níže má nahradit `username` se svým uživatelským jménem SSH.  Pokud chcete mít jistotu, že Oozie může zosobnit váš uživatelský účet, použijte následující příkaz:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > Můžete ignorovat chyby, které naznačují, uživatel je již členem `users` skupiny.

## <a name="add-a-database-driver"></a>Přidat ovladač databáze

Vzhledem k tomu tento pracovní postup používá Sqoopu exportovat data do služby SQL database, je nutné zadat kopii ovladač JDBC používaných pro interakci s databází SQL. Zkopírujte ovladač JDBC do pracovního adresáře, použijte následující příkaz z relace SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Ověřte skutečné ovladač JDBC, který existuje v `/usr/share/java/`.

Pokud váš pracovní postup používá jiné prostředky, jako je například soubor jar obsahující aplikaci MapReduce, budete muset přidat také tyto prostředky.

## <a name="define-the-hive-query"></a>Definování dotazu Hive

Pomocí následujících kroků vytvořte skript jazyka (HiveQL) dotaz Hive, který definuje dotaz. V pracovním postupu Oozie dále v tomto dokumentu použijete dotaz.

1. V rámci připojení SSH pomocí následujícího příkazu vytvořte soubor s názvem `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Jakmile se otevře GNU nano editor, použijte tento dotaz jako obsah souboru:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existují dvě proměnné používané ve skriptu:

   * `${hiveTableName}`: Obsahuje název tabulky, který se má vytvořit.

   * `${hiveDataFolder}`: Obsahuje umístění pro uložení souborů dat pro tabulku.

     Soubor definice pracovního postupu, workflow.xml v tomto kurzu se tyto hodnoty předá tento skript HiveQL v době běhu.

4. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.  

5. Použijte následující příkaz pro kopírování `useooziewf.hql` k `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Tento příkaz uloží `useooziewf.hql` souboru v HDFS kompatibilního úložiště pro cluster.

## <a name="define-the-workflow"></a>Definování pracovního postupu

Definice pracovního postupu Oozie jsou napsané v Hadoop procesu Definition Language (hPDL), což je jazyk definice procesu XML. Následující postup použijte k definování pracovního postupu:

1. Pomocí následujícího příkazu vytvořte a upravte nový soubor:

    ```bash
    nano workflow.xml
    ```

2. Jakmile se otevře nano editor, zadejte následující kód XML jako obsah souboru:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Existují dvě akce, které jsou definovány v pracovním postupu:

   * `RunHiveScript`: Tato akce je spouštěcí akci a běží `useooziewf.hql` skript Hive.

   * `RunSqoopExport`: Tato akce exportuje data vytvořená ze skriptu Hivu do služby SQL database pomocí Sqoop. Tato akce spustí jenom v případě `RunHiveScript` akce je úspěšné.

     Pracovní postup obsahuje několik položek, jako například `${jobTracker}`. Tyto položky budou nahraďte hodnotami, které můžete použít v definici úlohy. Vytvoříte definici úlohy dále v tomto dokumentu.

     Všimněte si také, `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` položky v části Sqoop. Tato položka se dá pokyn Oozie zpřístupnit tento archiv Sqoop po spuštění této akce.

3. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.  

4. Použijte následující příkaz pro kopírování `workflow.xml` soubor `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Vytvoření tabulky

> [!NOTE]  
> Existuje mnoho způsobů, jak se připojit k databázi SQL vytvořte tabulku. V následujících krocích se používá [FreeTDS](http://www.freetds.org/) z clusteru HDInsight.

1. Následujícím příkazem nainstalujte FreeTDS v clusteru HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Upravit kód uvedený níže má nahradit `<serverName>` s názvem serveru Azure SQL, a `<sqlLogin>` se přihlašovací údaje pro server Azure SQL.  Zadejte příkaz pro připojení k SQL database kontrolu požadovaných součástí.  Zadejte heslo do příkazového řádku.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Zobrazí se výstup podobný následujícímu textu:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Na příkazovém řádku `1>` zadejte následující řádky:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tyto příkazy vytvoří tabulku s názvem `mobiledata`, používané tímto pracovním postupem.

    Pokud chcete ověřit, zda byl vytvořen v tabulce, použijte následující příkazy:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zobrazí se výstup podobný následujícímu textu:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Ukončete nástroj tsql tak, že zadáte `exit` na `1>` řádku.

## <a name="create-the-job-definition"></a>Vytvoření definice úlohy

Definici úlohy popisuje, kde se mají hledat workflow.xml. Také popisuje, kde najdete další soubory, které používá pracovní postup, jako například `useooziewf.hql`. Kromě toho definuje hodnoty pro vlastnosti používané v rámci pracovního postupu a přidružené soubory.

1. Pokud chcete získat úplnou adresu výchozí úložiště, použijte následující příkaz. Tato adresa se používá v konfiguračním souboru, který vytvoříte v dalším kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Tento příkaz vrátí informace, například následující kód XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Pokud HDInsight cluster používá Azure Storage jako výchozím úložištěm, `<value>` obsah elementu začínat `wasbs://`. Pokud místo toho používá Azure Data Lake Storage Gen1 začíná `adl://`. Pokud použijete Azure Data Lake Storage Gen2 začíná `abfs://`.

    Uložit obsah `<value>` element, protože se používá v dalších krocích.

2. Upravte následující xml následujícím způsobem:

    |Hodnotu zástupného symbolu| Nahrazená hodnota|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Hodnotu přijatou z kroku 1.|
    |admin| Vaše přihlašovací jméno pro clusteru HDInsight a pokud ne správce.|
    |název_serveru| Název serveru databáze Azure SQL.|
    |sqlLogin| Azure database přihlášení k SQL serveru.|
    |sqlPassword| Azure SQL database server heslo pro přihlášení.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Většinu informací v tomto souboru se používá k naplnění hodnot použitých v workflow.xml nebo ooziewf.hql souborů, jako například `${nameNode}`.  Pokud je cesta `wasbs` cestu, je nutné použít úplnou cestu. Není to jenom zkrátit `wasbs:///`. `oozie.wf.application.path` Položka definuje where workflow.xml soubor se nenašel. Tento soubor obsahuje pracovní postup, který byl spuštěn pomocí této úlohy.

3. Konfigurace definice úlohy Oozie vytvoříte pomocí následujícího příkazu:

    ```bash
    nano job.xml
    ```

4. Jakmile se otevře nano editor, vložte upravených XML jako obsah souboru.

5. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

## <a name="submit-and-manage-the-job"></a>Odeslat a spravovat úlohy

Následující postup použijte příkaz Oozie odesílat a spravovat pracovní postupy Oozie v clusteru. Příkaz Oozie je popisný rozhraní průběhu [rozhraní REST API pro Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Při použití příkazu Oozie musí použít plně kvalifikovaný název domény pro hlavní uzel HDInsight. Tento plně kvalifikovaný název domény přístupný pouze z clusteru, nebo pokud se cluster nachází ve službě Azure virtual network, z jiných počítačů ve stejné síti.

1. Pokud chcete získat adresu URL služby Oozie, použijte následující příkaz:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Vrátí informace, například následující kód XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` Část je adresa URL pro použití s příkazem Oozie.

2. Upravte kód nahraďte adresu URL, který jste získali dříve. Chcete-li vytvořit proměnnou prostředí pro adresu URL, použijte následující, takže není nutné k jeho zadání pro každý příkaz:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Úlohu odešlete následujícím způsobem:

    ```bash
    oozie job -config job.xml -submit
    ```

    Tento příkaz načte informace o úlohách z `job.xml` a odešle ji Oozie, ale nelze spustit.

    Po dokončení příkazu, měla by vrátit ID úlohy, například `0000005-150622124850154-oozie-oozi-W`. Toto ID se používá ke správě úlohy.

4. Upravit kód uvedený níže má nahradit `<JOBID>` s ID vrácené v předchozím kroku.  Chcete-li zobrazit stav úlohy, použijte následující příkaz:

    ```bash
    oozie job -info <JOBID>
    ```

    Vrátí informace, například následující text:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Tato úloha je ve stavu `PREP`. Tento stav indikuje, že úlohy byl vytvořen, ale nebyla spuštěna.

5. Upravit kód uvedený níže má nahradit `<JOBID>` s ID vrátil dříve.  Spustit úlohu, použijte následující příkaz:

    ```bash
    oozie job -start <JOBID>
    ```

    Pokud po spuštění tohoto příkazu můžete zkontrolovat stav, je ve spuštěném stavu, a vrátí informace pro akce v rámci úlohy.  Úloha bude trvat několik minut.

6. Upravit kód uvedený níže má nahradit `<serverName>` s názvem serveru Azure SQL, a `<sqlLogin>` se přihlašovací údaje pro server Azure SQL.  Po úspěšném dokončení úlohy můžete ověřit, že data byla vygenerována a exportovat do tabulky databáze SQL pomocí následujícího příkazu.  Zadejte heslo do příkazového řádku.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Na `1>` výzva, zadejte následující příkaz:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Vrácené informace se podobně jako následující text:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Další informace o příkazu Oozie najdete v tématu [nástroj příkazového řádku Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Rozhraní REST API pro Oozie

Pomocí rozhraní REST API Oozie můžete vytvořit své vlastní nástroje, které pracují s Oozie. HDInsight konkrétní informace o použití rozhraní REST API Oozie je následující:

* **IDENTIFIKÁTOR URI**: Můžete přístup k rozhraní REST API z mimo cluster v `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Ověřování**: K ověření, použijte rozhraní API pro účet clusteru HTTP (správce) a heslo. Příklad:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Další informace o tom, jak používat rozhraní REST API Oozie najdete v tématu [rozhraní API webových služeb Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webového uživatelského rozhraní

Webové uživatelské rozhraní Oozie poskytuje webové zobrazení stavu úlohy Oozie v clusteru. S webovým Uživatelským rozhraním se zobrazí následující informace:

   * Stav úlohy
   * Definice úlohy
   * Konfigurace
   * Graf akcí v rámci úlohy
   * Protokoly pro konkrétní úlohu

Můžete také zobrazit podrobnosti pro akce v rámci úlohy.

Pro přístup k webovým Uživatelským rozhraním Oozie, proveďte následující kroky:

1. Vytvoření tunelu SSH ke clusteru HDInsight. Další informace najdete v tématu [použití tunelování SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po vytvoření tunelu, otevřete ve webovém prohlížeči pomocí identifikátoru URI webového uživatelského rozhraní Ambari `http://headnodehost:8080`.

3. Na levé straně stránky vyberte **Oozie** > **rychlé odkazy** > **Oozie webového uživatelského rozhraní**.

    ![Obrázek nabídky](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Webové uživatelské rozhraní Oozie výchozí zobrazit spuštěné úlohy pracovního postupu. Pokud chcete zobrazit všechny úlohy pracovního postupu, vyberte **všechny úlohy**.

    ![Zobrazí všechny úlohy](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Chcete-li zobrazit další informace o úloze, vyberte úlohu.

    ![Informace o úloze](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Z **informace o úloze** kartu, můžete zobrazit informace o základních úlohách a jednotlivé akce v rámci úlohy. Karty v horní části můžete použít k zobrazení **definice úlohy**, **konfigurace úlohy**, přístup **protokol úloh**, nebo se podívejte do orientovaného acyklického grafu (DAG) úlohy v části **Úlohy DAG**.

   * **Úloha protokolu**: Vyberte **získat protokoly** tlačítko Načíst všechny protokoly pro konkrétní úlohu nebo použít **zadejte vyhledávací filtr** pole k filtrování protokolů.

       ![Protokol úlohy](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Úlohy DAG**: Orientovaného acyklického grafu je grafický přehled cesty k datům provedených v pracovním postupu.

       ![Úloha DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Pokud vyberete jednu z akcí z **informace o úloze** kartu, přináší informace o této akce. Vyberte například **RunSqoopExport** akce.

    ![Informace o akci](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Můžete zobrazit podrobnosti pro akci, jako je například odkaz **adresa URL konzoly**. Pomocí tohoto odkazu zobrazíte informace o úlohách sledovacího modulu pro konkrétní úlohu.

## <a name="schedule-jobs"></a>Plánování úloh

Koordinátor můžete použít k určení zahájení, skončí a četnost výskytu pro úlohy. Pokud chcete definovat plán pro pracovní postup, proveďte následující kroky:

1. Pomocí následujícího příkazu vytvořte soubor s názvem **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Použijte následující kód XML jako obsah souboru:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > `${...}` Proměnné jsou nahrazené hodnoty v definici úlohy za běhu. Proměnné jsou:
    >
    > * `${coordFrequency}`: Doba mezi spuštěné instance úlohy.
    > * `${coordStart}`: Čas spuštění úlohy.
    > * `${coordEnd}`: Čas ukončení úlohy.
    > * `${coordTimezone}`: Koordinátor úlohy jsou v pevné časové pásmo s žádné letní čas, obvykle reprezentovaný pomocí času UTC. Toto časové pásmo se označuje jako *Oozie zpracování časové pásmo.*
    > * `${wfPath}`: Cesta workflow.xml.

2. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

3. Pokud chcete zkopírovat soubor do pracovního adresáře pro tuto úlohu, použijte následující příkaz:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Chcete-li změnit `job.xml` souborů, které jste vytvořili dříve, pomocí následujícího příkazu:

    ```bash
    nano job.xml
    ```

    Proveďte následující změny:

   * Chcete-li dát pokyn Oozie ke spuštění souboru koordinátor místo pracovního postupu, změňte `<name>oozie.wf.application.path</name>` k `<name>oozie.coord.application.path</name>`.

   * Chcete-li nastavit `workflowPath` proměnné použité koordinátorem, přidejte následující kód XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Nahradit `wasbs://mycontainer@mystorageaccount.blob.core.windows` text s hodnotou použitou v jiných položek v souboru job.xml.

   * Chcete-li definovat zahájení, ukončení a frekvenci koordinátor, přidejte následující kód XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Tyto hodnoty nastaveny, počáteční čas 12:00 PM 10. května 2018 a koncový čas do 12. května 2018. Interval pro spuštění této úlohy je nastavený na hodnotu denně. Je frekvence v minutách, takže 24 hodin × 60 minut = 1 440 minut. A konečně časové pásmo je nastavena na čas UTC.

5. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

6. Odeslat a spustit úlohu, použijte následující příkaz:

    ```bash
    oozie job -config job.xml -run
    ```

7. Pokud přejdete Oozie webového uživatelského rozhraní a vyberte **koordinátor úlohy** kartu, se zobrazí informace, například na následujícím obrázku:

    ![Koordinátor úlohy kartu](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **Další Materializace** položka obsahuje při příštím spuštění úlohy.

8. Jako předchozí úlohy pracovního postupu Pokud vyberete položku úlohy ve webovém uživatelském rozhraní zobrazuje informace na úloze:

    ![Informace o úloze Coordinator](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]  
    > Tento obrázek zobrazuje jenom úspěšná spuštění úlohy, nikoli jednotlivé akce v rámci naplánovaný workflow. Chcete-li zobrazit jednotlivé akce, vyberte jednu z **akce** položky.

    ![Informace o akci](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Řešení potíží

S uživatelským rozhraním Oozie můžete zobrazit protokoly Oozie. Oozie uživatelského rozhraní obsahuje taky odkazy na protokoly JobTracker pro úlohy MapReduce, které byly spuštěny pracovní postup. Vzor pro řešení potíží s by měl být:

   1. Zobrazte úlohy v Oozie webového uživatelského rozhraní.

   2. Pokud dojde k chybě nebo selhání konkrétní akce, vyberte akci a zjistěte, jestli **chybová zpráva** pole poskytuje další informace o selhání.

   3. Pokud je k dispozici, použijte adresu URL z akce zobrazíte další podrobnosti, jako jsou protokoly JobTracker, pro akci.

Níže jsou uvedeny konkrétní chyby, které se můžete setkat a způsob jejich řešení.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nepodařilo se inicializovat clusteru

**Příznaky**: Stav úlohy změní na **POZASTAVENO**. Podrobnosti o úloze zobrazit `RunHiveScript` stav jako **START_MANUAL**. Výběr akce se zobrazí následující chybová zpráva:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Příčina:** Adresy objektů Blob v Azure storage, které se používá v **job.xml** soubor neobsahuje kontejner úložiště nebo název účtu úložiště. Musí být ve formátu adresa úložiště objektů Blob `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Rozlišení**: Změna adresy úložiště objektů Blob, které používá úlohy.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Není povoleno zosobnit Oozie &lt;uživatele&gt;

**Příznaky**: Stav úlohy změní na **POZASTAVENO**. Podrobnosti o úloze zobrazit `RunHiveScript` stav jako **START_MANUAL**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    JA002: User: oozie is not allowed to impersonate <USER>

**Příčina:** Aktuální nastavení oprávnění neumožňují Oozie zosobnit zadaný uživatelský účet.

**Rozlišení**: Oozie můžou vydávat za uživatele v **uživatelé** skupiny. Použití `groups USERNAME` zobrazíte skupiny, ke kterým je uživatelský účet členem. Pokud uživatel není členem **uživatelé** skupině, použijte následující příkaz pro přidání uživatele do skupiny:

    sudo adduser USERNAME users

> [!NOTE]  
> Může trvat několik minut, než HDInsight rozpozná, že uživatel byl přidán do skupiny.

### <a name="launcher-error-sqoop"></a>Spouštěč chyby (Sqoop)

**Příznaky**: Stav úlohy změní na **KILLED**. Podrobnosti o úloze zobrazit `RunSqoopExport` stav jako **chyba**. Pokud vyberete akci, zobrazí se následující chybová zpráva:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Příčina:** Sqoop není schopen načíst ovladač databáze vyžadované pro přístup k databázi.

**Rozlišení**: Při použití Sqoopu z úlohu Oozie musí zahrnovat ovladač databáze s další prostředky, jako je například workflow.xml, tato úloha používá. Také odkazovat na archiv, který obsahuje databázi ovladače z `<sqoop>...</sqoop>` část workflow.xml.

Například pro úlohy v tomto dokumentu použijete následující kroky:

1. Kopírovat `mssql-jdbc-7.0.0.jre8.jar` do souboru **/kurzy/useoozie** adresáře:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Upravit `workflow.xml` a přidejte následující kód XML na nový řádek nad `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak definovat pracovní postup Oozie a jak se spouští úloha Oozie. Další informace o tom, jak pracovat s HDInsight, naleznete v následujících článcích:

* [Nahrání dat pro úlohy Apache Hadoop v HDInsight][hdinsight-upload-data]
* [Použití Apache Sqoop s Apache Hadoop v HDInsight][hdinsight-use-sqoop]
* [Použití Apache Hivu s Apache Hadoop v HDInsight][hdinsight-use-hive]
* [Použití Apache Pig s Apache Hadoop v HDInsight][hdinsight-use-pig]
* [Vývoj programů Java MapReduce pro HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
