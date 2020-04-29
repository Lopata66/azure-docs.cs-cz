---
title: Poznámky k verzi Azure Media Services V3 | Microsoft Docs
description: Abyste měli přehled o nejnovějším vývoji, najdete v tomto článku nejnovější aktualizace Azure Media Services V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769750"
---
# <a name="azure-media-services-v3-release-notes"></a>Zpráva k vydání verze Azure Media Services V3

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` a vložením této adresy URL: do čtečky kanálů RSS.

V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="known-issues"></a>Známé problémy

> [!NOTE]
> Pomocí [Azure Portal](https://portal.azure.com/) můžete spravovat V3 [Live události](live-events-outputs-concept.md), zobrazit [prostředky](assets-concept.md)v3 a získat informace o přístupu k rozhraním API. Pro všechny ostatní úlohy správy (například transformace a úlohy) použijte [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

Další informace najdete v tématu [pokyny k migraci pro přesun z Media Services V2 na V3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>Duben 2020

### <a name="improvements-in-documentation"></a>Vylepšení v dokumentaci

Do [dokumentace k Azure](../azure-media-player/azure-media-player-overview.md)byly migrovány Azure Media Player docs.

## <a name="january-2020"></a>Leden 2020

### <a name="improvements-in-media-processors"></a>Vylepšení v procesorech médií

- Vylepšená podpora prokládaných zdrojů v analýze videa – takový obsah je teď před odesláním do odvozených modulů nesprávně prokládaný.
- Když vygenerujete miniatury s využitím "nejlepšího" režimu, kodér nyní vyhledává rámec 30 sekund, aby vybral rámeček, který není monochromatický.

### <a name="azure-government-cloud-updates"></a>Azure Government aktualizace cloudu

Media Services GA'ed v následujících Azure Government oblastech: *USGov Arizona* a *USGov Texas*.

## <a name="december-2019"></a>Prosinec 2019

Přidala se podpora CDN pro *vyplněné hlavičky předběžného* načítání a pomoci pro živé streamování a streamování videa na vyžádání; k dispozici pro zákazníky, kteří mají přímý kontrakt s Akamai CDN. Počátek – pomoc CDN – funkce předběžného načítání zahrnuje následující výměny hlaviček protokolu HTTP mezi Akamai CDN a Azure Media Servicesm počátkem:

|Hlavička protokolu HTTP|Hodnoty|Odesílatel|Příjemce|Účel|
| ---- | ---- | ---- | ---- | ----- |
|CDN – počátek – pomoc – předběžné načtení – povoleno | 1 (výchozí) nebo 0 |CDN|Zdroj|K označení CDN je povolené předběžné načtení|
|CDN – počátek-asistence – předběžné načtení-cesta| Příklad: <br/>Fragmenty (video = 1400000000, Format = MPD-Time-CMAF)|Zdroj|CDN|Zadání cesty předběžného načtení do sítě CDN|
|CDN – počátek-asistence – předběžné načtení – požadavek|1 (požadavek na předběžné načtení) nebo 0 (pravidelný požadavek)|CDN|Zdroj|Pro indikaci, že žádost z CDN je předběžné načtení|

Pokud se chcete podívat na část výměny hlaviček v akci, můžete vyzkoušet následující postup:

1. Použijte metodu post nebo kudrlinkou k vystavení žádosti o Media Services původu zvukového nebo obrazového segmentu nebo fragmentu videa. Ujistěte se, že jste v žádosti přidali hlavičku CDN-počátek-asistenci-pomoc-předběžné načtení – povoleno: 1.
2. V odpovědi byste měli vidět hlavičku CDN-Origin-Assist-předběžného umístění s relativní cestou jako hodnotou.

## <a name="november-2019"></a>Listopad 2019

### <a name="live-transcription-preview"></a>Živý přepis Preview

Živý přepis je teď ve verzi Public Preview a dostupný pro použití v oblasti Západní USA 2.

Živý přepis je navržený tak, aby fungoval ve spojení s živými událostmi jako doplňkovou funkcí.  Podporuje se u živých událostí předávacího a standardního kódování nebo Premium.  Když je tato funkce povolená, služba použije funkci [řeči k textu](../../cognitive-services/speech-service/speech-to-text.md) Cognitive Services k přepisovat mluveného slova v příchozím zvukovém textu. Tento text je pak k dispozici pro doručování s videem a zvukem v protokolech MPEG-POMLČKy a HLS. Fakturace je založena na novém doplňkovém měřiči, který má za následek dodatečné náklady, když je ve stavu spuštěno.  Podrobnosti o živém přepisu a fakturaci najdete v tématu [živý přepis](live-transcription.md) .

> [!NOTE]
> V současné době je živý přepis dostupný jenom jako funkce Preview v oblasti Západní USA 2. V tuto chvíli podporuje přepis mluvených slov v angličtině (EN-US).

### <a name="content-protection"></a>Ochrana obsahu

Funkce *Prevence opětovného přehrání tokenu* , která byla vydaná v omezených oblastech zpátky v září, je teď dostupná ve všech oblastech.
Media Services zákazníci teď můžou nastavit limit počtu, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Další informace najdete v tématu [Prevence opětovného přehrání tokenu](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Noví doporučení partneři pro živé kodéry

Přidání podpory pro následující nové doporučené partnerské kodéry pro živé streamování RTMP:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [Kamery GoPro Hero7/8 a Max Action](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Vylepšení kódování souborů

- Nyní je k dispozici nová předvolba kódování s podporou obsahu. Vytváří sadu rychlostmi zarovnaných na skupinu GOP pomocí kódování s ohledem na obsah. Vzhledem k jakémukoli vstupnímu obsahu služba provádí počáteční odlehčenou analýzu vstupního obsahu. Tyto výsledky používá k určení optimálního počtu vrstev, vhodné přenosové rychlosti a nastavení rozlišení pro doručování pomocí adaptivního streamování. Tato předvolba je zvláště platná pro videa s nízkou složitostí a středními složitostmi, kde výstupní soubory jsou nižší, ale kvalita, která uživatelům nabízí dobré prostředí. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem. Další informace najdete v tématu o [otevřených specifikacích rozhraní API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Vylepšený výkon a multithreading pro opětovné Sizer ve standardním kodéru. Za určitých podmínek by měl zákazník vidět zvýšení výkonu mezi 5-40% kódováním VOD. Obsah s nízkou složitostí kódovaný do více přenosových rychlostí uvidí nejvyšší zvýšení výkonu. 
- Kódování standard teď při použití nastavení skupinu GOP založeného na čase udržuje regulární skupinu GOP tempo pro obsah VFR (Variable snímkové frekvence) během kódování VOD.  To znamená, že zákazník, který posílá smíšený obsah snímků, který se mezi 15-30 FPS může zobrazit například, by měl vidět normální skupinu GOP vzdálenosti vypočítané na výstupu pro streamování souborů MP4 s adaptivní přenosovou rychlostí. Tím se zvýší schopnost plynule přepínat mezi stopami při doručování přes HLS nebo POMLČKy. 
-  Vylepšená synchronizace AV pro VFR (variabilní snímková frekvence) zdrojového obsahu

### <a name="video-indexer-video-analytics"></a>Video Indexer, analýza videa

- Klíčové snímky extrahované pomocí přednastavené VideoAnalyzer jsou teď v původním rozlišení videa, místo aby se změnila velikost. Extrakce klíčových snímků s vysokým rozlišením poskytuje originální kvalitní image a umožňuje využívat modely umělých inteligentních analýz založené na obrázcích, které poskytuje Microsoft Počítačové zpracování obrazu a Custom Vision, aby bylo možné získat ještě více informací z vašeho videa.

## <a name="september-2019"></a>Září 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Živé lineární kódování pro živé události

Media Services V3 oznamuje verzi Preview 24 hodin x 365 dní živého lineárního kódování živých událostí.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme vyřazení *Azure Media Indexer* a *Azure Media Indexer 2 ve verzi Preview*. Data o vyřazení najdete v tématu [starší verze součástí](../previous/legacy-components.md) . [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahrazuje tyto starší verze procesorů médií.

Další informace najdete v tématu [migrace z Azure Media Indexer a Azure Media Indexer 2 na Azure Media Services video indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Srpen 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Pro Media Services je otevřen regionální pár jar. 

Media Services je teď k dispozici v oblasti Jižní Afrika – sever a Jižní Afrika – západ.

Další informace najdete v tématu [cloudy a oblasti, ve kterých existuje Media Services V3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme, že vyřadíme nepoužívané procesory *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (ázev), které jsou vyřazené. Informace o datech vyřazení najdete v tématu tyto [starší součásti](../previous/legacy-components.md) .

Podrobnosti najdete v článku [migrace WAME do Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) a [migrace do Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Červenec 2019

### <a name="content-protection"></a>Ochrana obsahu

Při streamování obsahu chráněného omezením tokenu musí koncoví uživatelé získat token, který se odešle jako součást žádosti o doručení klíče. Funkce *Prevence opětovného přehrání tokenu* umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Další informace najdete v tématu [Prevence opětovného přehrání tokenu](content-protection-overview.md#token-replay-prevention).

Od července byla funkce Preview dostupná jenom v USA – střed a USA – středozápad.

## <a name="june-2019"></a>Červen 2019

### <a name="video-subclipping"></a>Dílčí výstřižek videa

Video teď můžete při kódování pomocí [úlohy](https://docs.microsoft.com/rest/api/media/jobs)oříznout nebo vystřihnout. 

Tato funkce funguje s libovolnou [transformací](https://docs.microsoft.com/rest/api/media/transforms) , která je sestavená buď pomocí přednastavení [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) , nebo z [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) předvoleb. 

Viz příklady:

* [Vytvoření dílčího klipu videa s využitím .NET](subclip-video-dotnet-howto.md)
* [Vytvoření dílčího klipu videa s REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Květen 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor podporu pro diagnostické protokoly a metriky Media Services

Nyní můžete použít Azure Monitor k zobrazení dat telemetrie emitovaných Media Services.

* Pomocí diagnostických protokolů Azure Monitor můžete monitorovat požadavky odeslané koncovým bodem pro doručení Media Services Key. 
* Monitoruje metriky vydávané Media Services [koncovými body streamování](streaming-endpoint-concept.md).   

Podrobnosti najdete v tématu [monitorování metrik Media Services a diagnostických protokolů](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Podpora více zvukových stop v dynamickém balení 

Při streamování assetů, které mají více zvukových stop s více kodeky a jazyky, teď [Dynamická balení](dynamic-packaging-overview.md) podporuje více zvukových stop pro výstup HLS (verze 4 nebo vyšší).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Oblast Korea je otevřená pro Media Services 

Media Services je teď k dispozici v oblasti Korea – střed a Korea – jih. 

Další informace najdete v tématu [cloudy a oblasti, ve kterých existuje Media Services V3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Zlepšení výkonu

Byly přidány aktualizace, které zahrnují vylepšení výkonu Media Services.

* Maximální velikost souboru podporovaná pro zpracování byla aktualizována. Podívejte se na [kvóty a omezení](limits-quotas-constraints.md).
* [Vylepšení rychlosti kódování](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Duben 2019

### <a name="new-presets"></a>Nové předvolby

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) se přidal do vestavěných přednastavení analyzátoru.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) se přidal do vestavěných přednastavení kodéru. Další informace najdete v tématu [kódování s ohledem na obsah](content-aware-encoding.md). 

## <a name="march-2019"></a>Březen 2019

Dynamické balení teď podporuje Dolby ATMOS. Další informace najdete v tématu [zvukové kodeky podporované dynamickým balením](dynamic-packaging-overview.md#audio-codecs).

Teď můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. Další informace najdete v tématu [přidružení filtrů k lokátoru streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Únor 2019

V národních cloudech Azure se teď podporuje Media Services V3. Ne všechny funkce jsou zatím dostupné ve všech cloudech. Podrobnosti najdete v tématu [cloudy a oblasti, ve kterých existuje Azure Media Services V3](azure-clouds-regions.md).

Do schémat Azure Event Grid byla přidána událost [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) pro Media Services.

## <a name="january-2019"></a>Leden 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Soubory Media Encoder Standard a MPI 

Při kódování s Media Encoder Standard k vytvoření souborů MP4 se vygeneruje nový soubor. MPI a přidá se do výstupního prostředku. Tento soubor MPI má za cíl zlepšit výkon pro [dynamické balení](dynamic-packaging-overview.md) a streamování.

Soubor MPI byste neměli upravovat ani odebírat nebo v rámci služby nemusíte mít žádnou závislost na existenci (nebo ne) takového souboru.

## <a name="december-2018"></a>Prosinec 2018

Mezi verze V3 rozhraní API pro aktualizace od verze GA patří:
       
* Pro **filtry prostředků** a **filtry účtu**se už nevyžadují vlastnosti **PresentationTimeRange** . 
* Možnosti $top a $skip dotazu pro **úlohy** a **transformace** byly odebrány a $OrderBy byly přidány. V rámci přidávání nové funkce řazení bylo zjištěno, že $top a $skip možnosti byly omylem vystaveny dříve, i když nejsou implementovány.
* Rozšiřitelnost výčtu byla znovu povolena. Tato funkce byla povolená ve verzi Preview sady SDK a v rámci verze GA se nechtěně vypnula.
* Dva předdefinované zásady streamování se přejmenovaly. **SecureStreaming** je teď **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** je nyní **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Listopad 2018

Modul CLI 2,0 je teď dostupný pro [Azure Media Services V3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nové příkazy

- [AZ AMS Account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [AZ AMS Account-Filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [AZ AMS Asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ AMS Asset-Filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ AMS Content-Key-Policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [AZ AMS Job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ AMS Live-Event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ AMS Live-Output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ AMS streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ AMS streaming – Lokátor](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -umožňuje správu rezervovaných jednotek médií. Další informace najdete v tématu [škálování rezervovaných jednotek médií](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nové funkce a zásadní změny

#### <a name="asset-commands"></a>Příkazy assetu

- ```--storage-account```a ```--container``` přidané argumenty.
- Výchozí hodnoty pro čas vypršení platnosti (nyní + 23H) a oprávnění (čtení) ```az ams asset get-sas-url``` v příkazu byl přidán.

#### <a name="job-commands"></a>Příkazy úlohy

- ```--correlation-data```a ```--label``` přidané argumenty
- ```--output-asset-names```přejmenování na ```--output-assets```. Nyní přijímá seznam assetů oddělených mezerou ve formátu "název prostředku". Prostředek bez popisku se dá odeslat takto: "Asset =".

#### <a name="streaming-locator-commands"></a>Příkazy lokátoru streamování

- ```az ams streaming locator```základní příkaz nahrazen ```az ams streaming-locator```.
- ```--streaming-locator-id```a ```--alternative-media-id support``` přidané argumenty.
- ```--content-keys argument```argument se aktualizoval.
- ```--content-policy-name```přejmenování na ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Příkazy zásad streamování

- ```az ams streaming policy```základní příkaz nahrazen ```az ams streaming-policy```.
- Podporuje parametry šifrování v ```az ams streaming-policy create``` přidaných.

#### <a name="transform-commands"></a>Příkazy transformace

- ```--preset-names```Argument byl nahrazen ```--preset```parametrem. Nyní můžete nastavit pouze 1 výstup/přednastavení současně (Pokud chcete přidat více, je třeba spustit ```az ams transform output add```). Můžete také nastavit vlastní StandardEncoderPreset předáním cesty k vlastnímu formátu JSON.
- ```az ams transform output remove```dá se provést předáním výstupního indexu, který se má odebrat.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumenty přidané v ```az ams transform create``` příkazech a ```az ams transform output add``` .

## <a name="october-2018---ga"></a>Říjen 2018 – GA

Tato část popisuje Azure Media Services (AMS) Říjen Updates.

### <a name="rest-v3-ga-release"></a>Verze REST v3 pro GA

[Verze REST v3 pro ga](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zahrnuje více rozhraní API pro filtry manifestu Live, Account/Asset Level a podpora DRM.

#### <a name="azure-resource-management"></a>Správa prostředků Azure 

Podpora správy prostředků Azure umožňuje sjednocené rozhraní API pro správu a provoz (teď všechno na jednom místě).

Od této verze můžete použít šablony Správce prostředků k vytvoření živých událostí.

#### <a name="improvement-of-asset-operations"></a>Zlepšení provozu prostředků 

Byla představena následující vylepšení:

- Ingestování z adres URL protokolu HTTP (s) nebo Blob Storage adres URL SAS Azure
- Zadejte vlastní názvy kontejnerů pro prostředky. 
- Snadnější podpora výstupu při vytváření vlastních pracovních postupů pomocí Azure Functions.

#### <a name="new-transform-object"></a>Nový objekt transformace

Nový objekt **transformace** zjednodušuje model kódování. Nový objekt usnadňuje vytváření a sdílení kódování Správce prostředků šablon a přednastavení. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Ověřování Azure Active Directory a RBAC

Ověřování Azure AD a Access Control na základě rolí (RBAC) umožňují zabezpečené transformace, LiveEvents, zásady klíčů obsahu nebo prostředky podle rolí nebo uživatelů v Azure AD.

#### <a name="client-sdks"></a>Klientské sady SDK  

Jazyky podporované v Media Services V3: .NET Core, Java, Node. js, Ruby, TypeScript, Python, přejít.

#### <a name="live-encoding-updates"></a>Živé aktualizace kódování

Zavádí se následující aktualizace pro živé kódování:

- Nový režim s nízkou latencí pro živý (10 sekund od začátku do konce).
- Vylepšená podpora RTMP (zvýšená stabilita a lepší podpora zdrojového kodéru).
- Zabezpečení pro ingestování RTMP

    Při vytváření živé události teď dostanete čtyři adresy URL pro příjem. 4 adresy URL pro přijímání jsou skoro stejné, mají stejný token streamování (AppId), ale liší se jenom část číslo portu. Dvě z těchto adres URL jsou primární a zálohují pro RTMP. 
- Podpora překódování po dobu 24 hodin. 
- Vylepšená podpora služby AD-Signaling v RTMP přes SCTE35.

#### <a name="improved-event-grid-support"></a>Vylepšená podpora Event Grid

Můžete si prohlédnout následující Event Grid vylepšení podpory:

- Azure Event Grid Integration pro snazší vývoj s Logic Apps a Azure Functions. 
- Přihlaste se k odběru událostí pro kódování, živé kanály a další.

### <a name="cmaf-support"></a>Podpora CMAF

CMAF a podpora šifrování "cbcs" pro Apple HLS (iOS 11 +) a přehrávače MPEG-SPOJOVNÍKů, které podporují CMAF.

### <a name="video-indexer"></a>Video Indexer

Vydání Video Indexer GA bylo oznámeno v srpnu. Nové informace o aktuálně podporovaných funkcích najdete v tématu [co je video indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plánuje změny

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Připravuje se modul Azure CLI 2,0, který obsahuje operace se všemi funkcemi (včetně živých, zásad pro klíče obsahu, filtrů účtů a assetů, zásad streamování). 

### <a name="known-issues"></a>Známé problémy

Následující problém mají vliv jenom na zákazníky, kteří použili rozhraní API pro verzi Preview pro Asset nebo AccountFilters.

Pokud jste vytvořili filtry prostředků nebo účtů mezi 09/28 a 10/12 pomocí Media Services V3 CLI nebo rozhraní API, je potřeba odebrat všechny Assety a AccountFilters a znovu je vytvořit z důvodu konfliktu verzí. 

## <a name="may-2018---preview"></a>Květen 2018 – Preview

### <a name="net-sdk"></a>.NET SDK

V sadě .NET SDK jsou k dispozici následující funkce:

* **Transformuje** a **úlohy** ke kódování nebo analýze mediálního obsahu. Příklady najdete v tématu [streamování souborů](stream-files-tutorial-with-api.md) a [Analýza](analyze-videos-tutorial-with-api.md).
* **Lokátory streamování** pro publikování a streamování obsahu do zařízení koncových uživatelů
* Zásady **streamování** a **zásady klíčů obsahu** ke konfiguraci doručování klíčů a ochrany obsahu (DRM) při doručování obsahu.
* **Živé události** a **živé výstupy** umožňují konfigurovat ingestování a archivaci obsahu živého streamování.
* **Prostředky** pro ukládání a publikování mediálního obsahu v Azure Storage. 
* **Koncové body streamování** , které slouží ke konfiguraci a škálování dynamického balení, šifrování a streamování pro živý i mediální obsah na vyžádání.

### <a name="known-issues"></a>Známé problémy

* Při odesílání úlohy můžete určit, že se má vaše zdrojové video ingestovat pomocí adres URL protokolu HTTPS, adres URL SAS nebo cest k souborům umístěným v úložišti objektů BLOB v Azure. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

- [Přehled](media-services-overview.md)
- [Aktualizace dokumentace k Media Services V3](docs-release-notes.md)
- [Poznámky k verzi Media Services V2](../previous/media-services-release-notes.md)
