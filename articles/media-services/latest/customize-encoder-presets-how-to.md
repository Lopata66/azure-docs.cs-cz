---
title: Kódování vlastní transformace pomocí Media Services V3 .NET – Azure | Microsoft Docs
description: V tomto tématu se dozvíte, jak použít Azure Media Services V3 ke kódování vlastní transformace pomocí .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80068033"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Jak kódovat pomocí vlastní transformace – .NET

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených integrovaných přednastavení na základě doporučených osvědčených postupů, jak je znázorněno v kurzu [streamování souborů](stream-files-tutorial-with-api.md) . Můžete také vytvořit vlastní předvolby, která bude cílit na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Důležité informace

Při vytváření vlastních přednastavení platí následující požadavky:

* Všechny hodnoty pro výšku a šířku v obsahu AVC musí být násobkem 4.
* V Azure Media Services V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi rozhraními API v2, která jako jednotku používala kilobity za sekundu. Pokud je například přenosová rychlost v v2 zadaná jako 128 (kilobit/s), ve verzi V3 by se nastavila na 128000 (bity za sekundu).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Stažení ukázky

Naklonujte úložiště GitHub, které obsahuje úplný vzorek .NET Core pro váš počítač, pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Ukázka vlastní předvolby se nachází ve složce [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) .

## <a name="create-a-transform-with-a-custom-preset"></a>Vytvoření transformace pomocí vlastní předvolby 

Když vytváříte novou [transformaci](https://docs.microsoft.com/rest/api/media/transforms), je potřeba určit, co má vytvořit jako výstup. Objekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Přednastavení** popisuje podrobné pokyny k operacím zpracování videa nebo zvuku, které se mají použít ke generování požadovaných **TransformOutput**. Následující **TransformOutput** vytvoří vlastní kodek a výstupní nastavení vrstvy.

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže). V Media Services V3 vrátí metody **Get** v entitách **hodnotu null** , pokud entita neexistuje (u názvu se nerozlišuje malá a velká písmena).

### <a name="example"></a>Příklad

Následující příklad definuje sadu výstupů, které chceme vygenerovat při použití této transformace. Nejprve přidáme AacAudio vrstvu pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřadíme popisky, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup zahrnoval také miniatury. V následujícím příkladu určíme obrázky ve formátu PNG vygenerované v 50% rozlišení vstupního videa a tři časová razítka – {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát pro výstupní soubory – jeden pro video a zvuk a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, která vytvoří jedinečné názvy na každou vrstvu. Můžeme buď použít `{Label}` `{Bitrate}` makro nebo, v příkladu se zobrazí předchozí.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md) 
