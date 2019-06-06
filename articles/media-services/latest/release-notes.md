---
title: Azure Media Services v3 poznámky k verzi | Dokumentace Microsoftu
description: Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje nejnovější informace o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 06/04/2019
ms.author: juliako
ms.openlocfilehash: e34e0f9fee1d09e66dcdd5069e800dc1f3f750cb
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688645"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 poznámky k verzi

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="known-issues"></a>Známé problémy

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použití [rozhraní REST API](https://aka.ms/ams-v3-rest-sdk), rozhraní příkazového řádku, nebo jeden z podporovaných sad SDK.

Další informace najdete v tématu [pokyny k migraci pro přechod ze služby Media Services v2 na v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="may-2019"></a>. Května 2019.

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Podpora Azure Monitor pro Media Services diagnostické protokoly a metriky

Azure Monitor můžete nyní zobrazit telemetrická data emmited službou Media Services.

* Použijte diagnostické protokoly Azure monitoru ke sledování požadavků odesílaných Media Services klíč doručování koncový bod. 
* Monitorování metrik, protože ho vygeneroval Media Services [koncové body streamování](streaming-endpoint-concept.md).   

Podrobnosti najdete v tématu [Media Services monitorování metrik a diagnostických protokolů](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Zvukové stopy s více podporují v dynamické balení 

Při streamování prostředky, které mají více zvukové stopy s více kodeky a jazyky, [dynamické balení](dynamic-packaging-overview.md) teď podporuje více zvukové stopy pro výstup HLS (verze 4 nebo vyšší).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea páru oblastí je otevřen pro Media Services 

Služba Media Services je teď dostupná v oblastech Korea – střed a Korea – jih. 

Další informace najdete v tématu [Cloudy a oblasti, ve které Media Services v3 existuje](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Vylepšení výkonu

Přidané aktualizace, které zahrnují vylepšení výkonu služby Media Services.

* Maximální velikost souboru podporovaná při zpracování byl aktualizován. Zobrazit, [kvóty a omezení](limits-quotas-constraints.md).
* [Kódování vylepšení rychlosti](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>2019. dubna

### <a name="new-presets"></a>Nové přednastavení

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) byl přidán do přednastavení integrovaná analýza.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) přidal do předdefinované kodér přednastavení. Další informace najdete v tématu [kódování obsahu](cae-experimental.md). 

## <a name="march-2019"></a>2019. března

Dynamické balení, teď podporuje Dolby Atmos. Další informace najdete v tématu [zvuk kodeky podporuje dynamické balení](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Nyní můžete zadat seznam prostředků nebo účet filtrů, které pro vaše Lokátor streamování. Další informace najdete v tématu [filtry přidružit Lokátor streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>. Února 2019

Media Services v3 je nyní podporována v národních cloudů Azure. Ne všechny funkce jsou dostupné ve všech cloudech ještě. Podrobnosti najdete v tématu [Cloudy a oblasti, ve které Azure Media Services v3 existuje](azure-clouds-regions.md).

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) událostí byl přidán do služby Azure Event Grid schémata pro Media Services.

## <a name="january-2019"></a>2019. ledna

### <a name="media-encoder-standard-and-mpi-files"></a>Soubory kodéru Media Encoder Standard a MPI 

Při kódování pomocí Media Encoder Standard na vytvářejí soubory MP4, je nový soubor .mpi generují a přidávají do výstupu Asset. Tento soubor MPI slouží pro zvýšení výkonu [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

Nesmí změnit nebo odebrat soubor MPI či provést všechny závislosti ve své službě existence (nebo nemusíte) tyto souboru.

## <a name="december-2018"></a>Prosinec 2018

Aktualizace z verze GA z rozhraní API V3 zahrnují:
       
* **PresentationTimeRange** již nejsou požadovány pro vlastnosti **Asset filtry** a **filtrů účtů**. 
* Možnosti pro dotaz $top a $skip **úlohy** a **transformuje** byly odebrány a byla přidána $orderby. Při přidání nové funkce řazení bylo zjištěno, že možnosti $top a $skip došlo omylem byl zpřístupněn dříve i v případě, že nejsou implementované.
* Rozšíření výčtu se znovu povolil. Tato funkce bylo povoleno ve verzi preview verze sady SDK a je teď ve verzi GA omylem zakázán.
* Dvě předdefinované datové proudy zásady byly přejmenovány. **SecureStreaming** je nyní **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** je nyní **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>. Listopadu 2018

Modul CLI 2.0 je nyní k dispozici pro [všeobecné dostupnosti služby Azure Media Services v3](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nové příkazy

- [AZ jeden účet ams](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [účet ams az-filtru](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [asset ams az-filtru](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [živá ams az – událost](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [streamování ams az-lokátoru](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ ams account naposledy použité položky](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – umožňuje spravovat rezervovaných jednotek médií. Další informace najdete v tématu [rezervovaných jednotek médií škálování](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nové funkce a změny způsobující chyby

#### <a name="asset-commands"></a>Příkazy Asset

- ```--storage-account``` a ```--container``` argumenty přidán.
- Výchozí hodnoty pro oprávnění (čtení) a čas vypršení platnosti (Now + 23h) v ```az ams asset get-sas-url``` příkazu přidaného.

#### <a name="job-commands"></a>Příkazy úlohy

- ```--correlation-data``` a ```--label``` Přidat argumenty
- ```--output-asset-names``` přejmenovat na ```--output-assets```. Nyní přijímá místo oddělený seznam prostředků v "assetName = label" formátu. Prostředek bez popisku je možné odeslat takto: "assetName =".

#### <a name="streaming-locator-commands"></a>Příkazy Lokátor streamování

- ```az ams streaming locator``` základní příkaz nahradí ```az ams streaming-locator```.
- ```--streaming-locator-id``` a ```--alternative-media-id support``` argumenty přidán.
- ```--content-keys argument``` argument aktualizovat.
- ```--content-policy-name``` přejmenovat na ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Streamování příkazy zásad

- ```az ams streaming policy``` základní příkaz nahradí ```az ams streaming-policy```.
- Podpora šifrování parametry v ```az ams streaming-policy create``` přidán.

#### <a name="transform-commands"></a>Transformovat

- ```--preset-names``` argument nahrazena ```--preset```. Nyní lze nastavit pouze 1 výstup/přednastavení najednou (abyste mohli přidat další musíte spustit ```az ams transform output add```). Také můžete nastavit vlastní StandardEncoderPreset předáním cesty do vaší vlastní formátu JSON.
- ```az ams transform output remove``` můžete provést předáním výstup index odebrat.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenty přidá ```az ams transform create``` a ```az ams transform output add``` příkazy.

## <a name="october-2018---ga"></a>. Října 2018 – GA

Tato část popisuje aktualizace Azure Media Services (AMS) dne.

### <a name="rest-v3-ga-release"></a>Verze GA v3 REST

[Verze GA v3 REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zahrnuje další rozhraní API pro Live, manifestu filtry na úrovni účtu/Asset a podpora DRM.

#### <a name="azure-resource-management"></a>Správa prostředků Azure 

Podpora správy prostředků Azure umožňuje jednotnou správu a operace rozhraní API (teď všechno na jednom místě).

Od této verze, můžete pomocí šablon Resource Manageru k vytvoření živé události.

#### <a name="improvement-of-asset-operations"></a>Zlepšení Asset operací 

Byly zavedeny následující vylepšení:

- Ingestování z adresy URL http (s) nebo adresy URL SAS úložiště objektů Blob v Azure.
- Zadejte vlastní kontejner názvy pro prostředky. 
- Jednodušší podpora výstupu k vytvoření vlastních pracovních postupů s využitím Azure Functions.

#### <a name="new-transform-object"></a>Nové transformace objektů

Nové **transformace** objekt zjednodušuje modelu kódování. Nový objekt umožňuje snadno vytvářet a sdílet kódování šablon Resource Manageru a předvolby. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Ověřování služby Active Directory a Azure RBAC

Ověřování Azure AD a řízení přístupu na základě Role (RBAC) umožňují zabezpečené transformací, LiveEvents, zásady klíč obsahu nebo prostředků podle Role nebo uživatelé ve službě Azure AD.

#### <a name="client-sdks"></a>Klientské sady SDK  

Jazyky podporované v Media Services v3: .NET Core, Javy, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Živé kódování aktualizace

Jsou zavedené následující živé kódování aktualizace:

- Nový režim s nízkou latencí pro živé (10 sekund end-to-end).
- (Zvýšení stability a další podporu zdrojového kodér) Vylepšená podpora RTMP ve službě.
- Ingestování RTMPS zabezpečené.

    Když vytvoříte živá událost, můžete teď získání 4 ingestovaných adres URL. Ingestování 4 adresy URL jsou téměř identické, mít stejný token streamování (AppId), jenom část čísla portu se liší. Dva z adres URL jsou primární a záložní pro RTMPS. 
- Podpora překódování 24 hodin. 
- Vylepšená podpora ad signalizace v RTMP prostřednictvím SCTE35.

#### <a name="improved-event-grid-support"></a>Vylepšená podpora služby Event Grid

Zobrazí se následující služby Event Grid podporují vylepšení:

- Integrace služby Azure Event Grid pro jednodušší vývoj pomocí Logic Apps a Azure Functions. 
- Přihlásit k odběru událostí v kódování, živých kanálů a další.

### <a name="cmaf-support"></a>Podpora CMAF

Podpora šifrování CMAF a "cbcs" (iOS 11 +) Apple HLS a MPEG-DASH přehrávačů, které podporují CMAF.

### <a name="video-indexer"></a>Video Indexer

Video verzi Indexer GA byl jsme oznámili v srpnu. Nové informace o aktuálně podporovaných funkcích najdete v tématu [co je Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plány pro změny

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Modul Azure CLI 2.0, který obsahuje operace s funkcí (včetně Live, obsahu zásady klíčů, účtu nebo Asset filtry, streamování zásady) je již brzy. 

### <a name="known-issues"></a>Známé problémy

Tento problém týká pouze zákazníků, které používají rozhraní API ve verzi preview pro prostředek nebo AccountFilters.

Pokud jste vytvořili prostředky nebo filtry účtů 09/28 až 10/12 pomocí Media Services v3 rozhraní příkazového řádku nebo rozhraní API, musíte odebrat všechny prostředky a AccountFilters a znovu je vytvořit z důvodu konfliktu verze. 

## <a name="may-2018---preview"></a>Květen 2018 – Preview

### <a name="net-sdk"></a>.NET SDK

Následující funkce jsou k dispozici v sadě .NET SDK:

* **Transformuje** a **úlohy** k zakódování nebo analyzovat mediálního obsahu. Příklady najdete v tématu [Stream soubory](stream-files-tutorial-with-api.md) a [analyzovat](analyze-videos-tutorial-with-api.md).
* **Lokátory streamování** pro publikování a streamování obsahu do zařízení koncových uživatelů
* **Streamování zásady** a **zásady klíčů obsahu** konfigurace doručení klíče a ochrana obsahu (DRM) při doručování obsahu.
* **Živé události** a **Live výstupy** konfigurace ingestování a archivaci obsah živého streamování.
* **Prostředky** ukládat a publikovat mediálního obsahu ve službě Azure Storage. 
* **Koncové body streamování** ke konfiguraci a škálování dynamické balení, šifrování a streamování živě i na vyžádání multimediálního obsahu.

### <a name="known-issues"></a>Známé problémy

* Po odeslání úlohy, můžete ingestovat zdrojového videa zdroje pomocí adresy URL HTTPS, adresy URL SAS nebo cesty k souborům umístěným v úložišti objektů Blob v Azure. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)
