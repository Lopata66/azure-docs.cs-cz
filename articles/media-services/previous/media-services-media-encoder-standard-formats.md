---
title: Formáty a kodeky Media Encoder Standard – Azure
description: Tento článek poskytuje přehled formátů Media Encoder Standard a kodeků.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: e2ccce13e4ef09426d0f3a02dcbce2f330b0ead8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251021"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Kodeky a standardní formáty kodéru médií

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 2](media-services-media-encoder-standard-formats.md)
> * [Verze 3](../latest/media-encoder-standard-formats.md)

Tento dokument obsahuje seznam nejběžnějších formátů souborů pro import a export, které můžete použít s Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejneru nebo souborů
| Formáty souborů (přípony souborů) | Podporuje se |
| --- | --- |
| FLV (s kodeky H.264 a AAC) (.flv) |Ano |
| MXF (.mxf) |Ano |
| GXF (.gxf) |Ano |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ano |
| Windows Media Video (WMV) a ASF (.wmv, .asf) |Ano |
| AVI (nekomprimovaný s 8bitovým nebo 10bitovým kódováním) (.avi) |Ano |
| MP4 (.mp4, .m4a, .m4v) a ISMV (.isma, .ismv) |Ano |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ano |
| Matroska a WebM (.mkv) |Ano |
| WAVE a WAV (.wav) |Ano |
| QuickTime (.mov) |Ano |

> [!NOTE]
> Výše uvedený seznam obsahuje běžnější přípony souborů. Media Encoder Standard podporuje mnoho dalších (například:. m2ts,. mpeg2video,. QT). Pokud se pokusíte soubor zakódovat a zobrazí se chybová zpráva s informacemi o tom, že formát není podporován, zadejte [svou zpětnou](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)vazbu.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formáty zvuku ve vstupních kontejnerech
Media Encoder Standard podporuje překonání následujících formátů zvuku ve vstupních kontejnerech:

* Soubory MXF, GXF a QuickTime, které obsahují zvukové stopy s prokládanými ukázkami stereo nebo 5,1

– nebo –

* Soubory MXF, GXF a QuickTime, kde se zvuk přenáší jako samostatné stopy PCM, ale mapování kanálů (na stereo nebo prostorový zvuk 5.1) je možné odvodit z metadat souboru

## <a name="input-video-codecs"></a>Vstupní kodeky videa
| Vstupní kodeky videa | Podporuje se |
| --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |
| Avid DNxHD (v souborech MXF) |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |
| Digitální video (DV) (v souborech AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |
| MPEG-1 |Ano |
| VC-1 a WMV9 |Ano |
| Canopus HQ a HQX |Ne |
| MPEG-4 Part 2 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| Nekomprimovaný formát YUV420 nebo Mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Apple ProRes 422 HQ |Ano |
| Apple ProRes Proxy |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H. 265| Hlavní a hlavní profily 10 (&#42;)<br/>Hlavní Podpora profilů pro 10 je určená pro obsah 8bit 4:2:0. |

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Podporuje se |
| --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |
| MPEG Layer 2 |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ano |
| Windows Media Audio |Ano |
| WAV a PCM |Ano |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (adaptivní s více rychlostmi) |Ano |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Dolby® Digital (AC3) |Ne |
| Dolby® Digital Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Formáty výstupu a kodeky
V následující tabulce jsou uvedeny kodeky a formáty souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně kontejnerů MP4 s více přenosovými rychlostmi) |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC V1, IT-AAC v2 |
| MPEG2 – TS |H. 264 (profily vysoké, hlavní a základní) |AAC-LC, HE-AAC V1, IT-AAC v2 |

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md)

[Postup při kódování pomocí Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

