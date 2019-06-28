---
title: 'Kurz: Provedení extrakce, transformace, načítání (ETL) operací s použitím Apache Hive v Azure HDInsight'
description: V tomto kurzu přečtěte si, jak extrahovat data z datové sady nezpracovaná sdíleného svazku clusteru, je transformovat pomocí Apache Hive v Azure HDInsight a pak načítat Transformovaná data do Azure SQL Database pomocí Sqoop.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f28cae7ed56b694f4194adf78c288ffa87eb71d8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447732"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Kurz: Extrakce, transformace a načítání dat pomocí Apache Hive v Azure HDInsight

V tomto kurzu se provést operaci ETL: extrakce, transformace a načítání dat. Provést soubor nezpracovaných dat sdíleného svazku clusteru, naimportujete do clusteru Azure HDInsight, je transformovat pomocí Apache Hive a načtení do služby Azure SQL database s Apache Sqoop.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Extrahování a nahrávání dat do clusteru HDInsight.
> * Transformace dat pomocí Apache Hive.
> * Načtení dat do služby Azure SQL database pomocí Sqoop.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Účet úložiště Azure Data Lake Storage Gen2, který je nakonfigurovaný pro HDInsight**

    Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Cluster systémem Linux Hadoop v HDInsight**

    Zobrazit [rychlý start: Začínáme s Apache Hadoop a Apache Hive v Azure HDInsight pomocí webu Azure portal](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: Databázi Azure SQL použijete jako cílové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL na webu Azure Portal](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Klient Secure Shell (SSH)** : Další informace najdete v tématu [připojení k HDInsight (Hadoop) pomocí protokolu SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte do [výzkumu a inovativní technologie správy, Bureau of Transportation statistiky](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stránce vyberte následující hodnoty:

   | Název | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2013 |
   | Filter Period (Filtr období) |January (Leden) |
   | Fields (Pole) |Rok, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, původu, OriginCityName, OriginState, DestAirportID, cíl, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Zrušte zaškrtnutí všech ostatních polí.

3. Vyberte **Download** (Stáhnout). Získáte soubor .zip s vybranými datovými poli.

## <a name="extract-and-upload-the-data"></a>Extrahování a nahrávání dat

V této části budete odesílat data do vašeho clusteru HDInsight a pak zkopírovat tato data do účtu Data Lake Storage Gen2.

1. Otevřete příkazový řádek a použijte následující příkaz zabezpečení Copy (Scp) pro nahrání souboru ZIP k hlavnímu uzlu clusteru HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Nahradit `<file-name>` zástupný prvek s názvem souboru ZIP.
   * Nahradit `<ssh-user-name>` zástupný symbol přihlašování přes SSH pro HDInsight cluster.
   * Nahradit `<cluster-name>` zástupný symbol název clusteru HDInsight.

   Pokud k ověření přihlášení SSH používáte heslo, zobrazí se výzva k zadání hesla.

   Pokud používáte veřejný klíč, budete pravděpodobně muset použít parametr `-i` k zadání cesty k odpovídajícímu privátnímu klíči. Například, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Po dokončení nahrávání se ke clusteru připojte pomocí SSH. Na příkazovém řádku zadejte následující příkaz:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Pomocí následujícího příkazu rozbalte soubor .zip:

   ```bash
   unzip <file-name>.zip
   ```

   Příkaz extrahuje **CSV** souboru.

4. Následujícím příkazem vytvořte v systému souborů Data Lake Storage Gen2.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Nahradit `<file-system-name>` zástupným názvem, který chcete udělit systému souborů.

   Nahradit `<storage-account-name>` zástupný symbol s názvem účtu úložiště.

5. Použijte následující příkaz k vytvoření adresáře.

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Použijte následující příkaz pro kopírování *CSV* soubor do adresáře:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Pokud název souboru obsahuje mezery ani speciální znaky, použijte uvozovky kolem názvu souboru.

## <a name="transform-the-data"></a>Transformace dat

V této části použijete Beeline na spuštění úlohy Apache Hive.

Jako součást úlohy Apache Hive, importujete data ze souboru CSV do tabulky Apache Hive s názvem **zpoždění**.

1. Ze SSH dotaz, který už máte pro HDInsight cluster, použijte následující příkaz k vytvoření a úprava nový soubor s názvem **flightdelays.hql**:

   ```bash
   nano flightdelays.hql
   ```

2. Upravte následující text tak, že nahradit `<file-system-name>` a `<storage-account-name>` zástupné texty názvem svého souboru systému a úložiště účtu. Pak zkopírujte a vložte text do konzole nano serveru pomocí klávesy SHIFT spolu s tlačítko kliknutím pravého tlačítka myši.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Uložte soubor pomocí kláves CTRL + X a pak zadejte `Y` po zobrazení výzvy.

4. Spusťte Hive a soubor **flightdelays.hql** pomocí následujícího příkazu:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Po dokončení skriptu __flightdelays.hql__ pomocí následujícího příkazu otevřete interaktivní relaci Beeline:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. Po zobrazení příkazového řádku `jdbc:hive2://localhost:10001/>` pomocí následujícího dotazu načtěte data z importovaných dat o zpožděných letech:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Tento dotaz načte seznam měst, ve kterých došlo ke zpožděním kvůli nepřízni počasí, společně s průměrnou délkou zpoždění a uloží ho do umístění `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Později z tohoto umístění data načte Sqoop a exportuje je do služby Azure SQL Database.

7. Beeline ukončíte zadáním `!quit` na příkazovém řádku.

## <a name="create-a-sql-database-table"></a>Vytvoření tabulky databáze SQL

Potřebujete název serveru z databáze SQL pro tuto operaci. Dokončete tyto kroky pro vyhledání názvu serveru.

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. Vyberte **databází SQL**.

3. Filtrovat podle názvu databáze, které chcete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

4. Filtrovat podle názvu databáze, kterou chcete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

    ![Získání podrobností o serveru SQL Azure](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Získání podrobností o serveru SQL Azure")

    Existuje mnoho způsobů, jak se připojit ke službě SQL Database a vytvořit tabulku. V následujících krocích se používá [FreeTDS](https://www.freetds.org/) z clusteru HDInsight.

5. Pokud chcete nainstalovat FreeTDS, použijte následující příkaz z připojení SSH ke clusteru:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Po dokončení instalace, použijte následující příkaz pro připojení k databázi SQL serveru.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Nahradit `<server-name>` zástupný symbol s názvem serveru SQL Database.

   * Nahradit `<admin-login>` zástupný text přihlašovací jméno správce pro službu SQL Database.

   * Nahradit `<database-name>` zástupný symbol název databáze

   Po zobrazení výzvy zadejte heslo pro přihlašovací jméno správce SQL Database.

   Zobrazí se výstup podobný následujícímu textu:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Na `1>` výzva, zadejte následující příkazy:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Po zadání příkazu `GO` se vyhodnotí předchozí příkazy.

   Dotaz vytvoří tabulku s názvem **zpoždění**, která má clusterovaný index.

9. Pomocí následujícího dotazu ověřte, že se vytvořil v tabulce:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Výstup se bude podobat následujícímu:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Zadáním `exit` na příkazovém řádku `1>` ukončete nástroj tsql.

## <a name="export-and-load-the-data"></a>Export a načtení dat.

V předchozích částech zkopírovali Transformovaná data do umístění `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. V této části použít Sqoop k exportování dat z `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` do tabulky, které jste vytvořili ve službě Azure SQL database.

1. Pomocí následujícího příkazu ověřte, že má Sqoop vhled do vaší databáze SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Příkaz vrátí seznam databází, včetně databáze, ve které jste vytvořili **zpoždění** tabulky.

2. Použijte následující příkaz k exportu dat z **hivesampletable** tabulky **zpoždění** tabulky:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop se připojí k databázi, která obsahuje **zpoždění** tabulky a exportuje data z `/tutorials/flightdelays/output` do adresáře **zpoždění** tabulky.

3. Po `sqoop` dokončení příkazu, použijte nástroj tsql pro připojení k databázi:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Použijte následující příkazy k ověření, že data se exportují do **zpoždění** tabulky:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Měl by se zobrazit výpis dat v tabulce. Tabulka obsahuje název města a průměrnou délku zpoždění letu pro příslušné město.

5. Zadejte `exit` ukončete nástroj tsql.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky používané v tomto kurzu jsou již existující. Žádné čištění je nezbytné.

## <a name="next-steps"></a>Další postup

Přečtěte si další způsoby, jak pracovat s daty v HDInsight, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
