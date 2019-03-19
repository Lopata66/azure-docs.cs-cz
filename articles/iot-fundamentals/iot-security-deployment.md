---
title: Zabezpečení nasazení Azure Internet of Things (IoT) | Dokumentace Microsoftu
description: Tento článek podrobně popisuje, jak zabezpečit vaše nasazení Azure IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: b6a444e64e58611e36208cfa615b4a148de3596c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182672"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Sady šifer akcelerátorů řešení IoT

Akcelerátory řešení IoT se podporují následující šifrovací sada, v uvedeném pořadí.

| Šifrovací sada | Délka |
| --- | --- |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (ekvalizéru Služba FS 7680 bits RSA) |256 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (ekvalizéru Služba FS 3072 bits RSA) |128 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (ekvalizéru Služba FS 7680 bits RSA) |256 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (ekvalizéru Služba FS 3072 bits RSA) |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| Protokol TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Další informace najdete v tématech

Přečtěte si informace o zabezpečení služby IoT Hub v [řízení přístupu ke službě IoT Hub](../iot-hub/iot-hub-devguide-security.md) v příručce pro vývojáře IoT Hub. 
