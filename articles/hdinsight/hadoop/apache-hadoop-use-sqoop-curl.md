---
title: Používáme nástroj Curl k exportu dat pomocí Apache Sqoop v Azure HDInsight
description: Zjistěte, jak vzdálené odeslání úloh Apache Sqoop k HDInsight pomocí příkazu Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718686"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Spouštět úlohy Apache Sqoop v HDInsight pomocí Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Zjistěte, jak používáme nástroj Curl můžete spouštět úlohy Apache Sqoop na cluster Apache Hadoop v HDInsight. Tento článek ukazuje, jak exportovat data z úložiště Azure a naimportujte ho do databáze SQL serveru pomocí příkazu Curl. V tomto článku je pokračování [použití Apache Sqoop se systémem Hadoop v HDInsight](./hdinsight-use-sqoop.md).

Curl slouží k předvedení toho, jak můžete pracovat s HDInsight pomocí nezpracované požadavky HTTP na spuštění, monitorování a načtení výsledků úlohy Sqoop. Tento postup funguje s použitím rozhraní WebHCat REST API (dříve známé jako Templeton) k dispozici ve vašem clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavit testovací prostředí](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [použití Apache Sqoop se systémem Hadoop v HDInsight](./hdinsight-use-sqoop.md).

* Klient k dotazování Azure SQL database. Zvažte použití [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) nebo [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl je nástroj pro přenos dat z nebo do clusteru HDInsight.

* [jq](https://stedolan.github.io/jq/). Nástroj jq se používá ke zpracování JSON data vrácená z požadavky REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Odesílání úloh Apache Sqoop pomocí Curl

Používáme nástroj Curl k exportu dat použití Apache Sqoop úloh ze služby Azure storage k systému SQL Server.

> [!NOTE]  
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícímu k odesílání požadavků na server.

Příkazy v této části nahraďte `USERNAME` uživatelem pro ověření clusteru a nahraďte `PASSWORD` heslem pro uživatelský účet. Nahraďte `CLUSTERNAME` názvem svého clusteru.
 
Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS (Secure HTTP) a pomoci tak zajistit, že přihlašovací údaje budou na server odeslány bezpečně.

1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Měla by se zobrazit odpověď podobná následujícímu:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Nahraďte `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` příslušnými hodnotami z požadavků. Použijte následující postup k odeslání sqoop úlohy:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Parametry použité v tomto příkazu jsou následující:

   * **-d** – od `-G` se nepoužívá výchozí nastavení požadavku pro metodu POST. `-d` Určuje hodnoty dat, které se odesílají s požadavkem.

       * **User.Name** – uživatel, který spouští příkaz.

       * **příkaz** – The Sqoop příkazu ke spuštění.

       * **statusdir** – adresář, který stavu pro tuto úlohu se zapíšou do.

     Tento příkaz vrátí ID úlohy, který slouží ke kontrole stavu úlohy.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz. Nahraďte `JOBID` pomocí hodnoty vrácené v předchozím kroku. Například, pokud se návratová hodnota `{"id":"job_1415651640909_0026"}`, pak `JOBID` by `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Pokud úloha dokončí, bude mít stav **SUCCEEDED**.
   
   > [!NOTE]  
   > Tento požadavek Curl vrátí dokument JavaScript Object Notation (JSON) se informace o úloze; jq slouží k načtení jenom hodnoty stavu.

4. Jakmile se stav úlohy se změnila na **SUCCEEDED**, můžete načíst výsledky úlohy z úložiště objektů Blob v Azure. `statusdir` Parametr předaný s dotazem obsahuje umístění výstupního souboru; v takovém případě `wasb:///example/data/sqoop/curl`. Tuto adresu se ukládá výstup úlohy `example/data/sqoop/curl` adresář na výchozí kontejner úložiště používá HDInsight cluster.

    Na webu Azure portal můžete použít pro přístup k objektům BLOB stderr a stdout.

5. Pokud chcete ověřit, že se data vyexportovala, použijte následující dotazy z vašeho klienta SQL exportovaná data zobrazíte:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Omezení
* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje operace hromadného vložení.
* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop provede několik vloží místo dávkování operace vložení.

## <a name="summary"></a>Souhrn
Jak je ukázáno v tomto dokumentu, můžete spouštět, monitorovat a zobrazit výsledky Sqoop úloh ve vašem clusteru HDInsight nezpracovaná požadavku HTTP.

Další informace o rozhraní REST použité v tomto článku najdete v článku <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">pokyny k rozhraní API REST Apache Sqoop</a>.

## <a name="next-steps"></a>Další postup
[Použití Apache Sqoop se Apache Hadoop v HDInsight](hdinsight-use-sqoop.md)

Pro další HDInsight články zahrnující curl:
 
* [Vytvořte clustery systému Apache Hadoop pomocí rozhraní Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Spustit dotazy Apache Hive s Apache Hadoop v HDInsight pomocí rozhraní REST](apache-hadoop-use-hive-curl.md)
* [Spuštění úlohy mapreduce je možné s Apache Hadoop v HDInsight pomocí rozhraní REST](apache-hadoop-use-mapreduce-curl.md)
* [Spuštění úlohy Apache Pig s Apache Hadoop v HDInsight pomocí cURL](apache-hadoop-use-pig-curl.md)
