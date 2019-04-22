---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Další informace o použití Beeline klienta ke spouštění dotazů Hive se systémem Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 prostřednictvím JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257123"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití Apache Beeline klienta s Apache Hive

Další informace o použití [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) spustit dotazy Apache Hive v HDInsight.

Beeline je klient Hive, který je součástí hlavní uzly clusteru HDInsight. Beeline používá JDBC pro připojení k serveru HiveServer2, služba hostovaná v clusteru HDInsight. Také vám pomůže Beeline Hive v HDInsight přistupovat vzdáleně přes internet. Následující příklady popisují nejběžnější připojovací řetězce pro připojení k HDInsight z Beeline:

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Když se připojujete z relace SSH k hlavnímu uzlu clusteru, pak můžete připojit k `headnodehost` adresu na portu `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Prostřednictvím služby Azure Virtual Network

Při připojování z klienta k HDInsight prostřednictvím služby Azure Virtual Network, musíte plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Protože toto připojení se provádí přímo na uzlech clusteru, připojení používá port `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Nahraďte `<headnode-FQDN>` s plně kvalifikovaný název domény hlavního uzlu clusteru. Pokud chcete zjistit plně kvalifikovaný název domény hlavního uzlu, použijte informace v [Správa HDInsight pomocí rozhraní REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentu.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Do clusteru HDInsight Enterprise Security Package (ESP)

Když připojení z klienta do clusteru Enterprise Security Package (ESP) připojí k Azure Active Directory (AAD), musíte také zadat název domény `<AAD-Domain>` a název domény uživatelského účtu s oprávněními pro přístup ke clusteru `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` s názvem účtu domény s oprávněními pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` s názvem aplikace Azure Active Directory (AAD), ke které je připojený clusteru. Použít velkých řetězec pro `<AAD-DOMAIN>` hodnotu, jinak přihlašovací údaje, které nebude nalezena. Zkontrolujte `/etc/krb5.conf` sféry názvů v případě potřeby.

---

### <a name="over-public-internet"></a>Prostřednictvím veřejného Internetu

Když se připojíte přes veřejný internet, je nutné zadat název účtu přihlášení clusteru (výchozí `admin`) a heslo. Například použití Beeline ze systému klienta pro připojení k `<clustername>.azurehdinsight.net` adresu. Toto připojení se provádí přes port `443`a je zašifrovaná pomocí SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` s účet přihlášení clusteru pro váš cluster. Nahraďte `password` se heslo pro účet přihlášení clusteru.

---

### <a id="sparksql"></a>Použití Beeline s Apache Sparkem

Apache Spark poskytuje vlastní implementaci serveru HiveServer2, který se někdy označuje jako server Spark Thrift. Tato služba překladu místo Hive pomocí Spark SQL a může poskytovat lepší výkon v závislosti na dotazu.

#### <a name="over-public-internet-with-apache-spark"></a>Přes veřejný internet s Apache Sparkem

Připojovací řetězec použitý při připojení přes internet se mírně liší. Anglický `httpPath=/hive2` je `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z clusteru head nebo uvnitř Azure Virtual Network s Apache Sparkem

Když se připojujete přímo z hlavního uzlu clusteru, nebo z prostředků ve stejné virtuální síti Azure jako HDInsight cluster, přenést `10002` byste měli použít pro server Spark Thrift, nikoli `10001`. Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Požadavky

* Cluster Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si, že [schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro váš cluster primárního úložiště. Například `wasb://` pro službu Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, je identifikátor URI `wasbs://` nebo `abfss://`v uvedeném pořadí. Další informace najdete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).


* Option 1: Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Většina kroků v tomto dokumentu předpokládají, že používáte Beeline z relace SSH do clusteru.

* Option 2:  Místní Beeline klienta.


## <a id="beeline"></a>Spuštění dotazu Hive

Tento příklad je založen na použití Beeline klienta z připojení SSH.

1. Otevřete připojení SSH ke clusteru pomocí kódu níže. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Po zobrazení výzvy zadejte heslo pro uživatelský účet SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Připojení k serveru HiveServer2 klientem Beeline z otevřít relaci SSH tak, že zadáte následující příkaz:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Příkazy beeline začínat `!` znak, třeba `!help` zobrazí nápovědu. Ale `!` může vynechat některé příkazy. Například `help` také funguje.

    Je `!sql`, který se používá k provedení příkazy HiveQL. HiveQL se ale tak běžně používá, můžete vynechat předchozí `!sql`. Následující dva příkazy jsou ekvivalentní:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Na novém clusteru je uveden pouze jednu tabulku: **hivesampletable**.

4. Chcete-li zobrazit schéma pro hivesampletable použijte následující příkaz:

    ```hiveql
    describe hivesampletable;
    ```

    Tento příkaz vrátí následující informace:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Tyto informace popisují sloupců v tabulce.

5. Zadejte následující příkazy k vytvoření tabulky s názvem **log4jLogs** pomocí ukázkových dat, které jsou součástí clusteru HDInsight: (Na základě opravit, podle potřeby vaše [schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Tyto příkazy provádět následující akce:

    * `DROP TABLE` – Pokud existuje v tabulce, je odstranit.

    * `CREATE EXTERNAL TABLE` -Vytvoří **externí** tabulky v Hivu. Externí tabulky pouze uložte definici tabulky Hive. Data zůstane v původním umístění.

    * `ROW FORMAT` -Způsob formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

    * `STORED AS TEXTFILE LOCATION` -Pokud jsou data uložená a v jakém formátu souboru.

    * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento dotaz vrátí hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive se pokusí použít schéma pro všechny soubory v adresáři. V takovém případě adresář obsahuje soubory, které neodpovídají schématu. Chcete-li zabránit uvolňování paměti ve výsledcích, tento příkaz sděluje Hive, že ji by měl vrátit pouze data ze souborů s koncovkou. log.

   > [!NOTE]  
   > Pokud očekáváte, že podkladová data aktualizovat externího zdroje je třeba použít externí tabulky. Například automatizovaných datových odesílat operaci MapReduce nebo procesu.
   >
   > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.

    Výstup tohoto příkazu se podobá následujícímu textu:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Chcete-li ukončit Beeline, použijte `!exit`.

## <a id="file"></a>Spusťte soubor HiveQL

To je pokračováním z předchozího příkladu. Pomocí následujících kroků vytvořte soubor a pak ji spustit pomocí Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **query.hql**:

    ```bash
    nano query.hql
    ```

2. Použijte následující text jako obsah souboru. Tento dotaz vytvoří novou tabulku "vnitřní" s názvem **nepřenesl**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádět následující akce:

   * **Vytvoření tabulky IF NOT EXISTS** – Pokud tabulka již neexistuje, vytvoří se. Vzhledem k tomu, **externí** – klíčové slovo se nepoužívá, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložené v datovém skladu Hive a jsou kompletně spravované pomocí Hive.
   * **ULOŽENÉ jako ORC** – ukládá data ve formátu optimalizované řádek úložiště se sloupcovou strukturou (ORC). Formát ORC je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.
   * **VLOŽIT PŘEPSÁNÍ... Vyberte** -vybere řádky z **log4jLogs** tabulce, která obsahuje **[Chyba]**, pak vloží data do **nepřenesl** tabulky.

    > [!NOTE]  
    > Na rozdíl od externích tabulek vyřadit interní tabulku odstraní podkladová data.

3. Chcete-li uložit soubor, použijte **Ctrl**+**_X**, zadejte **Y**a nakonec **Enter**.

4. Pomocí následujících spusťte soubor pomocí Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` Parametr spustí Beeline a provede příkazy ve `query.hql` souboru. Po dokončení dotazu se dostanete na `jdbc:hive2://headnodehost:10001/>` řádku. Můžete také spustit soubor pomocí `-f` parametr, který ukončí Beeline po dokončení dotazu.

5. Pro ověření, že **nepřenesl** byla vytvořena tabulka, použijte následující příkaz vrátí všechny řádky z **nepřenesl**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tří řádků dat by měla být vrácena, všechny obsahující **[Chyba]** ve sloupci t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Další kroky

Další obecné informace o Hivu ve službě HDInsight najdete v následujícím dokumentu:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o jiných způsobech mohl pracovat s Hadoop v HDInsight najdete v následujících dokumentech:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
