---
title: Monitorování aplikací v Service Fabric pomocí ELK v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nastavit ELK a monitorovat své aplikace Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 689207339db0250d42fc64c33f43c42c18317d41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388681"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Kurz: Monitorování aplikací Service Fabric pomocí ELK

Tento kurz je čtvrtou částí série. Ukazuje, jak pomocí ELK (Elasticsearch, Logstash a Kibana) monitorovat aplikace Service Fabric spuštěné v Azure.

Ve čtvrté části této série se naučíte:
> [!div class="checklist"]
> * Nastavení serveru ELK v Azure
> * Konfigurace Logstash pro příjem protokolů ze služby Event Hubs
> * Vizualizace protokolů platformy a aplikace v Kibana

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavení aplikace Service Fabric Reliable Services v Javě](service-fabric-tutorial-create-java-app.md)
> * [Nasazení a ladění aplikace v místním clusteru](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Nasazení aplikace do clusteru Azure](service-fabric-tutorial-java-deploy-azure.md)
> * Nastavit monitorování a diagnostiku aplikace
> * [Nastavení CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nastavte svou aplikaci tak, aby vysílala protokoly do umístění zadaného v [druhé části](service-fabric-tutorial-debug-log-local-cluster.md).
* Dokončete [třetí část](service-fabric-tutorial-java-deploy-azure.md), abyste měli spuštěný cluster Service Fabric nakonfigurovaný pro odesílání protokolů do služby Event Hubs.
* Potřebujete zásadu ve službě Event Hubs, která má oprávnění naslouchat a přidružený primární klíč ze třetí série.

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové aplikace Voting

Pokud jste nesestavili ukázkovou aplikaci Voting v [první části této série kurzů](service-fabric-tutorial-create-java-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Vytvoření serveru ELK v Azure

Pro účely tohoto kurzu můžete použít předkonfigurované prostředí ELK. Pokud už ho máte, přeskočte k části **Instalace Logstash**. Pokud ho však nemáte, v následujících krocích se v Azure vytvoří.

1. Vytvořte v Azure ELK s certifikací od [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6). Pro účely tohoto kurzu není nutné při vytváření tohoto serveru dodržovat žádné konkrétní specifikace.

2. Přejděte ke svému prostředku na webu Azure Portal a v části **Podpora a řešení potíží** přejděte na kartu **Diagnostika spouštění**. Pak klikněte na kartu **Sériový protokol**.

    ![Diagnostika spouštění](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. V protokolech vyhledejte heslo, které je potřeba pro přístup k instanci Kibana. Vypadá podobně jako následující fragment kódu:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Stisknutím tlačítka Připojit na stránce Přehled serveru na webu Azure Portal získejte podrobnosti o přihlášení.

    ![Připojení k virtuálnímu počítači](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Pomocí následujícího příkazu se připojte přes SSH k serveru, který je hostitelem image ELK.

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Nastavení ELK

1. Prvním krokem je načtení prostředí ELK.

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Pokud používáte stávající prostředí, je potřeba spuštěním následujícího příkazu zastavit službu Logstash.

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Spuštěním následujícího příkazu nainstalujte modul plug-in pro službu Event Hubs.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Vytvořit nebo upravit vaše stávající konfigurační soubor Logstash s následujícím obsahem: Při vytváření souboru musí být vytvořen v ```/opt/bitnami/logstash/conf/access-log.conf``` Pokud používáte image ELK od Bitnami v Azure.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Konfiguraci ověříte spuštěním následujícího příkazu:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Spusťte službu Logstash.

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Zkontrolujte Elasticsearch a ujistěte se, že přijímáte data.

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Přístup k řídicí panel Kibana na **http:\//SERVER-IP** a zadejte uživatelské jméno a heslo pro Kibana. Pokud jste použili image ELK v Azure, výchozí uživatelské jméno je user a heslo je to, které jste získali v části **Diagnostika spouštění**.

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zprovoznění serveru ELK v Azure
> * Konfigurace serveru pro příjem diagnostických informací z clusteru Service Fabric

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nastavení CI/CD s využitím Jenkinse](service-fabric-tutorial-java-jenkins.md)
