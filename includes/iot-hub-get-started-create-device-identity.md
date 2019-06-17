---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b2bce9788006a564def9bd8c1375a85dc4184b67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814801"
---
## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části použijete rozhraní příkazového řádku Azure k vytvoření identity zařízení pro účely tohoto kurzu. Rozhraní příkazového řádku Azure je předinstalován v [Azure Cloud Shell](~/articles/cloud-shell/overview.md), nebo můžete [nainstalovat Azure CLI místně](/cli/azure/install-azure-cli). V ID zařízení se rozlišují malá a velká písmena.

1. Spusťte následující příkaz prostředí příkazového řádku, kde jsou k instalaci rozšíření IoT pomocí Azure CLI:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Pokud používáte Azure CLI místně, použijte následující příkaz pro přihlášení ke svému účtu Azure (Pokud používáte Cloud Shell, jste přihlášení automaticky a není nutné ke spuštění tohoto příkazu):

    ```cmd/sh
    az login
    ```

1. Nakonec vytvořte novou identitu zařízení volá `myDeviceId` a načíst připojovací řetězec zařízení pomocí následujících příkazů:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Poznamenejte si připojovací řetězec zařízení z výsledku. Tento připojovací řetězec zařízení používá aplikace pro zařízení pro připojení ke službě IoT Hub jako zařízení.

<!-- images and links -->
