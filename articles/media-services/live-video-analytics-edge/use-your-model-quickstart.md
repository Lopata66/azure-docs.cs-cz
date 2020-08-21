---
title: Analýza živého videa pomocí vlastního modelu HTTP – Azure
description: V tomto rychlém startu použijete počítačovou vizi k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 5fd7f1a7db323e3aa7971c1ced6fc588d6e60e37
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684165"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Rychlý Start: Analýza živého videa pomocí vlastního modelu HTTP

V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak použít model počítačové vize k detekci objektů. Do odvozené služby se pošle podmnožina snímků v živém obrazovém kanálu. Výsledky se odesílají do centra IoT Edge. 

Tento rychlý Start používá virtuální počítač Azure jako zařízení IoT Edge a používá simulovaný živý Stream videa. Vychází z ukázkového kódu napsaného v jazyce C# a sestavuje se v rychlém startu pro [detekci pohybů a generování událostí](detect-motion-emit-events-quickstart.md) . 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Předpoklady

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Přehled

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretace výsledků

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet další rychlé starty, ponechte prostředky, které jste vytvořili. V opačném případě přejdete na Azure Portal, přejdete do skupin prostředků, vyberete skupinu prostředků, ve které jste spustili tento rychlý Start, a odstraníte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte [zabezpečenou verzi modelu YoloV3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) a nasaďte ji do zařízení IoT Edge. 

Přečtěte si další výzvy pro pokročilé uživatele:

* Místo používání simulátoru RTSP použijte [kameru IP](https://en.wikipedia.org/wiki/IP_camera) , která má podporu pro RTSP. Můžete vyhledat kamery protokolu IP, které podporují protokol RTSP na stránce ONVIF, která je v [souladu](https://www.onvif.org/conformant-products/) s těmito produkty. Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
* Místo virtuálního počítače Azure Linux použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Můžete postupovat podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Pak zařízení Zaregistrujte v Azure IoT Hub podle pokynů v tématu [nasazení prvního modulu IoT Edge na zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md).
