---
title: Livestream nastavení nízké latence v Azure Media Services | Microsoft Docs
description: Toto téma obsahuje přehled nastavení Livestream s nízkou latencí a ukazuje, jak nastavit nízkou latenci.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 720931cf9f955f63075e3881b6b9f2e884bd3a76
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232453"
---
# <a name="live-event-low-latency-settings"></a>Nastavení nízké latence události za provozu

Tento článek ukazuje, jak nastavit nízkou latenci pro [živou událost](https://docs.microsoft.com/rest/api/media/liveevents). Pojednává také o typických výsledcích, které vidíte při použití nastavení nízké latence v různých přehrávačích. Výsledky se liší v závislosti na síti CDN a latenci sítě.

Chcete-li použít novou funkci **LowLatency** , nastavte **StreamOptionsFlag** na **LowLatency** na **Livestream**. Při vytváření [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) pro přehrávání HLS nastavte [LiveOutput. HLS. fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) na hodnotu 1. Jakmile je datový proud v provozu, můžete použít [Azure Media Player](https://ampdemo.azureedge.net/) (Ukázková stránka amp) a nastavit možnosti přehrávání tak, aby používaly profil heuristiky s nízkou latencí.

> [!NOTE]
> V současné době je LowLatency HeuristicProfile v Azure Media Player navržena pro přehrávání back-streamů v protokolu MPEG-SPOJOVNÍKu s formátem CSF nebo CMAF (například `format=mdp-time-csf` nebo `format=mdp-time-cmaf` ). 

Následující příklad rozhraní .NET ukazuje, jak nastavit **LowLatency** na **Livestream**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Podívejte se na úplný příklad: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latence živých událostí

V následujících tabulkách jsou uvedeny typické výsledky pro latenci (když je povolený příznak LowLatency) v Media Services, měřeno od doby, kdy kanál příspěvků dosáhne toho, kdy je v přehrávači vidět přehrávání. Chcete-li použít nízkou latenci, měli byste nastavení kodéru vyladit až na 1 sekundu "délku" skupiny obrázků (skupinu GOP). Při použití větší skupinu GOP délky minimalizujete využití šířky pásma a omezíte přenosovou rychlost při stejné frekvenci snímků. Je to obzvláště užitečné u videí s menším pohybem.

### <a name="pass-through"></a>Průchod 

||2S – skupinu GOP s povolenou nízkou latencí|1 skupinu GOP nízká latence povolena|
|---|---|---|
|**POMLČKa v AMP**|desítkách|8s|
|**HLS v nativním přehrávači pro iOS**|14s|desítkách|

### <a name="live-encoding"></a>Kódování v reálném čase

||2S – skupinu GOP s povolenou nízkou latencí|1 skupinu GOP nízká latence povolena|
|---|---|---|
|**POMLČKa v AMP**|14s|desítkách|
|**HLS v nativním přehrávači pro iOS**|18s|13s|

> [!NOTE]
> Koncová latence se může lišit v závislosti na podmínkách místní sítě nebo při zavedení vrstvy mezipaměti CDN. Měli byste testovat přesnou konfiguraci.

## <a name="next-steps"></a>Další kroky

- [Přehled živého streamování](live-streaming-overview.md)
- [Kurz živého streamování](stream-live-tutorial-with-api.md)

