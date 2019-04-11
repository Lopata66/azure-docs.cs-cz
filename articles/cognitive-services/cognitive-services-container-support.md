---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak získat Cognitive Services blíž ke svým datům kontejnery Dockeru.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: c6cf3f188309891b0456b034e3809a0f0209c124
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469535"
---
# <a name="container-support-in-azure-cognitive-services"></a>Podpora kontejnerů ve službě Azure Cognitive Services

Podpora kontejnerů ve službě Azure Cognitive Services umožňuje vývojářům používat stejná bohatá rozhraní API, které jsou dostupné v Azure a umožňuje flexibilitu při nasazení a hostování služeb, které jsou součástí [kontejnery Dockeru](https://www.docker.com/what-container). Podpora kontejnerů je aktuálně dostupná ve verzi preview pro podmnožinu služeb Azure Cognitive Services, včetně částí [pro počítačové zpracování obrazu](Computer-vision/Home.md), [pro rozpoznávání tváře](Face/Overview.md), [rozhraní Text Analytics](text-analytics/overview.md)a [ Language Understanding](LUIS/luis-container-howto.md) (LUIS).

Kontejnerizace je přístup k distribuci softwaru, ve kterém k aplikaci nebo službě, včetně konfigurace a jeho závislostí je oprav jako image kontejneru. S téměř nebo vůbec žádné změny je možné nasadit image kontejneru na hostiteli. Kontejnery jsou izolované od sebe navzájem a základní operační systém s menší nároky na místo než virtuální počítač. Kontejnery můžete vytvořené z imagí kontejneru pro krátkodobé úlohy a odebrat, pokud už je nepotřebujete.

Následující video ukazuje použití kontejner služeb Cognitive Services.

[![Cstyly ukázku pro služby Cognitive Services](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

[Pro počítačové zpracování obrazu](Computer-vision/Home.md), [pro rozpoznávání tváře](Face/Overview.md), [rozhraní Text Analytics](text-analytics/overview.md), a [Language Understanding (LUIS)](LUIS/what-is-luis.md) služby jsou dostupné na [Microsoft Azure](https://azure.microsoft.com). Přihlaste se [webu Azure portal](https://portal.azure.com/) k vytvoření a prozkoumáte prostředky Azure pro tyto služby.

## <a name="features-and-benefits"></a>Funkce a výhody

- **Kontrola nad daty**: Umožňuje zákazníkům zvolit, kam svá data zpracovává tyto služby Cognitive Services. To je nezbytné pro zákazníky, které nelze odesílat data do cloudu, ale přístup k technologie služeb Cognitive Services. Podpora konzistence v hybridním prostředí – napříč daty, správu, identity a zabezpečení.
- **Ovládací prvek průběhu aktualizace modelu**: Poskytují zákazníkům flexibilitu při vytváření verzí a aktualizace modelů nasazení v rámci svých řešení.
- **Přenosné architektura**: Povolte vytváření architektura přenosné aplikace, který je možné nasadit v Azure, místní a hraniční. Kontejnery, které je možné nasadit přímo do [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), nebo [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Zásobník](../azure-stack/index.yml). Další informace najdete v tématu [nasazení Kubernetes pro Azure Stack](../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
- **Vysoká propustnost / nízká latence**: Umožňují zákazníkům škálovat pro vysokou propustností a nízkou latencí tím, že služby Cognitive Services a spusťte fyzicky blízko jejich aplikační logiku a data. Kontejnery není limit transakcí za sekundu (TPS) a můžete provést škálování současně i horizontální navyšování kapacity pro zpracování poptávky, pokud zadáte nezbytné hardwarové prostředky. 


## <a name="containers-in-azure-cognitive-services"></a>Kontejnery v Azure Cognitive Services

Kontejnery služby Azure Cognitive Services nabízejí následující sadu kontejnerů Dockeru, z nichž každý obsahuje podmnožinu funkcí ze služeb Azure Cognitive Services:

| Služba | Cenovou úroveň. | Kontejner | Popis |
|---------|----------|----------|-------------|
|[Computer Vision](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**Rozpoznání textu** |Extrahuje tištěný text z obrázků různé objekty s různými povrchy a pozadími, jako je potvrzení a plakáty nebo vizitky.<br/><br/>**Důležité:** Kontejner rozpoznat Text v současné době používá pouze angličtinu.<br>[Vyžádat si přístup](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Tvář](Face/face-how-to-install-containers.md) |F0, S0|**Tvář** |Zjistí lidských tváří na obrázcích a Určuje atributy, včetně orientačních bodů pro rozpoznávání tváře (například ústa a oči), pohlaví, věk a další funkce rozpoznávání obličeje předpovědět počítače. Kromě zjišťování můžete pro rozpoznávání tváře zkontrolujte, jestli dvě tváře na stejnou bitovou kopii nebo jinou Image jsou stejné s použitím skóre spolehlivosti nebo porovnání proti databázi a zjistěte, jestli podobně vypadajících tváří, nebo identické pro rozpoznávání tváře již existuje. Můžete také uspořádat podobných tváří do skupin pomocí sdílené visual vlastností.<br>[Vyžádat si přístup](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**Služba LUIS** ([image](https://go.microsoft.com/fwlink/?linkid=2043204))|Načte trénovaného nebo publikované Language Understanding modelu, označované také jako aplikace LUIS, do kontejneru dockeru a poskytuje přístup k předpovědi dotazu z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrání tyto zpět a [LUIS portál](https://www.luis.ai) zvyšte přesnost předpovědi aplikace.|
|[Analýza textu](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Extrakce frází klíč** ([image](https://go.microsoft.com/fwlink/?linkid=2018757)) |Extrahuje klíčových frází pro identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). |
|[Analýza textu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Rozpoznávání jazyka** ([image](https://go.microsoft.com/fwlink/?linkid=2018759)) |Až 120 jazyků zjišťuje, jaké vstupní text je napsaný v jazyce a sestavy jeden jazyk kódu pro každý dokument podání žádosti. Kód jazyka spárovaný se skóre označuje sílu skóre. |
|[Analýza textu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Analýza subjektivního hodnocení** ([image](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analyzuje nezpracovaný text pro příčiny o pozitivní nebo negativní zabarvení. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení. Modely analýzu jsou předem trénuje pomocí rozsáhlé tělo technologií text a přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](./text-analytics/language-support.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. |

Kromě toho jsou podporovány kontejnery ve službě Cognitive Services [All-in-One nabídky](https://azure.microsoft.com/pricing/details/cognitive-services/). Můžete vytvořit jeden jeden prostředek All-in-One služeb Cognitive Services a používají stejný klíč fakturace pro všechny typy kontejnerů uvedených výše.

## <a name="container-availability-in-azure-cognitive-services"></a>Dostupnost kontejneru ve službě Azure Cognitive Services

Kontejnery služby Azure Cognitive Services jsou veřejně dostupné prostřednictvím předplatného Azure a imagí kontejnerů Dockeru můžete načíst z Microsoft Container Registry nebo Docker Hubu. Můžete použít [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z registru odpovídající.

> [!IMPORTANT]
> V současné době musíte dokončit proces registrace pro přístup k [pro rozpoznávání tváře](Face/face-how-to-install-containers.md) a [rozpoznat Text](Computer-vision/computer-vision-how-to-install-containers.md) kontejnery, ve kterých vyplňují a odesílají dotazník otázek o vás, vaše společnost a případ použití pro které chcete implementovat kontejnery. Když jste udělili přístup a zadat přihlašovací údaje, můžete pak načítání imagí kontejneru pro rozpoznávání tváře a rozpoznat Text kontejnerů v privátním registru kontejneru hostovaná služba Azure Container Registry.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů Azure Cognitive Services, musí splňovat následující požadavky:

**Modul docker**: Musíte mít lokálně nainstalovaný modul Docker. Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), a [Windows](https://docs.docker.com/docker-for-windows/). Na Windows Docker musí být nakonfigurované pro podporu kontejnerů Linuxu. Kontejnery dockeru je také možné nasadit přímo do [Azure Kubernetes Service](../aks/index.yml) nebo [Azure Container Instances](../container-instances/index.yml).

Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure.

**Znalost společnosti Microsoft Container Registry a Docker**: Byste měli mít základní znalost konceptů Microsoft Container Registry a Docker, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.

Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).

Jednotlivých kontejnerů může mít vlastní požadavky, včetně serveru a požadavky na přidělení paměti.

## <a name="developer-samples"></a>Ukázky pro vývojáře

Ukázky pro vývojáře najdete na adrese naše [úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Další postup

Instalace a prozkoumejte funkce poskytované službou kontejnerů ve službě Azure Cognitive Services:

* [Nainstalovat a používat kontejnery pro počítačové zpracování obrazu](Computer-vision/computer-vision-how-to-install-containers.md)
* [Nainstalovat a používat kontejnery pro rozpoznávání tváře](Face/face-how-to-install-containers.md)
* [Nainstalovat a používat kontejnery pro analýzu textu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [Nainstalovat a používat kontejnery Language Understanding (LUIS)](LUIS/luis-container-howto.md)
