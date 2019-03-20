---
title: Instalace Jupyteru místně a připojení ke Sparku v Azure HDInsight
description: Zjistěte, jak místně do počítače nainstalovat Poznámkový blok Jupyter a připojte ho ke clusteru Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 7b20f0ec4669b485f87d050fcf597244fb898c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091252"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Na váš počítač nainstalovat Poznámkový blok Jupyter a připojte se k Apache Spark v HDInsight

V tomto článku se dozvíte, jak instalace Poznámkový blok Jupyter s vlastní PySpark (pro jazyk Python) a Apache Sparku (pro Scala) jader s Spark magic a připojení ke clusteru HDInsight poznámkového bloku. Může existovat několik důvodů, proč instalace Jupyteru do místního počítače a může být také některé běžné problémy. Další informace o to, najdete v části [proč by instalace Jupyteru do počítače](#why-should-i-install-jupyter-on-my-computer) na konci tohoto článku.

Při instalaci Jupyter a připojení k Apache Spark v HDInsight jsou čtyři klíčové kroky.

* Konfigurace clusteru Spark.
* Instalace aplikace Jupyter notebook.
* Instalace jádra PySpark a Spark s kouzlem Spark.
* Nakonfigurujte magic Spark pro přístup ke clusteru Spark v HDInsight.

Další informace o jádrech vlastní a k dispozici pro poznámkové bloky Jupyter s clusterem HDInsight Spark magic, naleznete v tématu [jádra dostupná pro poznámkové bloky Jupyter s Apache Spark Linux clusterů v HDInsight](apache-spark-jupyter-notebook-kernels.md).

> [!IMPORTANT]  
> Kroky v následujícím článku fungovat jenom Spark verze 2.1.0.

## <a name="prerequisites"></a>Požadavky
Požadavky uvedené v tomto poli nejsou pro instalaci Jupyter. Jedná se o připojení Poznámkový blok Jupyter ke clusteru služby HDInsight po instalaci poznámkového bloku.

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark (verze 2.1.0 nebo nižší) v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).



## <a name="install-jupyter-notebook-on-your-computer"></a>Na váš počítač nainstalovat aplikace Jupyter notebook

Před instalací poznámkové bloky Jupyter, je nutné nainstalovat Python. Python a Jupyter je k dispozici jako součást [distribuce Anacondy](https://www.anaconda.com/download/). Při instalaci Anacondy nainstalujete distribuci jazyka Python. Po instalaci Anacondy přidáte instalace Jupyteru spuštěním příslušnými příkazy.

1. Stáhněte si [instalační program Anaconda](https://www.anaconda.com/download/) pro platformu a spusťte instalační program. Při spuštění Průvodce instalací, ujistěte se, zda že jste vybrali možnost Přidat Anaconda do proměnné PATH.

2. Spusťte následující příkaz k instalaci aplikace Jupyter.

        conda install jupyter

    Další informace o instalaci Jupyter, naleznete v tématu [instalace Jupyteru pomocí Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalace jádra a Spark magic

Pokyny o tom, jak nainstalovat Spark magic jádra PySpark a Spark, postupujte podle pokynů k instalaci v [sparkmagic dokumentaci](https://github.com/jupyter-incubator/sparkmagic#installation) na Githubu. Prvním krokem v dokumentaci magic Spark žádostí o instalaci Spark magic. Pomocí následujících příkazů nahraďte tento první krok v odkazu, v závislosti na verzi clusteru HDInsight se připojíte k. Potom postupujte podle pokynů v dokumentaci magic Spark. Pokud chcete nainstalovat jiný jádrech, je nutné provést krok 3 v části Spark magic instalační pokyny.

* Pro clustery v3.5 a v3.6 nainstalujte sparkmagic 0.11.2 spuštěním `pip install sparkmagic==0.11.2`

* Pro clustery verzi 3.4 nainstalujte sparkmagic 0.2.3 spuštěním `pip install sparkmagic==0.2.3`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurace magic Spark pro připojení ke clusteru HDInsight Spark

V této části nakonfigurujete Kouzlo Spark, který jste dříve nainstalovali pro připojení ke clusteru Apache Spark, který musí již jste vytvořili v Azure HDInsight.

1. Spusťte prostředí Python pomocí následujícího příkazu:

    ```
    python
    ```

2. Informace o konfiguraci Jupyter je obvykle uložen do domovského adresáře uživatele. Zadejte následující příkaz k identifikaci domovský adresář a vytvořte složku s názvem existuje **.sparkmagic**.  Úplná cesta bude výstupem.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Ve složce `.sparkmagic`, vytvořte soubor s názvem **config.json** a přidejte následující fragment kódu JSON dovnitř.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```
4. Proveďte následující změny do souboru:

    |Hodnoty v šabloně | Nová hodnota |
    |---|---|
    |{USERNAME}|Přihlášení ke clusteru, výchozí je správce.|
    |{NÁZEV_DNS_CLUSTERU}|Název clusteru|
    |{BASE64ENCODEDPASSWORD}|Kódováním Base 64 hesla pro vaše vlastní heslo.  Můžete vygenerovat heslo ve formátu base64 v [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Používáte-li zachovat `sparkmagic 0.11.23` (clusterů v3.5 a v3.6).  Pokud používáte `sparkmagic 0.2.3` (clusterů verzi 3.4), nahraďte `"should_heartbeat": true`.|

    Zobrazí se úplný ukázkový soubor v [ukázka config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Ujistěte se, že nedošlo k úniku relací jsou odesílány prezenční signály. Když počítač přejde do režimu spánku nebo je vypnutý, nejsou zasílány prezenčního signálu, což vede k relaci se vyčistí. Pro clustery verzi 3.4, pokud chcete toto chování zakázat, můžete nastavit konfiguraci Livy `livy.server.interactive.heartbeat.timeout` k `0` z uživatelského rozhraní Ambari. Pro clustery v3.5 Pokud nenastavíte výše, verze 3.5 Konfigurace relace se neodstraní.

5. Začněte Jupyter. Použijte následující příkaz z příkazového řádku.

        jupyter notebook

6. Ověřte, že můžete použít k dispozici Spark magic s jádrech. Proveďte následující kroky.

    a. Vytvořte nový poznámkový blok. V pravém rohu vyberte **nový**. Měli byste vidět výchozí jádra **Python 2** nebo **Python 3** a jádrech jste nainstalovali. Skutečné hodnoty se mohou lišit v závislosti na vybrané možnosti instalace.  Vyberte **PySpark**.

    ![Jádra v poznámkovém bloku Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "jádra v poznámkovém bloku Jupyter")

    > [!IMPORTANT]  
    > Po výběru **nový** zkontrolujte prostředí, aby všechny chyby.  Pokud se zobrazí chyba `TypeError: __init__() got an unexpected keyword argument 'io_loop'` může se jednat o známý problém s některými verzemi tornádu.  Pokud ano, zastavte jádra a poté downgradovat tornádu instalaci pomocí následujícího příkazu: `pip install tornado==4.5.3`.

    b. Spusťte následující fragment kódu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Pokud můžete úspěšně načíst výstup, je otestovat připojení ke clusteru HDInsight.

    Pokud chcete aktualizovat konfiguraci poznámkového bloku pro připojení do jiného clusteru, aktualizujte config.json s novou sadu hodnot, jak je uvedeno v kroku 3 výše.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Proč instalace Jupyteru do počítače?

Může existovat několik důvodů, proč můžete chtít nainstalovat do počítače Jupyter a připojte ho ke clusteru Apache Spark v HDInsight.

* I když poznámkové bloky Jupyter jsou už k dispozici pro cluster Spark v Azure HDInsight, instalace Jupyteru do počítače poskytuje možnost vytvořit místně vašich poznámkových bloků, test aplikace proti spuštěný cluster a pak nahrajte poznámkové bloky do clusteru. K nahrání poznámkových bloků do clusteru, můžete nahrát je pomocí poznámkového bloku Jupyter, na kterém běží nebo cluster nebo je uložit do složky /HdiNotebooks v účtu úložiště přidruženého clusteru. Další informace o tom, jak jsou poznámkové bloky uložené v clusteru, naleznete v tématu [umístění ukládaných poznámkové bloky Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* S poznámkovými bloky k dispozici místně, můžete připojit k jiné clustery Spark podle požadavků vašich aplikací.
* Githubu můžete implementovat systém správy zdrojového kódu a správy verzí pro poznámkových bloků. Je také možné prostředí vytvářené společnými silami místo, kde více uživatelů může pracovat s stejného poznámkového bloku.
* Můžete pracovat s poznámkovými bloky místně i bez clusteru. Potřebujete jenom cluster k testování vašich poznámkových bloků, nechcete spravovat ručně vašich poznámkových bloků nebo vývojové prostředí.
* Může být jednodušší konfigurovat místní vývojové prostředí, než je konfigurace instalace Jupyter v clusteru.  Můžete využít výhod veškerý software, který jste si nainstalovali místně bez konfigurace jeden nebo více vzdálených clusterů.

> [!WARNING]  
> S Jupyterem nainstalované na místním počítači poběží více uživatelů stejného poznámkového bloku na stejném clusteru Spark ve stejnou dobu. V takovém případě se vytvoří víc relací Livy. Pokud narazíte na problém a chcete ladit, který bude, že složité úlohy sledování relace Livy patří který uživatel.  

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
