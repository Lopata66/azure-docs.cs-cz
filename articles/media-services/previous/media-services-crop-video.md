---
title: Jak oříznout videa pomocí Media Encoder Standard – Azure | Microsoft Docs
description: Tento článek ukazuje, jak oříznout videa pomocí Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 03d68cc3a60abba8b7189a9d03fbc21d7606f736
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "69016615"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Oříznutí videa pomocí kodéru Media Encoder Standard  

K oříznutí vstupního videa můžete použít Media Encoder Standard (status národního prostředí). Oříznutí je proces výběru obdélníkového okna v rámci videa a kódování pouze pixelů v rámci tohoto okna. Následující diagram vám pomůže tento proces znázornit.

![Oříznout video](./media/media-services-crop-video/media-services-crop-video01.png)

Předpokládejme, že jako vstupní video máte rozlišení 1080 pixelů (poměr stran 16:9), ale v levé a pravé straně mají černé pruhy (pole pilíře), takže aktivní video obsahuje pouze 4:3 Window nebo 1440x1080 pixelů. Pomocí nástroje pro úpravy pozadí můžete oříznout nebo upravit černé pruhy a zakódovat oblast 1440x1080.

Oříznutí v části národního odinstalování je předzpracovaná fáze, takže parametry oříznutí v předplatném kódování se vztahují na původní vstupní video. Kódování je následná fáze a nastavení šířky a výšky se vztahuje na *předem zpracované* video, nikoli na původní video. Při navrhování přednastavených možností je třeba provést následující: (a) vybrat parametry oříznutí na základě původního vstupního videa a (b) vybrat nastavení kódování na základě oříznutého videa. Pokud nesouhlasíte s nastavením kódování na oříznuté video, nebude výstup odpovídat vašemu očekávání.

[Následující](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) téma ukazuje, jak vytvořit úlohu kódování pomocí nástroje pro vytváření a určení vlastní předvolby pro úlohu kódování. 

## <a name="creating-a-custom-preset"></a>Vytvoření vlastní předvolby
V příkladu zobrazeném v diagramu:

1. Původní vstup je 1080
2. Je nutné ji oříznout na výstup 1440x1080, který je zarovnán na střed vstupního rámce.
3. To znamená, že posun X je (1920 – 1440)/2 = 240 a posun Y nula.
4. Šířka a výška obdélníku oříznutí jsou 1440 a 1080, v uvedeném pořadí.
5. Ve fázi kódování se požádá o vyprodukování tří vrstev, jedná se o řešení 1440x1080, 960x720 a 480x360, v uvedeném pořadí.

### <a name="json-preset"></a>Přednastavení JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="restrictions-on-cropping"></a>Omezení pro ořezávání
Funkce oříznutí by měla být ruční. Měli byste načíst vstupní video do vhodného nástroje pro úpravy, který vám umožní vybrat snímky zájmu, umístit kurzor k určení posunutí pro obdélník oříznutí, aby se určilo přednastavení kódování, které se pro konkrétní video vyladěno atd. Tato funkce není určená k tomu, aby umožňovala například: automatické zjišťování a odstraňování černých Letterbox/pillarbox ohraničení ve vstupním videu.

Následující omezení platí pro funkci oříznutí. Pokud nejsou splněny, úloha kódování může selhat nebo získat Neočekávaný výstup.

1. Souřadnice a velikost obdélníku oříznutí se musí vejít do vstupního videa.
2. Jak je uvedeno výše, Šířka & Výška v nastavení kódování musí odpovídat oříznutému videu.
3. Oříznutí se vztahuje na videa zachycená v režimu na šířku (tj. neplatí pro videa zaznamenaná přes smartphone, která se podrží svisle nebo v režimu na výšku).
4. Funguje nejlépe s progresivním videem zachyceným čtvercovým pixelem.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
Informace o skvělých funkcích, které nabízí AMS, najdete v tématu Azure Media Services výukové cesty.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
