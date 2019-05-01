---
title: Začínáme s příkladem HBase v HDInsight – Azure
description: Postupujte podle tohoto příkladu Apache HBase a začněte používat Hadoop ve službě HDInsight. Vytvářejte tabulky z prostředí HBase a dotazujte je pomocí Hive.
keywords: příkaz hbase,příklad hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718877"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Začínáme s příkladem Apache HBase ve službě HDInsight

Zjistěte, jak vytvořit [Apache HBase](https://hbase.apache.org/) clusteru v HDInsight vytvářet tabulky HBase a dotazovat tabulky pomocí [Apache Hive](https://hive.apache.org/).  Obecné informace o HBase najdete v tématu [Přehled HBase ve službě HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Požadavky
Než se pustíte do tohoto příkladu HBase, musíte mít následující položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Vytvoření clusteru Apache HBase
Následující postup používá šablonu Azure Resource Manageru pro vytvoření clusteru HBase a výchozího účtu služby Azure Storage. Pro lepší pochopení parametrů použitých v postupu a dalších metod vytvoření clusteru si projděte téma [Vytvoření Hadoop clusterů se systémem Linux v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o použití Data Lake Storage Gen2 najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Kliknutím na následující obrázek otevřete šablonu na portálu Azure Portal. Tato šablona je umístěná na [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. V okně **Vlastní nasazení** zadejte následující hodnoty:
   
   * **Předplatné**: Vyberte své předplatné Azure, který se používá k vytvoření clusteru.
   * **Skupina prostředků**: Vytvořit skupinu pro správu prostředků Azure nebo použijte již existující.
   * **Umístění**: Zadejte umístění skupiny prostředků. 
   * **Název clusteru**: Zadejte název pro HBase cluster.
   * **Přihlašovací jméno a heslo clusteru**: Výchozí přihlašovací jméno je **admin** (správce).
   * **Uživatelské jméno SSH a heslo**: Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Můžete ho změnit.
     
     Další parametry jsou volitelné.  
     
     Každý cluster obsahuje závislost účtu Azure Storage. Po odstranění clusteru se data zachovají na účtu úložiště. Výchozí název účtu úložiště clusteru je název clusteru s připojenou příponou „úložiště“. Je pevně kódovaný v části proměnných šablon.
3. Vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a klikněte na **Koupit**. Vytvoření clusteru trvá přibližně 20 minut.

> [!NOTE]  
> Po odstranění clusteru služby HBase můžete vytvořit jiný cluster HBase pomocí stejného výchozího kontejneru blob. Nový cluster převezme tabulky HBase, které jste vytvořili v původním clusteru. Aby se zabránilo nekonzistencím, doporučujeme zakázat tabulky HBase před odstraněním clusteru.
> 
> 

## <a name="create-tables-and-insert-data"></a>Vytváření tabulek a vkládání dat
SSH slouží k připojení ke clusterům HBase a používání [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) k vytváření tabulek HBase, vkládání dat a dotazování na data. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight HBase][img-hbase-sample-data-tabular]

V HBase (implementace [cloudu BigTable](https://cloud.google.com/bigtable/)), vypadají stejná data následovně:

![Velké objemy tabulkových dat HDInsight HBase][img-hbase-sample-data-bigtable]


**Použití prostředí HBase**

1. Ze SSH spusťte následující příkaz HBase:
   
    ```bash
    hbase shell
    ```

2. Vytvořte HBase se skupinami o dvou sloupcích:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Vložte některá data:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![Prostředí HDInsight Hadoop HBase][img-hbase-shell]
4. Získání jednoho řádku
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Měly by se zobrazit stejné výsledky jako pomocí příkazu vyhledávání, protože existuje pouze jeden řádek.
   
    Další informace o schématu tabulky HBase najdete v tématu [Úvod do navrhování schémat HBase Apache][hbase-schema]. Další příkazy HBase najdete v tématu [Referenční příručka Apache HBase][hbase-quick-start].
5. Opusťte prostředí
   
    ```hbaseshell
    exit
    ```

**Hromadné načítání dat do tabulky kontaktů HBase**

HBase obsahuje několik metod načítání dat do tabulek.  Další informace naleznete v tématu [Hromadné načítání](https://hbase.apache.org/book.html#arch.bulk.load).

Ukázkový datový soubor nachází v kontejneru objektů blob veřejný, *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  Obsah datového souboru je:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Volitelně můžete vytvořit textový soubor a nahrát ho do vlastního účtu úložiště. Pokyny najdete v tématu [nahrávání dat pro úlohy Apache Hadoop v HDInsight][hdinsight-upload-data].

> [!NOTE]  
> Tento postup používá tabulku kontaktů HBase, kterou jste vytvořili v posledním postupu.

1. Ze SSH spusťte následující příkaz, který transformuje datový soubor na StoreFiles a uloží ho do relativní cesty určené parametrem Dimporttsv.bulk.output.  Pokud jste v prostředí HBase, odejděte pomocí příkazu exit.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Spusťte následující příkaz a nahrajte data z adresy /example/data/storeDataFileOutput do tabulky HBase:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Prostředí HBase můžete otevřít a použít příkaz skenování k zobrazení seznamu obsahu tabulky.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Použití Apache Hive k dotazování služby Apache HBase

Data v tabulkách HBase můžete dotazovat pomocí [Apache Hive](https://hive.apache.org/). V této části vytvoříte tabulku Hive, která se namapuje na tabulku HBase, a použijete ji k dotazování dat v tabulce HBase.

1. Otevřete **PuTTY** a připojte se ke clusteru.  Pokyny naleznete v předchozím postupu.
2. Z relace SSH pomocí následujícího příkazu spusťte Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Další informace o Beeline najdete v tématu [Použití Hivu s Hadoopem ve službě HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
       
3. Spusťte následující příkaz [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skript k vytvoření tabulky Hive, která se mapuje na tabulku HBase. Před spuštěním tohoto prohlášení ověřte, zda jste vytvořili ukázkové tabulky odkazované dříve v tomto kurzu pomocí prostředí HBase.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Spusťte následující skript HiveQL pro dotaz na data v tabulce HBase:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Použití rozhraní REST API HBase pomocí Curl

Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Požadavky byste vždy měli provádět pomocí protokolu HTTPS (Secure HTTP), čímž pomůžete zajistit, že se přihlašovací údaje budou na server odesílat bezpečně.

1. Pomocí následujícího příkazu můžete zobrazit seznam existujících tabulek HBase:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Pokud chcete vytvořit novou tabulku HBase se dvěma skupinami sloupců, použijte následující příkaz:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schéma je k dispozici ve formátu JSon.
1. Chcete-li vložit nějaká data použijte následující příkaz:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    Hodnoty určené v přepínači -d musíte zakódovat base64. V tomto příkladu:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Osobní: Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) umožňuje vložit více (dávkových) hodnot.
1. Pro získání řádku použijte následující příkaz:
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Další informace o HBase Rest naleznete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.
>
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícího k odesílání požadavků na server:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Měla by se zobrazit odpověď podobná následující odpovědi:
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>Kontrola stavu clusteru
HBase v HDInsight se dodává s webovým uživatelským rozhraním pro sledování clusterů. Pomocí webového uživatelského rozhraní, můžete žádat o statistické údaje nebo informace o oblastech.

**Přístup k hlavnímu uživatelskému rozhraní HBase**

1. Přihlásit se do webového uživatelského rozhraní Ambari na https://&lt;Název_clusteru >. azurehdinsight.net.
2. V nabídce vlevo klikněte na **HBase**.
3. V horní části stránky klikněte na **Rychlé odkazy**, najeďte myší na odkaz na aktivní uzel Zookeeper a klikněte na **Hlavní uživatelské rozhraní HBase**.  Uživatelské rozhraní se otevře na nové kartě prohlížeče:

   ![Hlavní uživatelské rozhraní HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Hlavní uživatelské rozhraní HBase obsahuje tyto části:

   - oblastní servery
   - zálohování hlavních serverů
   - tabulky
   - úlohy
   - atributy softwaru

## <a name="delete-the-cluster"></a>Odstranění clusteru
Aby se zabránilo nekonzistencím, doporučujeme zakázat tabulky HBase před odstraněním clusteru.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak vytvořit cluster Apache HBase a jak vytvářet tabulky a zobrazovat data v těchto tabulkách z prostředí HBase. Také jste se naučili, jak používat dotazy na data Hive v tabulkách HBase a jak používat rozhraní REST API HBase C# k vytvoření tabulky HBase a načtení dat z tabulky.

Další informace naleznete v tématu:

* [Přehled HDInsight HBase][hdinsight-hbase-overview]: Apache HBase je databáze NoSQL open source Apache postavená na Apache Hadoopu, která umožňuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
