---
title: Podpora Azure IoT Hub TLS
description: Osvědčené postupy při použití zabezpečených připojení TLS pro zařízení a služby komunikující s IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080608"
---
# <a name="tls-support-in-iot-hub"></a>Podpora TLS v IoT Hub

IoT Hub používá protokol TLS (Transport Layer Security) k zabezpečení připojení ze zařízení a služeb IoT. V současné době jsou podporovány tři verze protokolu TLS, a to verze 1,0, 1,1 a 1,2.

TLS 1,0 a 1,1 se považují za starší verze a plánuje se jejich vyřazení. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Při připojování k IoT Hub důrazně doporučujeme použít TLS 1,2 jako upřednostňovanou verzi protokolu TLS.

## <a name="tls-12-enforcement-available-in-select-regions"></a>V výběru oblastí je k dispozici vynucení TLS 1,2

Pro zvýšení zabezpečení nakonfigurujte centra IoT tak, aby povolovala *jenom* připojení klientů, která používají protokol TLS verze 1,2 a vynutila použití [doporučených šifr](#recommended-ciphers). Tato funkce je podporována pouze v těchto oblastech:

* USA – východ
* USA – středojih
* USA – západ 2
* USA (Gov) – Arizona
* USA (Gov) – Virginia

Pro tento účel zřiďte novou IoT Hub v některé z podporovaných oblastí a nastavte `minTlsVersion` vlastnost na `1.2` specifikaci prostředků služby IoT Hub pro Azure Resource Manager Template:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Vytvořený prostředek IoT Hub pomocí této konfigurace odmítne klienty zařízení a služeb, kteří se pokusí připojit pomocí protokolu TLS verze 1,0 a 1,1. Podobně platí, že pokud zpráva HELLo klienta neuvádí žádné [Doporučené šifry](#recommended-ciphers), dojde k odmítnutí handshake TLS.

> [!NOTE]
> `minTlsVersion`Vlastnost je určena jen pro čtení a nelze ji změnit po vytvoření prostředku IoT Hub. Proto je důležité, abyste správně otestovali a ověřili, že *všechna* vaše zařízení a služby IoT jsou kompatibilní s TLS 1,2 a předem [doporučenými šiframi](#recommended-ciphers) .
> 
> Po převzetí služeb při selhání `minTlsVersion` bude vlastnost IoT Hub v rámci převzetí služeb při selhání v geograficky spárovaném regionu platit.

## <a name="recommended-ciphers"></a>Doporučené šifry

Centra IoT, která jsou nakonfigurovaná tak, aby přijímala jenom TLS 1,2, taky vynutila použití následujících doporučených šifr:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Pro služby IoT Hub, které nejsou nakonfigurované pro vynucování TLS 1,2, bude TLS 1,2 dál fungovat s následujícími šiframi:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Použití TLS 1,2 v sadách IoT Hub SDK

Pomocí níže uvedených odkazů nakonfigurujte TLS 1,2 a povolená šifra v IoT Hub klientských sadách SDK.

| Jazyk | Verze podporující TLS 1,2 | Dokumentace |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 nebo novější            | [Odkaz](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Verze 2.0.0 nebo novější             | [Odkaz](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Verze 1.21.4 nebo novější            | [Odkaz](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Verze 1.19.0 nebo novější            | [Odkaz](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Verze 1.12.2 nebo novější            | [Odkaz](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Použití TLS 1,2 v nastavení IoT Edge

IoT Edge zařízení je možné nakonfigurovat tak, aby při komunikaci s IoT Hub používala TLS 1,2. Pro účely tohoto účelu použijte [stránku dokumentace IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).