---
title: Instalace a spuštění kontejnerů
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Postup stažení, instalaci a spouštění kontejnerů v tomto kurzu návod pro analýzu textu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 39c1a212bee552b436ef21d208f4a20086578325
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437845"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalace a spouštění kontejnerů pro analýzu textu

Kontejnery rozhraní Text Analytics poskytuje pokročilé zpracování přirozeného jazyka nezpracovaný text a zahrnuje tři hlavní funkce: analýzu mínění, extrakci klíčových frází a rozpoznání jazyka. Propojování entit se aktuálně nepodporuje v kontejneru. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné spustit jakýkoli Text Analytics, musí mít hostitelský počítač a kontejneru prostředí.

## <a name="preparation"></a>Příprava

Před použitím kontejnerů pro analýzu textu, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Text Analytics prostředků |Chcete-li použít kontejner, musíte mít:<br><br>A [ _rozhraní Text Analytics_ ](text-analytics-how-to-access-key.md) prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách portálu Azure přehled analýzy textu a klíče a jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené Procesorových jader a aspoň 2.6 gigahertz (GHz) nebo rychlejší a paměti v gigabajtech (GB), přidělit pro každý kontejner pro analýzu textu.

| Kontejner | Minimální | Doporučené | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Extrakce klíčových frází | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|
|Rozpoznávání jazyka | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|
|Analýza mínění | 1 jádro, 2 GB paměti | 1 jádro, 4 GB paměti |15, 30|

* Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.
* TPS – transakcí za sekundu

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Ze služby Microsoft Container Registry jsou dostupné Image kontejneru pro analýzu textu. 

| Kontejner | Úložiště |
|-----------|------------|
|Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z registru kontejneru Microsoft.

Úplný popis dostupných značek pro kontejnery pro analýzu textu naleznete v následujících kontejnerech na úložiště Docker Hub:

* [Extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Rozpoznávání jazyka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analýza subjektivního hodnocení](https://go.microsoft.com/fwlink/?linkid=2018654)

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Operace docker pull pro kontejner klíče frázi extrakce

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Operace docker pull pro kontejner detekce jazyka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Operace docker pull pro kontejner mínění

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s požadovanými fakturace nastavení. Další [příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče Text Analytics Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem Text Analytics Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spouští klíčových frází kontejner z image kontejneru
* Přidělí jedno Procesorové jádro a 4 gigabajty (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

Další [příklady](../text-analytics-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. 

Použít hostitele, `https://localhost:5000`, pro kontejner rozhraní API.

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](../text-analytics-resource-container-config.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="containers-api-documentation"></a>Dokumentace k rozhraní API kontejneru

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturace

Fakturační údaje do Azure, pomocí odesílání kontejnery pro analýzu textu _rozhraní Text Analytics_ prostředků v účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro analýzu textu. Souhrn:

* Rozhraní text Analytics poskytuje tři kontejnery Linuxu pro Docker, zapouzdření extrakce klíčových frází, rozpoznávání jazyka a analýzy subjektivního hodnocení.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech rozhraní Text Analytics tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](../text-analytics-resource-container-config.md) nastavení konfigurace
* Odkazovat na [– nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md) k vyřešení problémů týkajících se funkčnosti.

