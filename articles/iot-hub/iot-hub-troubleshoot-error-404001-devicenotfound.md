---
title: Řešení potíží s chybami Azure IoT Hub 404001 DeviceNotFound
description: Vysvětlení, jak opravit chybu 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960825"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Tento článek popisuje příčiny a řešení 404001 chyb **DeviceNotFound** .

## <a name="symptoms"></a>Příznaky

Během komunikace typu cloud-zařízení (C2D), jako je například zpráva C2D, dvojitá aktualizace nebo přímá metoda, operace selže s chybou **404001 DeviceNotFound**.

## <a name="cause"></a>Příčina

Operace se nezdařila, protože zařízení nelze najít pomocí IoT Hub. Zařízení buď není zaregistrované, nebo je zakázané.

## <a name="solution"></a>Řešení

Zaregistrujte ID zařízení, které jste použili, a pak to zkuste znovu.