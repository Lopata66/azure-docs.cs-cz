---
title: Jak vytvořit procesor médií pomocí sady Azure Media Services SDK pro .NET | Microsoft Docs
description: Naučte se vytvořit komponentu procesoru médií ke kódování, převedení formátu, šifrování nebo dešifrování mediálního obsahu pro Azure Media Services. Ukázky kódu jsou napsané v jazyce C# a používají sadu Media Services SDK pro .NET.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "61463826"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postupy: získání instance procesoru multimédií
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
V Media Services procesor médií je komponenta, která zpracovává konkrétní úlohu zpracování, jako je například kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Procesor médií se obvykle vytváří při vytváření úlohy pro kódování, šifrování nebo převod formátu mediálního obsahu.

## <a name="azure-media-processors"></a>Procesory médií Azure 

V následujícím tématu najdete seznam procesorů médií:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Získat procesor médií

Následující metoda ukazuje, jak získat instanci procesoru médií. Příklad kódu předpokládá použití proměnné na úrovni modulu s názvem **_context** pro odkazování na kontext serveru, jak je popsáno v části [Postupy: připojení k Media Services programově](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Když teď víte, jak získat instanci procesoru médií, přečtěte si téma [Postup kódování assetu](media-services-dotnet-encode-with-media-encoder-standard.md) , ve kterém se dozvíte, jak použít Media Encoder Standard ke kódování assetu.

