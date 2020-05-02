---
title: Instalace funkcí Speech Containers – Speech Service
titleSuffix: Azure Cognitive Services
description: Instalace a spuštění kontejnerů řeči. Převod řeči na text transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Převod textu na řeč převede vstupní text na syntetizované řeč podobné člověku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 53110875596830f63a946c1263bdbff3711366bf
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653155"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalace a spuštění kontejnerů služby Speech (verze Preview)

Kontejnery umožňují spouštět některá rozhraní API služby Speech Service ve vašem vlastním prostředí. Kontejnery jsou skvělé pro specifické požadavky zabezpečení a zásad správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner řeči.

Kontejnery řeči umožňují zákazníkům vytvořit architekturu aplikace pro rozpoznávání řeči, která je optimalizována pro možnosti robustního cloudu i pro celou hranici. K dispozici jsou čtyři různé kontejnery. Dva standardní kontejnery jsou **Převod řeči na text** a **Převod textu na řeč**. Mezi dva vlastní kontejnery patří **Custom Speech** textu a **vlastní převod textu na řeč**. Kontejnery řeči mají stejné [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) jako cloudové služby Azure Speech.

> [!IMPORTANT]
> Všechny kontejnery řeči jsou aktuálně nabízeny jako součást [veřejné "gated" verze Preview](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Oznámení se provede, když se v kontejnerech rozpoznávání řeči dosáhne všeobecné dostupnosti (GA).

| Funkce | Funkce | Latest (Nejnovější) |
|--|--|--|
| Převod řeči na text | Analyzuje mínění a transcribes nepřetržité zvukové nahrávky v reálném čase s využitím mezilehlého výsledku.  | 2.2.0 |
| Custom Speech na text | Pomocí vlastního modelu z [Custom Speechového portálu](https://speech.microsoft.com/customspeech)transcribes hlasové nahrávky v reálném čase nebo zvukové nahrávky do textu s mezilehlé výsledky. | 2.1.1 |
| Převod textu na řeč | Převede text na přirozený zvuk řeči pomocí prostého textu nebo jazyka SSML (Speech syntézy). | 1.3.0 |
| Vlastní převod textu na řeč | Pomocí vlastního modelu z [vlastního hlasového portálu](https://aka.ms/custom-voice-portal)převede převod textu na přirozený zvuk hlasu pomocí formátu prostého textu nebo jazyka SSML (Speech syntézy). | 1.3.0 |

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů řeči je nutné splnit následující předpoklady:

| Požaduje se | Účel |
|--|--|
| Docker Engine | Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br> |
| Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů. |
| Prostředek řeči | Aby bylo možné tyto kontejnery použít, je nutné mít následující:<br><br>Prostředek Azure _Speech_ pro získání přidruženého klíče rozhraní API a identifikátoru URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách s přehledem a klíči pro Azure Portal **řeči** . Oba jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce **Přehled** |

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Pokud chcete požádat o přístup ke kontejneru, vyplňte a odešlete [formulář žádosti o Cognitive Services kontejnerech řeči](https://aka.ms/speechcontainerspreview/) . 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Rozšířená podpora rozšíření Vector

**Hostitel** je počítač, který spouští kontejner Docker. Hostitel *musí podporovat* [Rozšířená rozšíření Vector](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Podporu AVX2 pro hostitele se systémem Linux můžete vyhledat pomocí následujícího příkazu:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> K podpoře AVX2 se *vyžaduje* hostitelský počítač. *Kontejner nebude* správně fungovat bez podpory AVX2.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner řeči.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Převod řeči na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Custom Speech na text | 2 jádra, 2 GB paměti | 4 jádra, 4 GB paměti |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
| Vlastní převod textu na řeč | 1 jádro, 2 GB paměti | 2 jádra, 3 GB paměti |

***

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají nastavení `--cpus` a `--memory` , která se používají jako součást `docker run` příkazu.

> [!NOTE]
> Minimum a doporučené jsou založeny na omezeních Docker, *nikoli* na prostředky hostitelského počítače. Například kontejnery Speech-to-text namapují část velkých jazykových modelů a *doporučuje* se, aby se celý soubor vešel do paměti, což je dalších 4-6 GB. První spuštění obou kontejnerů může trvat delší dobu, protože modely jsou stránkované v paměti.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

Obrázky kontejneru pro řeč jsou k dispozici v následujících Container Registry.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

| Kontejner | Repository |
|-----------|------------|
| Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

| Kontejner | Repository |
|-----------|------------|
| Custom Speech na text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

| Kontejner | Repository |
|-----------|------------|
| Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

| Kontejner | Repository |
|-----------|------------|
| Vlastní převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Vyžádání obsahu Docker pro kontejnery řeči

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Funkce Docker Pull pro kontejner převodu řeči na text

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru služby Container Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest` Značka vyžádá `en-US` národní prostředí. Pro další národní prostředí viz [národní prostředí pro převod řeči na text](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Národní prostředí pro převod textu na text

Všechny značky s výjimkou `latest` jsou v následujícím formátu a rozlišují velká a malá písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Následující značka je příkladem formátu:

```
2.2.0-amd64-en-us-preview
```

Pro všechna podporovaná národní prostředí kontejneru **převodů řeči** na text se podívejte na [tagy pro obrázky typu převod řeči na text](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Vyžádání obsahu Docker pro kontejner Custom Speech-to-text

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru služby Container Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Vlastní `locale` kontejnery `voice` řeči a jsou určeny vlastním modelem ingestované kontejnerem.

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Vyžádání obsahu Docker pro kontejner převodu textu na řeč

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru služby Container Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` Značka vyžádá `en-US` národní prostředí a `jessarus` hlas. Pro další národní prostředí viz [národní prostředí pro převod textu na mluvené slovo](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Národní prostředí pro převod textu na řeč

Všechny značky s výjimkou `latest` jsou v následujícím formátu a rozlišují velká a malá písmena:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Následující značka je příkladem formátu:

```
1.3.0-amd64-en-us-jessarus-preview
```

U všech podporovaných národních prostředí a odpovídajících hlasů kontejneru **textu na řeč** se podívejte na [značky obrázku pro převod textu na mluvené slovo](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Při vytváření standardního příspěvku http *pro převod textu na řeč* vyžaduje zpráva [SSML (Speech promarkup Language)](speech-synthesis-markup.md) `voice` prvek s `name` atributem. Hodnota je odpovídající národní prostředí a hlas kontejneru, označovaný také jako ["krátký název"](language-support.md#standard-voices). Například `latest` značka by měla název hlasu `en-US-JessaRUS`.

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Vyžádání obsahu Docker pro vlastní kontejner převodu textu na řeč

Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) Stáhněte image kontejneru z registru služby Container Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Vlastní `locale` kontejnery `voice` řeči a jsou určeny vlastním modelem ingestované kontejnerem.

***

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K [examples](speech-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty `{Endpoint_URI}` a `{API_Key}` , najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) . K [examples](speech-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` také další příklady příkazu.

# <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

Chcete-li spustit kontejner převodu *řeči na text* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spouští kontejner *řeči na text* z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analýza mínění na výstup řeči na text 

Počínaje v v 2.2.0 kontejneru převodu řeči na text můžete zavolat [rozhraní mínění Analysis V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) na výstup. K volání analýzy mínění budete potřebovat koncový bod prostředku rozhraní API pro analýzu textu. Příklad: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Pokud máte přístup ke koncovému bodu služby Text Analytics v cloudu, budete potřebovat klíč. Pokud používáte Analýza textu místně, možná ho nebudete muset zadávat.

Klíč a koncový bod jsou předány do kontejneru řeči jako argumenty, jak je uvedeno v následujícím příkladu.

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Tento příkaz:

* Provede stejné kroky jako v příkazu výše.
* Ukládá rozhraní API pro analýzu textu koncový bod a klíč pro posílání žádostí o analýzu mínění. 


# <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

Kontejner *Custom Speech-to-text* spoléhá na vlastní model řeči. Vlastní model se musí [vyškolet](how-to-custom-speech-train-model.md) pomocí [vlastního portálu pro rozpoznávání řeči](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Model Custom Speech musí být vyškolený z jedné z následujících verzí modelů:
> * **20181201 (sjednocené v 3.3)**
> * **20190520 (v 4.14 Unified)**
> * **20190701 (v 4.17 Unified)**<br>
> ![Custom Speech model kontejneru vlaků](media/custom-speech/custom-speech-train-model-container-scoped.png)

Pro spuštění kontejneru je vyžadováno **ID vlastního modelu** řeči. Najdete ho na stránce **školení** na vlastním portálu pro rozpoznávání řeči. Z vlastního portálu pro rozpoznávání řeči přejděte na stránku **školení** a vyberte model.
<br>

![Stránka s vlastním školením pro rozpoznávání řeči](media/custom-speech/custom-speech-model-training.png)

Získejte **ID modelu** , které chcete použít jako argument `ModelId` parametru `docker run` příkazu.
<br>

![Podrobnosti o vlastním modelu řeči](media/custom-speech/custom-speech-model-details.png)

Následující tabulka představuje různé `docker run` parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače, které Docker používá k trvalému uložení vlastního modelu. Například *C:\CustomSpeech* , kde se *jednotka C* nachází na hostitelském počítači. |
| `{MODEL_ID}` | Custom Speech **ID modelu** ze stránky **školení** na vlastním portálu pro rozpoznávání řeči. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Klíč rozhraní API je povinný. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit kontejner *Custom Speech-text* , spusťte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *Custom Speech-to-text* z image kontejneru.
* Přiděluje 4 jádra procesoru a 4 gigabajty (GB) paměti.
* Načte model *Custom Speech-to-text* ze vstupního připojení svazku, například *C:\CustomSpeech*.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Stáhne model na základě `ModelId` (pokud nebyl nalezen v připojení svazku).
* Pokud se vlastní model stáhl dříve, `ModelId` ignoruje se.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tts)

Chcete-li spustit kontejner převodu *textu na řeč* , spusťte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner převodu *textu na řeč* z image kontejneru.
* Přiděluje 2 jádra procesoru a 1 gigabajt (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

Vlastní kontejner převodu *textu na řeč* spoléhá na vlastní hlasový model. Vlastní model se musí [vyškolet](how-to-custom-voice-create-voice.md) pomocí [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Pro spuštění kontejneru je vyžadováno **ID vlastního modelu** hlasu. Najdete ho na stránce **školení** na vlastním hlasovém portálu. Z vlastního hlasového portálu přejděte na stránku **školení** a vyberte model.
<br>

![Stránka pro vlastní výuku hlasu](media/custom-voice/custom-voice-model-training.png)

Získejte **ID modelu** , které chcete použít jako argument pro `ModelId` parametr příkazu Docker run.
<br>

![Podrobnosti o vlastním hlasovém modelu](media/custom-voice/custom-voice-model-details.png)

Následující tabulka představuje různé `docker run` parametry a jejich odpovídající popisy:

| Parametr | Popis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Připojení svazku](https://docs.docker.com/storage/volumes/)hostitelského počítače, které Docker používá k trvalému uložení vlastního modelu. Například *C:\CustomSpeech* , kde se *jednotka C* nachází na hostitelském počítači. |
| `{MODEL_ID}` | **ID modelu** Custom Speech ze stránky **školení** vlastního hlasového portálu. |
| `{ENDPOINT_URI}` | Koncový bod je vyžadován pro měření a fakturaci. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |
| `{API_KEY}` | Klíč rozhraní API je povinný. Další informace najdete v tématu [shromažďování požadovaných parametrů](#gathering-required-parameters). |

Chcete-li spustit vlastní kontejner převodu *textu na řeč* , spusťte následující `docker run` příkaz:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí z image kontejneru vlastní kontejner převodu *textu na řeč* .
* Přiděluje 2 jádra procesoru a 1 gigabajt (GB) paměti.
* Načte vlastní model převodu *textu na řeč* ze vstupního připojení svazku, například *C:\CustomVoice*.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Stáhne model na základě `ModelId` (pokud nebyl nalezen v připojení svazku).
* Pokud se vlastní model stáhl dříve, `ModelId` ignoruje se.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

***

> [!IMPORTANT]
> Pro `Eula`spuštění `Billing`kontejneru musí `ApiKey` být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

> [!NOTE]
> Pokud spouštíte více kontejnerů, použijte jedinečné číslo portu.

| Containers | Adresa URL hostitele sady SDK | Protocol (Protokol) |
|--|--|--|
| Převod řeči na text a Custom Speech textu na text | `ws://localhost:5000` | WS |
| Převod textu na řeč a vlastní převod textu na řeč | `http://localhost:5000` | HTTP |

Další informace o používání protokolů WSS a HTTPS najdete v tématu [zabezpečení kontejnerů](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analýza mínění

Pokud jste zadali přihlašovací údaje rozhraní API pro analýzu textu [do kontejneru](#analyze-sentiment-on-the-speech-to-text-output), můžete použít sadu Speech SDK k posílání požadavků rozpoznávání řeči s analýzou mínění. Odezvy rozhraní API můžete nakonfigurovat tak, aby používaly buď *jednoduchý* nebo *podrobný* formát.

# <a name="simple-format"></a>[Jednoduchý formát](#tab/simple-format)

Chcete-li nakonfigurovat klienta řeči tak, aby používal jednoduchý formát `"Sentiment"` , přidejte jako hodnotu `Simple.Extensions`pro. Pokud chcete zvolit konkrétní verzi modelu Analýza textu, nahraďte `'latest'` ji `speechcontext-phraseDetection.sentimentAnalysis.modelversion` v konfiguraci vlastností.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`Vrátí výsledek mínění v kořenové vrstvě odpovědi.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Podrobný formát](#tab/detailed-format)

Chcete-li nakonfigurovat klienta řeči tak, aby používal podrobný formát `"Sentiment"` , přidejte jako hodnotu `Detailed.Extensions`pro `Detailed.Options`, nebo obojí. Pokud chcete zvolit konkrétní verzi modelu Analýza textu, nahraďte `'latest'` ji `speechcontext-phraseDetection.sentimentAnalysis.modelversion` v konfiguraci vlastností.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`poskytuje výsledek mínění v kořenové vrstvě odpovědi. `Detailed.Options`poskytuje výsledek ve `NBest` vrstvě odpovědi. Dají se použít samostatně nebo společně.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Pokud chcete zcela zakázat analýzu mínění, přidejte `false` hodnotu do. `sentimentanalysis.enabled`

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>Převod textu na řeč nebo vlastní převod textu na řeč

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Spuštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit více kontejnerů s vystavenými porty, ujistěte se, že každý kontejner spustíte s jiným vystaveným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Můžete mít tento kontejner a jiný kontejner Azure Cognitive Services běžící na hostiteli společně. Můžete mít také více kontejnerů stejného Cognitive Services kontejneru se systémem.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění nebo spuštění kontejneru může docházet k problémům. Použijte výstupní [připojení](speech-container-configuration.md#mount-settings) a povolte protokolování. Tím umožníte, aby kontejner generoval soubory protokolu, které jsou užitečné při řešení problémů.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery řeči odesílají informace o fakturaci do Azure pomocí prostředku *řeči* ve vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů řeči. Souhrn:

* Speech poskytuje pro Docker čtyři kontejnery pro Linux, které zapouzdřují různé možnosti:
  * *Převod řeči na text*
  * *Custom Speech na text*
  * *Převod textu na řeč*
  * *Vlastní převod textu na řeč*
* Image kontejneru se stáhnou z registru kontejneru v Azure.
* Image kontejneru se spouštějí v Docker.
* Bez ohledu na to, jestli se používá REST API (jenom pro převod textu na řeč) nebo sadu SDK (převod řeči na text nebo převod textu na řeč), zadáváte identifikátor URI hostitele kontejneru. 
* Při vytváření instance kontejneru budete muset zadat fakturační údaje.

> [!IMPORTANT]
>  Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (např. obrázek nebo analyzovaný text) do Microsoftu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](speech-container-configuration.md) pro nastavení konfigurace
* Naučte [se používat kontejnery služby Speech s Kubernetes a Helm](speech-container-howto-on-premises.md)
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
