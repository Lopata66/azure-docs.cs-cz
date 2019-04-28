---
title: Omezení přístupu pomocí sdílených přístupových podpisů – Azure HDInsight
description: Další informace o použití sdílených přístupových podpisů omezit HDInsight přístup k datům uloženým v objektech BLOB Azure storage.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 7fa46e3a5f0ed6504e4bc927caa0378d75fcc4a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763392"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Omezení přístupu k datům v HDInsight pomocí Azure Storage sdílených přístupových podpisů

HDInsight má úplný přístup k datům v Azure Storage účty přidružené ke clusteru. Sdílené přístupové podpisy v kontejneru objektů blob můžete použít k omezení přístupu k datům. Sdílených přístupových podpisů (SAS) jsou funkce účty úložiště Azure, která vám umožní omezit přístup k datům. Například poskytuje přístup jen pro čtení k datům.

> [!IMPORTANT]  
> Pro řešení, které využívá Apache Ranger zvažte použití HDInsight připojených k doméně. Další informace najdete v tématu [konfigurace HDInsight připojených k doméně](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]  
> HDInsight musí mít úplný přístup k výchozím úložištěm clusteru.

## <a name="requirements"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Předplatné Azure
* C# nebo Python. Příklad kódu jazyka C# je k dispozici jako řešení sady Visual Studio.

  * Visual Studio musí být ve verzi 2013, 2015 nebo 2017
  * Python musí být verze 2.7 nebo novější

* Cluster HDInsight se systémem Linux nebo [prostředí Azure PowerShell] [ powershell] – Pokud máte existující cluster založených na Linuxu, vám pomůže Apache Ambari do clusteru přidat sdílený přístupový podpis. Pokud ne, můžete použít Azure PowerShell k vytvoření clusteru a přidejte sdílený přístupový podpis při vytváření clusteru.

    > [!IMPORTANT]  
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* V příkladu souborů z [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Toto úložiště obsahuje následující položky:

  * Projekt sady Visual Studio, můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití se službou HDInsight
  * Skript v jazyce Python, můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití se službou HDInsight
  * Skript prostředí PowerShell, který můžete vytvořit HDInsight cluster a nakonfigurujte ho na používání sdíleného přístupového podpisu.

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Existují dvě formy sdílené přístupové podpisy:

* Ad hoc: Čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadané v identifikátoru URI SAS.

* Uložené zásady přístupu: Uložené zásady přístupu je definován na kontejner prostředku, jako je například kontejner objektů blob. Zásadu lze použít ke správě omezení pro jeden nebo více sdílených přístupových podpisů. Když přiřadíte SAS uložené zásady přístupu, dědí SAS omezení – čas zahájení, čas vypršení platnosti a oprávnění – definice zásady přístupu.

Rozdíl mezi dvě různými formami je důležité pro jeden klíč scénář: odvolání. SAS je adresa URL, takže každý, kdo získává sdíleného přístupového podpisu, použít bez ohledu na to, který je požadovaný začneme. Pokud SAS je publikována veřejně, je možné použít kdokoli na světě. SAS, která se distribuuje je platné, dokud jednu ze čtyř akcí se stane:

1. Je dosaženo času vypršení platnosti zadané v protokolu SAS.

2. Je dosaženo času vypršení platnosti zadané v zásadách přístupu uložené odkazuje SAS. Čas vypršení platnosti tím dosáhnout způsobit, že následující scénáře:

    * Časový interval uplynul.
    * Zásady přístupu uložené upraví tak mají čas vypršení platnosti v minulosti. Změna čas vypršení platnosti je jeden způsob odvolání SAS.

3. Zásady přístupu uložené odkazuje sdíleného přístupového podpisu je odstranit, což je jiný způsob odvolání SAS. Pokud znovu vytvoříte zásady přístupu se stejným názvem, všechny tokeny SAS pro předchozí zásady jsou platné (pokud nebyl předán čas vypršení platnosti na sdíleného přístupového podpisu). Pokud máte v úmyslu odvolání SAS, je nutné použít jiný název, pokud znovu vytvoříte zásady přístupu s času vypršení platnosti v budoucnu.

4. Klíč účtu, který byl použit k vytvoření sdíleného přístupového podpisu se znova vygeneroval. Obnovuje se klíč způsobí, že všechny aplikace, které používají předchozí klíč vč. ověřování. Aktualizujte všechny komponenty na nový klíč.

> [!IMPORTANT]  
> Identifikátor URI sdíleného přístupového podpisu je přidružená ke klíči účet použitý k vytvoření podpisu a přidruženého uložené zásady přístupu (pokud existuje). Pokud není zadána žádná uložené zásady přístupu, chcete-li změnit klíč účtu je jediný způsob, jak odebrat sdílený přístupový podpis.

Doporučujeme vždy používat uložené zásady přístupu. Při použití uložené zásady, můžete odvolat podpisy nebo prodloužit datum vypršení platnosti podle potřeby. Kroky v tomto dokumentu využívají uložené zásady přístupu k vygenerování SAS.

Další informace o sdílených přístupových podpisů najdete v tématu [vysvětlení modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Vytvoření uložené zásady a SAS pomocí jazyka C\#

1. Otevřete řešení v sadě Visual Studio.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na **SASToken** projektu a vyberte **vlastnosti**.

3. Vyberte **nastavení** a přidejte hodnoty pro následující položky:

   * StorageConnectionString: Připojovací řetězec pro účet úložiště, který chcete vytvořit SAS pro a uložené zásady. Formát by měl být `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` kde `myaccount` je název vašeho účtu úložiště a `mykey` je klíč pro účet úložiště.

   * ContainerName: Kontejner v účtu úložiště, který chcete omezit přístup k datům.

   * SASPolicyName: Název, který se má použít pro uložené zásady k vytvoření.

   * FileToUpload: Cesta k souboru, který se nahraje do kontejneru.

4. Spusťte projekt. Po vygenerování sdíleného přístupového podpisu, zobrazí se informace podobné následujícímu textu:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Uložte zásadu token SAS, název účtu úložiště a název kontejneru. Když přidružíte účet úložiště pro HDInsight cluster se používají tyto hodnoty.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Vytvoření uložené zásady a SAS pomocí Pythonu

1. Otevřete soubor SASToken.py a změňte následující hodnoty:

   * zásady\_název: Název, který se má použít pro uložené zásady k vytvoření.

   * úložiště\_účet\_název: Název účtu úložiště.

   * úložiště\_účet\_klíč: Klíč pro účet úložiště.

   * úložiště\_kontejneru\_název: Kontejner v účtu úložiště, který chcete omezit přístup k datům.

   * Příklad\_souboru\_cesta: Cesta k souboru, který se nahraje do kontejneru.

2. Spusťte skript. Po dokončení skriptu zobrazí token SAS, který je podobný následujícímu textu:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Uložte zásadu token SAS, název účtu úložiště a název kontejneru. Když přidružíte účet úložiště pro HDInsight cluster se používají tyto hodnoty.

## <a name="use-the-sas-with-hdinsight"></a>Použití SAS s HDInsight

Při vytváření clusteru HDInsight, musíte zadat účet primárního úložiště a volitelně můžete zadat další účty úložiště. Obě tyto metody pro přidání úložiště vyžadují plný přístup k účtům úložiště a kontejnerů, které se používají.

Pokud chcete omezit přístup ke kontejneru pomocí sdíleného přístupového podpisu, přidejte vlastní položku a **základního webu** konfiguraci clusteru.

* Pro **založené na Windows** nebo **založených na Linuxu** clusterů HDInsight, můžete přidat položky při vytváření clusteru pomocí Powershellu.
* Pro **založených na Linuxu** clusterů HDInsight, změňte konfiguraci po vytvoření clusteru pomocí nástroje Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Vytvořit cluster, který používá sdíleného přístupového podpisu

Příklad vytvoření clusteru služby HDInsight, který používá sdíleného přístupového podpisu je součástí `CreateCluster` adresáři úložiště. Jeho použití, postupujte následovně:

1. Otevřít `CreateCluster\HDInsightSAS.ps1` souboru v textovém editoru a upravte následující hodnoty na začátku dokumentu.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Například změnit `'mycluster'` na název clusteru, kterou chcete vytvořit. Hodnoty SAS by měl odpovídat hodnotám z předchozího postupu, při vytváření účtu úložiště a tokenu SAS.

    Po změně hodnoty uložte soubor.

2. Otevření nového příkazového řádku Azure PowerShell. Pokud nejste obeznámeni s Azure Powershellu nebo ho ještě nenainstalovali, přečtěte si téma [nainstalovat a nakonfigurovat Azure PowerShell][powershell].

1. Z příkazového řádku použijte následující příkaz k ověření ke svému předplatnému Azure:

    ```powershell
    Connect-AzAccount
    ```

    Po zobrazení výzvy se přihlaste pomocí účtu pro vašeho předplatného Azure.

    Pokud je váš účet přidružený k více předplatným Azure, budete možná muset použít `Select-AzSubscription` vyberte předplatné, které chcete použít.

4. V příkazovém řádku přejděte do adresáře `CreateCluster` adresář obsahující soubor HDInsightSAS.ps1. Potom použijte následující příkaz pro spuštění skriptu

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Po spuštění skriptu, protokoluje výstup do příkazového řádku Powershellu při vytváření prostředku účty skupiny a úložiště. Zobrazí se výzva k zadání uživatel HTTP pro HDInsight cluster. Tento účet slouží k zabezpečení přístupu k protokolu HTTP/s na clusteru.

    Při vytváření clusteru se systémem Linux, zobrazí se výzva pro název uživatelského účtu SSH a heslo. Tento účet se používá pro vzdálené přihlášení ke clusteru.

   > [!IMPORTANT]  
   > Po zobrazení výzvy pro HTTP/s nebo SSH uživatelské jméno a heslo, musí se zadat heslo, které splňují následující kritéria:
   >
   > * Musí být minimálně 10 znaků.
   > * Musí obsahovat alespoň jednu číslici.
   > * Musí obsahovat alespoň jeden jiný než alfanumerický znak.
   > * Musí obsahovat aspoň jedno velké nebo malé písmeno.

To chvíli trvat, než tento skript k dokončení, obvykle přibližně 15 minut. Po dokončení skriptu bez chyb, cluster se vytvořil.

### <a name="use-the-sas-with-an-existing-cluster"></a>Použití SAS s existujícím clusteru

Pokud máte existující cluster založených na Linuxu, můžete přidat SAS **základního webu** konfigurace pomocí následujících kroků:

1. Otevřete webové uživatelské rozhraní Ambari pro váš cluster. Adresa pro tuto stránku https://YOURCLUSTERNAME.azurehdinsight.net. Po zobrazení výzvy ověřování ke clusteru pomocí jméno správce (správce) a heslo, které jste použili při vytváření clusteru.

2. V levé části webové uživatelské rozhraní Ambari, vyberte **HDFS** a pak vyberte **Configs** kartu uprostřed stránky.

3. Vyberte **Upřesnit** kartu a potom vyhledejte **základního webu vlastní** oddílu.

4. Rozbalte **základního webu vlastní** část a pak přejděte k ukončení a vyberte **přidat vlastnost...**  odkaz. Použijte následující hodnoty **klíč** a **hodnotu** pole:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Hodnota**: Vrácený podpis C# nebo jste dříve spustili aplikaci v Pythonu

     Nahraďte **CONTAINERNAME** s názvem kontejneru, který jste použili aplikaci C# nebo SAS. Nahraďte **STORAGEACCOUNTNAME** názvem účtu úložiště můžete použít.

5. Klikněte na tlačítko **přidat** tlačítko Uložit tento klíč a hodnotu a pak klikněte na tlačítko **Uložit** tlačítko Uložit změny konfigurace. Po zobrazení výzvy, přidejte popis změny ("Přidání přístup k úložišti SAS" příklad) a potom klikněte na **Uložit**.

    Klikněte na tlačítko **OK** kdy byly změny dokončeny.

   > [!IMPORTANT]  
   > Změna se projeví až po restartování několik služeb.

6. Webové uživatelské rozhraní Ambari, vyberte **HDFS** ze seznamu na levé straně a pak vyberte **restartujte všechny ovlivněné** z **akce služby** rozevírací seznam na pravé straně. Po zobrazení výzvy vyberte __potvrďte restartování všech__.

    Tento postup opakujte pro MapReduce2 a YARN.

7. Po restartování služby, vyberte každé z nich a zakázat režim údržby z **akce služby** rozevírací seznam.

## <a name="test-restricted-access"></a>Test s omezením pomocí specifikátoru přístupu

Pokud chcete ověřit, že mají omezený přístup, připojte se ke clusteru pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení ke clusteru, postupujte následovně Chcete-li ověřit, že je možné pouze pro čtení a seznam položek v účtu úložiště SAS:

1. Pro vypsání obsahu kontejneru, použijte následující příkaz z příkazového řádku: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Nahraďte **SASCONTAINER** s názvem kontejneru, který vytvořili pro účet úložiště SAS. Nahraďte **SASACCOUNTNAME** s názvem účet úložiště používané pro SAS.

    Seznam obsahuje soubor, který nahráli při vytvoření kontejneru a SAS.

2. Použijte následující příkaz k ověření, že si můžete přečíst obsah souboru. Nahradit **SASCONTAINER** a **SASACCOUNTNAME** jako v předchozím kroku. Nahraďte **FILENAME** s názvem souboru zobrazeného v předchozím příkazem:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Tento příkaz vypíše obsah souboru.

3. Stáhněte soubor do místního systému souborů použijte následující příkaz:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Tento příkaz stáhne soubor do místního souboru s názvem **testfile.txt**.

4. Použijte následující příkaz k nahrání místního souboru do nového souboru s názvem **testupload.txt** na úložiště SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zobrazí zpráva podobná následující text:

        put: java.io.IOException

    K této chybě dochází, protože je čtení + seznam pouze umístění úložiště. Umístění dat na výchozí úložiště pro cluster, který se může zapisovat použijte následující příkaz:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Tentokrát by měl úspěšně dokončit operaci.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="a-task-was-canceled"></a>Úloha byla zrušena

**Příznaky**: Při vytváření clusteru pomocí skriptu prostředí PowerShell, může se zobrazit následující chybová zpráva:

    New-AzHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Příčina:** K této chybě může dojít, pokud uživatele SSH můžete použít heslo uživatele správce nebo HTTP pro cluster nebo (u clusterů se systémem Linux).

**Rozlišení**: Použijte heslo, které splňuje následující kritéria:

* Musí být minimálně 10 znaků.
* Musí obsahovat alespoň jednu číslici.
* Musí obsahovat alespoň jeden jiný než alfanumerický znak.
* Musí obsahovat aspoň jedno velké nebo malé písmeno.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak přidat úložiště omezený přístup ke svému clusteru HDInsight, přečtěte si další způsoby, jak pracovat s daty ve vašem clusteru:

* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
