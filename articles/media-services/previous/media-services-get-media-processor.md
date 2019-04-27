---
title: Jak vytvořit procesor médií pomocí Azure Media Services SDK pro .NET | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit komponentu procesor médií pro kódování, převod formátu, šifrování nebo dešifrování obsahu médií pro Azure Media Services. Ukázky kódu jsou napsané C# a používat Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463826"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postup: Získání instance procesoru médií
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Ve službě Media Services, které procesor médií je komponenta, která zpracovává konkrétní zpracování úloh, jako je například kódování formát převodu, šifrování nebo dešifrování obsahu médií. Obvykle vytvoříte procesor médií, při vytváření úlohy kódování, šifrování nebo převést formát mediálního obsahu.

## <a name="azure-media-processors"></a>Procesory médií Azure 

V následujícím tématu najdete seznam procesory médií:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Získat procesor médií

Následující metoda ukazuje, jak získat instanci procesoru médií. Příklad kódu předpokládá použití úrovni modulu proměnnou s názvem **_kontext** odkazovat kontextu serveru, jak je popsáno v části [jak: Připojení ke službě Media Services prostřednictvím kódu programu](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když víte, jak získat instanci procesor médií, přejděte [kódování Assetu](media-services-dotnet-encode-with-media-encoder-standard.md) tématu, kde se dozvíte, jak kódování prostředku pomocí Media Encoderu Standard.

