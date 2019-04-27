---
title: Ladění aplikace Java v místním clusteru Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ladit aplikaci Service Fabric spuštěnou v místním clusteru a jak z ní získat protokoly.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
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
ms.openlocfilehash: c5ff1a0373fcce339bea2b235d86f20dc861a15c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224042"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Kurz: Ladění aplikace v Javě nasazené v místním clusteru Service Fabric

Tento kurz je druhá část série. Zjistíte, jak pro aplikaci Service Fabric připojit vzdálený ladicí program pomocí Eclipse. Kromě toho se dozvíte, jak přesměrovat protokoly ze spuštěných aplikací do umístění, které je vhodné pro vývojáře.

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavení aplikace Service Fabric Reliable Services v Javě](service-fabric-tutorial-create-java-app.md)
> * Nasazení a ladění aplikace v místním clusteru
> * [Nasazení aplikace do clusteru Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-java-elk.md)
> * [Nastavení CI/CD](service-fabric-tutorial-java-jenkins.md)


Ve druhé části této série se naučíte:
> [!div class="checklist"]
> * Ladění aplikace v Javě pomocí Eclipse
> * Přesměrování protokolů do konfigurovatelného umístění


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Nastavte vývojové prostředí pro [Mac](service-fabric-get-started-mac.md) nebo [Linux](service-fabric-get-started-linux.md). Postupujte podle pokynů k instalaci modulu plug-in Eclipse, Gradle, sady Service Fabric SDK a Service Fabric CLI (sfctl).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové aplikace Voting

Pokud jste nesestavili ukázkovou aplikaci Voting v [první části této série kurzů](service-fabric-tutorial-create-java-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Sestavování a nasazování](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) aplikaci do místního vývojového clusteru.

## <a name="debug-java-application-using-eclipse"></a>Ladění aplikace v Javě pomocí Eclipse

1. Otevřete na svém počítači integrované vývojové prostředí (IDE) Eclipse a klikněte na **File –> Import...** (Soubor –> Importovat...).

2. V automaticky otevřeném okně vyberte možnost **General –> Existing Projects into Workspace** (Obecné –> Stávají projekty do pracovního prostoru) a stiskněte Next (Další).

3. V okně Import Projects (Importovat projekty) zvolte možnost **Select root directory** (Vybrat kořenový adresář) a vyberte adresář **Voting**. Pokud jste postupovali podle první části této série kurzů, adresář **Voting** je v adresáři **Eclipse-workspace**.

4. Aktualizujte soubor entryPoint.sh služby, kterou chcete ladit, aby se proces Javy spouštěl s parametry vzdáleného ladění. Pro účely tohoto kurzu se používá bezstavový front-end: *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. V tomto příkladu je pro ladění nastavený port 8001.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Aktualizujte manifest aplikace a nastavte počet instancí nebo replik pro laděnou službu na 1. Toto nastavení zabrání konfliktům na portu, který slouží k ladění. Například pro bezstavové služby nastavte ``InstanceCount="1"`` a pro stavové služby nastavte cílovou a minimální velikost sady replik na 1 následujícím způsobem: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. V integrovaném vývojovém prostředí (IDE) Eclipse vyberte **Run –> Debug Configurations –> Remote Java Application** (Spustit –> Konfigurace ladění –> Vzdálená aplikace v Javě), stiskněte tlačítko **New** (Nový), nastavte vlastnosti následujícím způsobem a klikněte na **Apply** (Použít).

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Přidejte zarážku na řádek 109 souboru *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*.

8. V průzkumníku balíčků klikněte pravým tlačítkem na projekt **Voting** a pak klikněte na **Service Fabric –> Publish Application...** (Service Fabric –> Publikovat aplikaci...).

9. V okně **Publish Application** (Publikovat aplikaci) vyberte v rozevíracím seznamu soubor **Local.json** a klikněte na **Publish** (Publikovat).

10. V integrovaném vývojovém prostředí (IDE) Eclipse vyberte **Run –> Debug Configurations –> Remote Java Application** (Spustit –> Konfigurace ladění –> Vzdálená aplikace v Javě), klikněte na konfiguraci **Voting**, kterou jste vytvořili, a pak klikněte na **Debug** (Ladit).

11. Přejděte na webový prohlížeč a přístup **localhost: 8080**. To se automaticky zarážce a Eclipse přejde **ladění perspektivy**.

Teď můžete použít stejné kroky, chcete-li ladit libovolné aplikace Service Fabric v Eclipse.

## <a name="redirect-application-logs-to-custom-location"></a>Přesměrování protokolů aplikací do vlastního umístění

Následující kroky vás provedou přesměrováním protokolů aplikací z výchozího umístění */var/log/syslog* do vlastního umístění.

1. V současné době aplikace běžící v pouze clustery Service Fabric s Linuxem podporují sběr jednoho souboru protokolu. Chcete-li nastavit aplikaci tak, aby protokoly vždy putují do */tmp/mysfapp0.0.log*, vytvořte soubor Logging.Properties v následujícím umístění *Voting/VotingApplication/VotingWebPkg/Code/logging.properties*  a přidejte následující obsah.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Do souboru *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* přidejte následující parametr pro příkaz ke spuštění Javy:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    Následující příklad ukazuje ukázkové spuštění s ladicím programem připojené, provedení podobně jako v předchozí části.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

V této fázi už víte, jak při vývoji aplikací Service Fabric v Javě ladit protokoly aplikací a jak k nim přistupovat.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Ladění aplikace v Javě pomocí Eclipse
> * Přesměrování protokolů do konfigurovatelného umístění

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nasazení aplikace do Azure](service-fabric-tutorial-java-deploy-azure.md)