---
title: Použití nástroje Docker Compose k nasazení několika kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nasadit více kontejnerů Cognitive Services. V tomto článku se dozvíte, jak orchestrovat více imagí kontejnerů Docker pomocí Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 54a2aac3db47d60f02a45adae9aaa6077d675a43
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716894"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Použití nástroje Docker Compose k nasazení několika kontejnerů

V tomto článku se dozvíte, jak nasadit více kontejnerů Azure Cognitive Services. Konkrétně se naučíte, jak používat Docker Compose k orchestraci více imagí kontejneru Docker.

> [Docker Compose](https://docs.docker.com/compose/) je nástroj pro definování a spouštění aplikací Docker pro více kontejnerů. V sestavách pomocí souboru YAML nakonfigurujete služby vaší aplikace. Pak můžete vytvořit a spustit všechny služby z konfigurace spuštěním jediného příkazu.

Může být užitečné pro orchestraci více imagí kontejneru v jednom hostitelském počítači. V tomto článku budeme vyžádat kontejnery pro Rozpoznávání textu a vyžádané formuláře.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* [Modul Docker](https://www.docker.com/products/docker-engine). Potvrďte, že rozhraní příkazového řádku Docker funguje v okně konzoly.
* Prostředek Azure se správnou cenovou úrovní. S tímto kontejnerem fungují jenom následující cenové úrovně:
  * **Počítačové zpracování obrazu** prostředek s pouze cenovou úrovní F0 nebo Standard.
  * Prostředek **pro rozpoznávání formulářů** s cenovou úrovní F0 nebo Standard.
  * **Cognitive Services** prostředku pomocí cenové úrovně S0

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Dokončete a odešlete [formulář žádosti o Cognitive Services kontejnerech řeči](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Soubor Docker Compose

Soubor YAML definuje všechny služby, které mají být nasazeny. Tyto služby spoléhají buď na `DockerFile`, nebo na stávající imagi kontejneru. V tomto případě použijeme dvě image ve verzi Preview. Zkopírujte a vložte následující soubor YAML a uložte ho jako *Docker-tváře. yaml*. Zadejte v souboru příslušné hodnoty **apikey**, **fakturace**a **hodnot endpointuri** .

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Vytvořte adresáře na hostitelském počítači, které jsou zadány pod uzlem **svazky** . Tento přístup je nutný, protože adresáře musí existovat předtím, než se pokusíte připojit bitovou kopii pomocí vazeb svazků.

## <a name="start-the-configured-docker-compose-services"></a>Spusťte nakonfigurované služby Docker Compose.

Soubor Docker Compose umožňuje správu všech fází v životním cyklu definované služby: spouštění, zastavování a obnovování služeb; zobrazení stavu služby; a streamování protokolů. Otevřete rozhraní příkazového řádku z adresáře projektu (kde se nachází soubor Docker-tváře. yaml).

> [!NOTE]
> Aby se předešlo chybám, ujistěte se, že hostitelský počítač správně sdílí jednotky s modulem Docker. Například pokud se E:\publicpreview používá jako adresář v souboru Docker-tváře. yaml, sdílejte jednotku E s Docker.

V rozhraní příkazového řádku spusťte následující příkaz, který spustí (nebo restartuje) všechny služby definované v souboru Docker-tváře. yaml:

```console
docker-compose up
```

První Docker spustí příkaz **Docker-sestavit** pomocí této konfigurace, načte image nakonfigurované v uzlu **služby** a pak je stáhne a připojí:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Po stažení imagí se služby image Services spustí:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Ověření dostupnosti služby

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Tady je příklad výstupu:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testování kontejneru Rozpoznávání textu

Otevřete **na** hostitelském počítači prohlížeč a v souboru Docker-YAML, jako je například http://localhost:5021/swagger/index.html, použijte zadaný port. K otestování Rozpoznávání textuho koncového bodu můžete použít funkci vyzkoušet ho v rozhraní API.

![Rozpoznávání textu kontejner](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Testování kontejneru pro rozpoznávání formulářů

Otevřete **na** hostitelském počítači prohlížeč a v souboru Docker-YAML, jako je například http://localhost:5010/swagger/index.html, použijte zadaný port. Pomocí funkce try it v rozhraní API můžete testovat koncový bod pro rozpoznávání formuláře.

![Kontejner pro rozpoznávání formulářů](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery Cognitive Services](../cognitive-services-container-support.md)
