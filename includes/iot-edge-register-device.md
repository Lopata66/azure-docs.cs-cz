---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263614"
---
Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Zařízení IoT Edge se chovají jinak než typická zařízení IoT a jinak se i spravují, a proto je hned na začátku deklarujete jako zařízení IoT Edge. 

1. Na webu Azure Portal přejděte do svého centra IoT.
1. Vyberte **IoT Edge** a pak vyberte **Přidat zařízení IoT Edge**.

   ![Přidání zařízení IoT Edge](./media/iot-edge-register-device/add-device.png)

1. Zadejte jedinečné ID zařízení pro vaše simulované zařízení.
1. Výběrem **Uložit** přidejte své zařízení.
1. V seznamu zařízení vyberte své nové zařízení.
1. Zkopírujte hodnotu **Připojovací řetězec – primární klíč** a uložte ji. Tuto hodnotu použijete ke konfiguraci modulu runtime IoT Edge v další části. 

