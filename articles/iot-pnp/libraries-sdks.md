---
title: technologie Plug and Play knihovny a sady SDK pro IoT
description: Informace o knihovnách zařízení a služeb, které jsou k dispozici pro vývoj řešení s podporou IoT technologie Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3ff01455c56dd259ea64878f912eb3f2431547e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495248"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK pro IoT technologie Plug and Play

Knihovny technologie Plug and Play IoT a sady SDK umožňují vývojářům vytvářet řešení IoT s využitím nejrůznějších programovacích jazyků na různých platformách. Následující tabulka obsahuje odkazy na ukázky a rychlé starty, které vám pomůžou začít:

## <a name="device-sdks"></a>Sady SDK pro zařízení

| Jazyk | Balíček | Úložiště kódu | ukázky | Rychlý start | Referenční informace |
|---|---|---|---|---|---|
| C – zařízení | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [ukázky](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device.md) | [Odkaz](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – zařízení | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Připojení ke službě IoT Hub](quickstart-connect-device.md) | [Odkaz](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java – zařízení | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Připojení ke službě IoT Hub](quickstart-connect-device.md) | [Odkaz](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python – zařízení | [2.3.0 PIP](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device.md) | [Odkaz](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Uzel – zařízení | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device.md) | [Odkaz](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Vložené: C – zařízení | Není k dispozici | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [ukázky](howto-use-embedded-c.md#samples) | [Jak používat Embedded C](howto-use-embedded-c.md) | Není k dispozici

## <a name="service-sdks"></a>Sady SDK pro služby

| Platforma  | Balíček | Úložiště kódu | ukázky | Rychlý start | Referenční informace |
|---|---|---|---|---|---|
| .NET – IoT Hub služba | [1.27.1 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Není k dispozici | [Odkaz](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Služba IoT Hub Java | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Není k dispozici | [Odkaz](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Služba IoT Hub Node | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Není k dispozici | [Odkaz](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Služba Python-Digital s dvojitou čárkou | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service.md) | Není k dispozici |
| Služba nedigitálních vláken v uzlu | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service.md) | Není k dispozici |

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet sady SDK a knihovny, přečtěte si  [příručku pro vývojáře](concepts-developer-guide-device.md) a [rychlé starty zařízení](quickstart-connect-device.md) a [služby](quickstart-service.md).