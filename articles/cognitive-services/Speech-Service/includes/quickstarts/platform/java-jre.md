---
title: 'Rychlý Start: sada Speech SDK pro Java (Windows, Linux, macOS) nastavení platformy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro používání Java (Windows, Linux, macOS) se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 142d4504ab12e7df5cc1e009038554a5b90dff0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188187"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro 64 JRE Java 8. Pokud chcete, aby název balíčku byl spuštěný sami, sada Java SDK není v centrálním úložišti Maven k dispozici. Bez ohledu na to, jestli používáte Gradle nebo `pom.xml` soubor závislosti, musíte přidat vlastní úložiště, na které odkazuje `https://csspeechstorage.blob.core.windows.net/maven/` (viz níže pro název balíčku).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček Java Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: jenom 64 bitů
  - Mac: macOS X verze 10,13 nebo novější
  - Linux Podívejte se na seznam [podporovaných distribucí a cílových architektur systému Linux](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Předpoklady

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Java IDE zatmění](https://www.eclipse.org/downloads/) (vyžaduje už nainstalovaný Java)
- Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny ( `libssl` pro podporu SSL (Secure Sockets Layer `libasound2` ) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

  - V Ubuntu/Debian spuštěním následujících příkazů nainstalujte požadované balíčky:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Pokud libssl 1.0.0 není k dispozici, nainstalujte libssl 1.0. x (kde x je větší než 0) nebo místo toho libssl 1.1.

  - V RHEL/CentOS spuštěním následujících příkazů nainstalujte požadované balíčky:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Vytvořit projekt zatmění a nainstalovat sadu Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
