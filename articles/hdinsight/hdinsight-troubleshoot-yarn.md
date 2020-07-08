---
title: Řešení potíží s VLÁKNy v Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s Apache Hadoopmi NITĚmi a Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79272198"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hadoop YARN pomocí služby Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešení při práci s Apache Hadoopmi částmi PŘÍZe v Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Návody vytvořit novou frontu PŘÍZ na clusteru?

### <a name="resolution-steps"></a>Postup řešení

Pomocí následujících kroků v Ambari vytvořte novou frontu PŘÍZe a pak vyvážit přidělení kapacity mezi všemi frontami.

V tomto příkladu se obě existující fronty (**výchozí** a **thriftsvr**) změnily z 50% kapacity na 25% kapacity, což dává novou kapacitu queue (Spark) 50%.

| Fronta | Kapacita | Maximální kapacita |
| --- | --- | --- |
| default | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| Spark | 50 % | 50 % |

1. Vyberte ikonu **zobrazení Ambari** a pak vyberte vzor mřížky. V dalším kroku vyberte možnost **správce front příz**.

    ![Správce fronty PŘÍZe na řídicím panelu Apache Ambari](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Vyberte **výchozí** frontu.

    ![Apache Ambari nitě vybrat výchozí frontu](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. U **výchozí** fronty změňte **kapacitu** z 50% na 25%. V případě fronty **thriftsvr** změňte **kapacitu** na 25%.

    ![Změňte kapacitu na 25% pro výchozí a thriftsvr fronty.](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Pokud chcete vytvořit novou frontu, vyberte **Přidat frontu**.

    ![Přidat frontu pro Apache Ambari nitě řídicí panel](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Pojmenujte novou frontu.

    ![Fronta názvů Ambari PŘÍZového řídicího panelu Apache](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Hodnoty **kapacity** ponechte na 50% a pak vyberte tlačítko **Akce** .

    ![Akce výběru nitě Apache Ambari](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Vyberte **Uložit a aktualizovat fronty**.

    ![Výběr uložit a aktualizovat fronty](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Tyto změny jsou okamžitě viditelné v uživatelském rozhraní plánovače PŘÍZe.

### <a name="additional-reading"></a>Další čtení

- [Apache Hadoop CapacityScheduler PŘÍZe](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Návody stahovat z clusteru protokoly PŘÍZe?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [Další](#additional-reading-2)informace o čtení.

1. Chcete-li zobrazit seznam všech ID aplikací pro aplikace PŘÍZ, které jsou aktuálně spuštěny, spusťte následující příkaz:

    ```apache
    yarn top
    ```

    ID jsou uvedena ve sloupci **APPLICATIONID** . Můžete stáhnout protokoly ze sloupce **APPLICATIONID** .

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Chcete-li stáhnout protokoly kontejneru PŘÍZ pro všechny hlavní aplikační servery, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem amlogs.txt.

1. Chcete-li stáhnout protokoly kontejneru PŘÍZ pouze pro nejnovější hlavní aplikaci, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem latestamlogs.txt.

1. Chcete-li stáhnout protokoly kontejneru PŘÍZe pro první dva hlavní aplikační servery, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem first2amlogs.txt.

1. Chcete-li stáhnout všechny protokoly kontejneru PŘÍZe, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem logs.txt.

1. Chcete-li stáhnout protokol kontejneru PŘÍZ pro konkrétní kontejner, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Další čtení

- [Připojení k HDInsight (Apache Hadoop) pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop koncepce a aplikace PŘÍZe](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
