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
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78924972"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro 64 JRE Java 8. Pokud chcete, aby název balíčku byl spuštěný sami, sada Java SDK není v centrálním úložišti Maven k dispozici. Bez ohledu na to, jestli používáte `pom.xml` Gradle nebo soubor závislosti, musíte přidat vlastní úložiště, na které `https://csspeechstorage.blob.core.windows.net/maven/` odkazuje (viz níže pro název balíčku).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček Java Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: jenom 64 bitů
  - Mac: macOS X verze 10,13 nebo novější
  - Linux: 64-bit pouze v Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Požadavky

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Java IDE zatmění](https://www.eclipse.org/downloads/) (vyžaduje už nainstalovaný Java)
- Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny`libssl` (pro podporu SSL (Secure `libasound2` Sockets Layer) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

  - V Ubuntu spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - V Debian 9 spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Na RHEL/CentOS 8 spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Vytvořit projekt zatmění a nainstalovat sadu Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
