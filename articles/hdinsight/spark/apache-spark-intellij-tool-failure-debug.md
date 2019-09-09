---
title: 'Selhání ladění úloh Sparku pomocí Azure Toolkit for IntelliJ (Preview) '
description: Doprovodné materiály k ladění aplikací pomocí nástrojů HDInsight v Azure Toolkit for IntelliJ
keywords: dálková ladění IntelliJ, vzdálené ladění IntelliJ, SSH, IntelliJ, HDInsight, ladění IntelliJ, ladění
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a07dcd58263674aa6fd360e138c0b9c999ea644e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814147"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Selhání ladění úloh Sparku pomocí Azure Toolkit for IntelliJ (Preview)

Tento článek poskytuje podrobné pokyny k používání nástrojů HDInsight v [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) ke spouštění aplikací pro **ladění chyb Spark** . 

## <a name="prerequisites"></a>Požadavky
* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). V tomto kurzu se používá Java verze 8.0.202.
  
* IntelliJ IDEA. V tomto článku [se používá IntelliJ nápad Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Viz [instalace Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Připojte se ke clusteru HDInsight. Viz [připojení ke clusteru HDInsight](apache-spark-intellij-tool-plugin.md).

* Průzkumník služby Microsoft Azure Storage. Viz [stažení Průzkumník služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Vytvoření projektu pomocí šablony ladění 

Vytvořte projekt Spark 2.3.2 pro pokračování v ladění selhání. v tomto dokumentu proveďte chybu ukázkového souboru ladění úlohy.

1. Otevřete IntelliJ IDEA. Otevřete okno **Nový projekt** .

   a. V levém podokně vyberte **Azure Spark/HDInsight** . 

   b. V hlavním okně vyberte **projekt Spark s úlohou selhání ukázka ladění (Preview) (Scala)** .

     ![Vytvořit ladicí projekt](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Vyberte **Další**.     
 
2. V okně **Nový projekt** proveďte následující kroky:

   ![Výběr sady Spark SDK](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V rozevíracím seznamu **sada SDK projektu** vyberte **Java 1,8** pro cluster **Spark 2.3.2** .

   c. V rozevíracím seznamu **verze Sparku** vyberte **Spark 2.3.2 (Scala 2.11.8)** .

   d. Vyberte **Finish** (Dokončit).

3. Vyberte **Src** > MainScala > a otevřete svůj kód v projektu. V tomto příkladu se používá skript **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spuštění aplikace Spark Scala/Java v clusteru HDInsight

Vytvořte aplikaci Spark Scala/Java a spusťte aplikaci v clusteru Spark pomocí následujících kroků:

1. Kliknutím na **Přidat konfiguraci** otevřete okno **Konfigurace spuštění nebo ladění** .

   ![Upravit konfigurace](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. V dialogovém okně **Spustit/ladit konfigurace** vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** .

   ![Přidat novou konfiguraci](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Přepněte na **vzdálené spuštění na kartě cluster** . Zadejte informace pro **název**, **cluster Spark**a **název hlavní třídy**. Naše nástroje podporují ladění pomocí **prováděcích**modulů. Výchozí hodnota **numExectors**je 5 a Vy byste lépe nestavili hodnotu vyšší než 3. Chcete-li zkrátit dobu běhu, můžete do **Konfigurace úlohy** přidat **Spark. příze. maxAppAttempts** a nastavit hodnotu na 1. Kliknutím na tlačítko **OK** konfiguraci uložíte.

   ![Spustit konfigurace ladění](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **Upravit konfigurace**. 

5. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru.
   
   ![Tlačítko pro vzdálené spuštění](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. ID aplikace můžete ověřit v okně výstup.
   
   ![Výsledek vzdáleného spuštění](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Neúspěšný profil úlohy stažení

Pokud se odeslání úlohy nepovede, můžete pro další ladění stáhnout profil neúspěšné úlohy do místního počítače.

1. Otevřete **Průzkumník služby Microsoft Azure Storage**, vyhledejte účet HDInsight clusteru pro neúspěšnou úlohu, Stáhněte neúspěšné prostředky úlohy z příslušného umístění: **\hdp\spark2-Events\\. Spark\\ – chyby.ID\<aplikace >** do místní složky. V okně **aktivity** se zobrazí průběh stahování.

   ![stažení chyby Soubor1](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![stažení chyby soubor2](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurace místního ladicího prostředí a ladění při selhání

1. Otevřete původní projekt nebo vytvořte nový projekt a přidružte jej k původnímu zdrojovému kódu. Pro ladění chyb aktuálně podporuje pouze verzi Spark 2.3.2.

2. V IntelliJ NÁPADu vytvořte konfigurační soubor **ladění chyby Sparku** , vyberte soubor FTD z dříve stažených zdrojů neúspěšných úloh pro pole **umístění kontextu selhání úlohy Spark** .
   
   ![Konfigurace selhání Crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Na panelu nástrojů klikněte na tlačítko místní spuštění. chyba se zobrazí v okně Spustit.
   
   ![spuštění – chyba – configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![spuštění – chyba – configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Nastavte bod přerušení, který označuje protokol, a pak klikněte na tlačítko místní ladění a proveďte místní ladění stejně jako normální projekty Scala/Java v IntelliJ.

5. Po ladění je možné, že pokud se projekt úspěšně dokončí, můžete znovu odeslat neúspěšnou úlohu do svého clusteru Spark v HDInsight.

## <a name="seealso"></a>Další kroky
* [Přehled Ladění aplikací Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Ukázka
* Vytvořit projekt Scala (video): [Vytváření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Analýza interaktivních dat pomocí Sparku ve službě HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k analýze teploty budovy pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používání nástrojů HDInsight pro IntelliJ s izolovaným prostorem Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
