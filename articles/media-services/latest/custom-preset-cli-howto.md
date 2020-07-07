---
title: Kódování vlastní transformace pomocí Media Services V3 Azure CLI | Microsoft Docs
description: V tomto tématu se dozvíte, jak použít Azure Media Services V3 ke kódování vlastní transformace pomocí rozhraní příkazového řádku Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80382949"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Jak kódovat pomocí vlastní transformace – Azure CLI

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených integrovaných přednastavení, a to na základě doporučených postupů v oboru, jak je znázorněno v rychlém startu [souborů streamování](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) . Můžete také vytvořit vlastní předvolby, která bude cílit na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Důležité informace

Při vytváření vlastních přednastavení platí následující požadavky:

* Všechny hodnoty pro výšku a šířku v obsahu AVC musí být násobkem 4.
* V Azure Media Services V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi rozhraními API v2, která jako jednotku používala kilobity za sekundu. Pokud je například přenosová rychlost v v2 zadaná jako 128 (kilobit/s), ve verzi V3 by se nastavila na 128000 (bity za sekundu).

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](create-account-cli-how-to.md).

Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definování vlastní předvolby

Následující příklad definuje tělo žádosti o novou transformaci. Definujeme sadu výstupů, které chceme vygenerovat při použití této transformace.

V tomto příkladu jsme nejdřív přidali vrstvu AacAudio pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřadíme popisky, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup zahrnoval také miniatury. V následujícím příkladu určíme obrázky ve formátu PNG vygenerované v 50% rozlišení vstupního videa a tři časová razítka – {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát pro výstupní soubory – jeden pro video a zvuk a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, která vytvoří jedinečné názvy na každou vrstvu. Můžeme buď použít `{Label}` `{Bitrate}` makro nebo, v příkladu se zobrazí předchozí.

Tuto transformaci uložíme do souboru. V tomto příkladu pojmenujte soubor `customPreset.json` .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Vytvořit novou transformaci  

V tomto příkladu vytvoříme **transformaci** , která je založena na vlastní předvolbě, které jsme definovali dříve. Při vytváření transformace byste nejdřív měli zjistit, jestli už neexistuje. Pokud transformace existuje, znovu ji použijte. Následující `show` příkaz vrátí `customTransformName` transformaci, pokud existuje:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Následující příkaz rozhraní příkazového řádku Azure vytvoří transformaci na základě vlastního přednastavení (definovaného dříve).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Pokud Media Services chcete použít transformaci na zadané video nebo zvuk, musíte úlohu v rámci této transformace odeslat. Kompletní příklad, který ukazuje, jak odeslat úlohu v transformaci, najdete v tématu [rychlý Start: Stream video soubory – Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Viz také:

[Azure CLI](/cli/azure/ams)
