---
title: Migrace ze služby indexer V1 a V2 na Azure Media Services Video Indexer | Microsoft Docs
description: Toto téma popisuje, jak migrovat z Azure Media Indexer V1 a v2 do Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: d38b11b8fc4351c6b074ccfdf47df06a71e0a0a4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823971"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrace z Media Indexer a Media Indexer 2 na Video Indexer

Procesor [Azure Media Indexer](media-services-index-content.md) médií bude vyřazení od 1. října 2020. Od 1. ledna 2020 budou zpracovatelské procesory [Azure Media Indexer 2 ve verzi Preview](media-services-process-content-with-indexer2.md) vyřazené.  [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahrazuje tyto starší verze procesorů médií.

Azure Media Services Video Indexer je postavená na Azure Media Analytics, Azure Kognitivní hledání Cognitive Services (například Face API, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a Custom Speech Service). Umožňuje extrahovat přehledy z videí pomocí zvukových modelů a videomodelů Video Indexeru. Pokud chcete zjistit, jaké scénáře se Video Indexer můžou použít v, jaké funkce nabízí a jak začít, přečtěte si téma [video indexer video a zvukové modely](../video-indexer/video-indexer-overview.md). 

Přehledy z vašich videosouborů a zvukových souborů můžete extrahovat pomocí [přednastavení analyzátoru Azure Media Services V3](../latest/analyzing-video-audio-files-concept.md) nebo přímo pomocí [rozhraní video indexer API](https://api-portal.videoindexer.ai/). V současné době dochází k překrytí mezi funkcemi nabízenými rozhraními API Video Indexer a rozhraními API Media Services V3.

> [!NOTE]
> Chcete-li pochopit, kdy byste chtěli použít Video Indexer a přednastavení Media Services Analyzer, Projděte si [dokument porovnání](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Tento článek popisuje kroky migrace z Azure Media Indexer a Azure Media Indexer 2 na Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Možnosti migrace 

|Pokud vyžadujete  |Stisknutím |
|---|---|
|řešení, které poskytuje přepis mluveného slova na text pro libovolný formát mediálního souboru ve formátech skrytých souborů titulků: VTT, SRT aplikace nebo TTML<br/>i další zvukové přehledy, jako jsou klíčová slova, témata Inferencing, akustické události, mluvčí diarization, extrakce a překlad entit.| Aktualizujte své aplikace tak, aby používaly funkce Video Indexer Azure přes Video Indexer v2 REST API nebo přednastavení zvukového analyzátoru Azure Media Services V3.|
|možnosti převodu řeči na text| Použijte přímo rozhraní Cognitive Services Speech API.|  

## <a name="getting-started-with-video-indexer"></a>Začínáme s Video Indexer

V následující části najdete relevantní odkazy: [Jak můžu začít s video indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Začínáme s rozhraními API pro Media Services V3

Rozhraní Azure Media Services V3 API umožňuje z vašich videí a zvukových souborů extrahovat přehledy pomocí [přednastavených funkcí analyzátoru Azure Media Services V3](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** umožňuje extrahovat z zvukového souboru nebo videosouboru více zvukových přehledů. Výstup obsahuje soubor VTT nebo TTML pro záznam zvuku a soubor JSON (se všemi dalšími dalšími informacemi o službě Audio Insights). Zvukové poznatky zahrnují klíčová slova, indexování mluvčích a analýzu mínění řeči. AudioAnalyzerPreset podporuje také rozpoznávání jazyka pro konkrétní jazyky. Podrobné informace najdete v tématu [transformace](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Začínáme

Pokud chcete začít, podívejte se na:

* [Kurz](../latest/analyze-videos-tutorial-with-api.md)
* Ukázky AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) nebo [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Ukázky VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) nebo [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Začínáme se službou Cognitive Services Speech Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) poskytuje službu pro převod řeči na text, která transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. K [přizpůsobení vlastního akustického modelu, jazykového modelu nebo modelu výslovnosti](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)můžete použít převod řeči na text. Další informace najdete v tématu [Cognitive Services převodu řeči na text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Služba převod řeči na text nebere v úvahu formáty videosouborů a převádí jenom [Některé formáty zvuku](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Další informace o službě převod textu na řeč a o tom, jak začít, najdete v tématu [co je převod z řeči na text](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text) .

## <a name="known-differences-from-deprecated-services"></a>Známé rozdíly oproti zastaralým službám 

Zjistíte, že Video Indexer Azure Media Services V3 AudioAnalyzerPreset a služby Cognitive Services Speech Services jsou spolehlivější a vytváří lepší výstup kvality než vyřazení Azure Media Indexer 1 a Azure Media Indexer 2.  

Mezi známé rozdíly patří: 

* Skryté soubory titulků ve formátu SAMI se už nebudou podporovat. Toto je starší formát titulků, který se už nepoužívá široce. Je nahrazena TTML, WebVTT a SRT aplikace.  
* Soubory objektů BLOB pro indexování zvuku (AIB) již nebudou podporovány. Tato funkce byla specifická pro technologii indexeru 1 a už se nenabízí.  
* Služba Cognitive Services Speech Services nepodporuje extrakci klíčových slov. Video Indexer a Media Services V3 však AudioAnalyzerPreset zároveň nabízí robustnější sadu klíčových slov ve formátu JSON. 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Další kroky

* [Starší verze součástí](legacy-components.md)
* [Stránka s cenami](https://azure.microsoft.com/pricing/details/media-services/#encoding)


