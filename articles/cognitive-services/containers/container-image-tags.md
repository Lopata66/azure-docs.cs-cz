---
title: Značky imagí kontejnerů Cognitive Services
titleSuffix: Azure Cognitive Services
description: Úplný seznam všech značek imagí kontejneru služby vnímání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e0df3de5eadfd2cc5c00c52da5c4942b42a68b2b
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722564"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Značky image kontejneru Azure Cognitive Services

Azure Cognitive Services nabízí mnoho imagí kontejneru. Registry kontejnerů a odpovídající úložiště se v různých imagích kontejnerů liší. Každý název Image kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určený k použití jako ucelený odkaz pro výpis všech Cognitive Servicesch imagí kontejnerů a jejich dostupných značek.

> [!TIP]
> Při použití [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) aplikace věnujte velkou pozornost pro velká a malá písmena registru kontejnerů, úložiště, názvu image kontejneru a odpovídajících značek – protože rozlišují **velká a malá písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoru anomálií][ad-containers] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services` úložišti a má název `anomaly-detector` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Počítačové zpracování obrazu

Bitovou kopii kontejneru rozpoznávání OCR [počítačové zpracování obrazu][cv-containers] najdete v `containerpreview.azurecr.io` registru kontejneru. Je uložený v `microsoft` úložišti a má název `cognitive-services-read` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-read` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • Další snížení využití paměti pro kontejner. |
|                                          | • Při instalaci s více lusky se vyžaduje externí mezipaměť. Například nastavte Redis pro ukládání do mezipaměti. |
|                                          | • Oprava výsledky chybějící problém, pokud je Redis mezipaměť nastavená na hodnotu ResultExpirationPeriod = 0.  |
|                                          | • Odeberte omezení velikosti textu požadavku 26MB. Kontejner teď může přijímat >soubory 26MB.  |
|                                          | • Přidejte časové razítko a sestavte verzi do protokolování konzoly.  |
| `1.1.013050001-amd64-preview`            | * Přidání ReadEngineConfig: ResultExpirationPeriod konfigurace inicializace kontejneru pro určení, kdy má systém vyčistit výsledky rozpoznávání. |
|                                          | Nastavení je v hodinách a výchozí hodnota je 48hr.   |
|                                          |   Nastavení může snížit využití paměti pro ukládání výsledků, zejména v případě, že je použito úložiště kontejneru v paměti.  |
|                                          |    * Příklad 1. ReadEngineConfig: ResultExpirationPeriod = 1, systém vymaže výsledek rozpoznávání 1hr po dokončení procesu.   |
|                                          |    * Příklad 2. ReadEngineConfig: ResultExpirationPeriod = 0, systém vymaže výsledek rozpoznávání po načtení výsledku.  |
|                                          | Při předání neplatného formátu obrázku do systému byla opravena interní chyba serveru 500. Nyní se vrátí 400 chyba:   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Rozpoznávání tváře

Image kontejneru [Face][fa-containers] se dá najít v `containerpreview.azurecr.io` registru kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-face` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-face` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Rozpoznávání formulářů

Image kontejneru [pro rozpoznávání formulářů][fr-containers] se dá najít v `containerpreview.azurecr.io` registru kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-form-recognizer` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Image kontejneru [Luis][lu-containers] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services` úložišti a má název `luis` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/luis` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Custom Speech na text

Obrázek kontejneru [Custom Speech do textu][sp-cstt] lze nalézt v `containerpreview.azurecr.io` registru kontejneru. Je uložený v `microsoft` úložišti a má název `cognitive-services-custom-speech-to-text` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [pro převod textu na řeč][sp-ctts] najdete v `containerpreview.azurecr.io` registru kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-custom-text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného textu na text][sp-stt] najdete v `containerpreview.azurecr.io` registru kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-speech-to-text` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Obrázek kontejneru s `en-US` národním prostředím |
| `2.2.0-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.2.0-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.2.0-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.2.0-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.2.0-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.2.0-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.2.0-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.2.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.2.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.2.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.2.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.2.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.2.0-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.2.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.2.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.2.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.2.0-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.2.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.2.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.2.0-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.2.0-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.2.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.2.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.2.0-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.2.0-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.2.0-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.2.0-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.2.0-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.2.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.2.0-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.2.0-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.2.0-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.2.0-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.2.0-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.2.0-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.2.0-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.2.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.2.0-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.2.0-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.1.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.1-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.1.1-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.1.1-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.1.1-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.1.1-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.1.1-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.1.1-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.1.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.1.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.1.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.1.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.1.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.1.1-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.1.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.1.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.1.1-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.1.1-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.1.1-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.1.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.1.1-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.1.1-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.1.1-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.1.1-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.1.1-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.1.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.1.1-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.1.1-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.1.1-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.1.1-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.1.1-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.1.1-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.1.1-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.1.1-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.1.1-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.1.0-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.1.0-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.1.0-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.1.0-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.1.0-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.1.0-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.1.0-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.1.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.1.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.1.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.1.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.1.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.1.0-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.1.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.1.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.1.0-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.1.0-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.1.0-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.1.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.1.0-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.1.0-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.1.0-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.1.0-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.1.0-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.1.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.1.0-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.1.0-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.1.0-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.1.0-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.1.0-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.1.0-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.1.0-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.1.0-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.1.0-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.3-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.2-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.0.2-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.2-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.0.2-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.0.2-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.0.2-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.2-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.2-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.2-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.2-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.2-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.2-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.2-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.2-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.2-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.2-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.2-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.2-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.2-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.2-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.0.2-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.2-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.2-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.2-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.2-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.0.2-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.2-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.2-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.2-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.2-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.2-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.2-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.2-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.0.2-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.0.2-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.2-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.2-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.2-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.2-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.1-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.0.1-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.1-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.0.1-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.0.1-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.0.1-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.1-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.1-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.1-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.1-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.0.1-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.1-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.1-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.0.1-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.1-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.1-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.1-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.1-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.1-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.1-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.0.1-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.0.1-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.1-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.1-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.1-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.0-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.0-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.0-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.0-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.0-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.0-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.0-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.0-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.0-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.0-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.0-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.0-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.0-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.0-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.0-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.0-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.0-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `1.2.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.2.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.2.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.2.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.2.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.2.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.2.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.2.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.2.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.2.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.2.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.2.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.2.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.2.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.3-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.3-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.3-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.3-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.3-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.3-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.3-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.3-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.3-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.3-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.3-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.3-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.3-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.3-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.2-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.2-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.2-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.2-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.2-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.2-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.2-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.2-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.2-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.2-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.2-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.2-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.2-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.2-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.0.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.0.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.0.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.0.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.0.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.0.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.0.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.0.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.0.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.0.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek kontejneru [Převod textu na řeč][sp-tts] najdete v `containerpreview.azurecr.io` registru kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS` hlasem        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Obrázek kontejneru s `ar-EG` národním prostředím a `ar-EG-Hoda` hlasem            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Obrázek kontejneru s `ar-SA` národním prostředím a `ar-SA-Naayf` hlasem           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Obrázek kontejneru s `bg-BG` národním prostředím a `bg-BG-Ivan` hlasem            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Obrázek kontejneru s `ca-ES` národním prostředím a `ca-ES-HerenaRUS` hlasem       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Obrázek kontejneru s `cs-CZ` národním prostředím a `cs-CZ-Jakub` hlasem           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Obrázek kontejneru s `da-DK` národním prostředím a `da-DK-HelleRUS` hlasem        |
| `1.3.0-amd64-de-at-michael-preview`         | Obrázek kontejneru s `de-AT` národním prostředím a `de-AT-Michael` hlasem         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Obrázek kontejneru s `de-CH` národním prostředím a `de-CH-Karsten` hlasem         |
| `1.3.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS` hlasem        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Obrázek kontejneru s `el-GR` národním prostředím a `el-GR-Stefanos` hlasem        |
| `1.3.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-HeatherRUS` hlasem      |
| `1.3.0-amd64-en-ca-linda-preview`           | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-Linda` hlasem           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    |
| `1.3.0-amd64-en-ie-sean-preview`            | Obrázek kontejneru s `en-IE` národním prostředím a `en-IE-Sean` hlasem            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS` hlasem     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS` hlasem        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Obrázek kontejneru s `fi-FI` národním prostředím a `fi-FI-HeidiRUS` hlasem        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Obrázek kontejneru s `fr-CH` národním prostředím a `fr-CH-Guillaume` hlasem       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     |
| `1.3.0-amd64-he-il-asaf-preview`            | Obrázek kontejneru s `he-IL` národním prostředím a `he-IL-Asaf` hlasem            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Hemant` hlasem          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana-Apollo` hlasem  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana` hlasem         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana` hlasem         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Obrázek kontejneru s `hr-HR` národním prostředím a `hr-HR-Matej` hlasem           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Obrázek kontejneru s `hu-HU` národním prostředím a `hu-HU-Szabolcs` hlasem        |
| `1.3.0-amd64-id-id-andika-preview`          | Obrázek kontejneru s `id-ID` národním prostředím a `id-ID-Andika` hlasem          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Obrázek kontejneru s `ms-MY` národním prostředím a `ms-MY-Rizwan` hlasem          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Obrázek kontejneru s `nb-NO` národním prostředím a `nb-NO-HuldaRUS` hlasem        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Obrázek kontejneru s `nl-NL` národním prostředím a `nl-NL-HannaRUS` hlasem        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Obrázek kontejneru s `pl-PL` národním prostředím a `pl-PL-PaulinaRUS` hlasem      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Obrázek kontejneru s `pt-PT` národním prostředím a `pt-PT-HeliaRUS` hlasem        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Obrázek kontejneru s `ro-RO` národním prostředím a `ro-RO-Andrei` hlasem          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-EkaterinaRUS` hlasem    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Irina-Apollo` hlasem    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Pavel-Apollo` hlasem    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Obrázek kontejneru s `sk-SK` národním prostředím a `sk-SK-Filip` hlasem           |
| `1.3.0-amd64-sl-si-lado-preview`            | Obrázek kontejneru s `sl-SI` národním prostředím a `sl-SI-Lado` hlasem            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Obrázek kontejneru s `sv-SE` národním prostředím a `sv-SE-HedvigRUS` hlasem       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Obrázek kontejneru s `ta-IN` národním prostředím a `ta-IN-Valluvar` hlasem        |
| `1.3.0-amd64-te-in-chitra-preview`          | Obrázek kontejneru s `te-IN` národním prostředím a `te-IN-Chitra` hlasem          |
| `1.3.0-amd64-th-th-pattara-preview`         | Obrázek kontejneru s `th-TH` národním prostředím a `th-TH-Pattara` hlasem         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Obrázek kontejneru s `tr-TR` národním prostředím a `tr-TR-SedaRUS` hlasem         |
| `1.3.0-amd64-vi-vn-an-preview`              | Obrázek kontejneru s `vi-VN` národním prostředím a `vi-VN-An` hlasem              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Danny-Apollo` hlasem    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Tracy-Apollo` hlasem    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-TracyRUS` hlasem        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-HanHanRUS` hlasem       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Yating-Apollo` hlasem   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Zhiwei-Apollo` hlasem   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS` hlasem        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   |
| `1.2.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS` hlasem     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS` hlasem        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |
| `1.1.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS` hlasem        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   |
| `1.1.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS` hlasem     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS` hlasem        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS` hlasem     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS` hlasem        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Bitovou kopii kontejneru [extrakce klíčových frází][ta-kp] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `keyphrase` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/keyphrase` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Rozpoznávání jazyka

Bitovou kopii kontejneru [rozpoznávání jazyka][ta-la] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `language` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/language` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analýza mínění

Bitovou kopii kontejneru [Analýza mínění][ta-se] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `sentiment` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/sentiment` .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analýza mínění V3 (angličtina)               |
| `3.0-es`   | Analýza mínění V3 (španělština)               |
| `3.0-fr`   | Analýza mínění V3 (francouzština)                |
| `3.0-it`   | Analýza mínění V3 (italština)               |
| `3.0-de`   | Analýza mínění V3 (němčina)                |
| `3.0-zh`   | Analýza mínění V3 (zjednodušená čínština)  |
| `3.0-zht`  | Analýza mínění V3 (tradiční čínština) |
| `3.0-ja`   | Analýza mínění V3 (japonština)              |
| `3.0-pt`   | Analýza mínění V3 (portugalština)            |
| `3.0-nl`   | Analýza mínění V3 (holandština)                 |
| `1.1.009301-amd64-preview`    | Analýza mínění v2      |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
