---
title: Rychlý start k řízení zařízení ze služby Azure IoT Hub (Python) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Python. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 13652b287da94adff5bdf2235900734e5908c56f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516649"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Rychlý start: Řízení zařízení připojeného k centru IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která umožňuje spravovat zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu za účelem uložení nebo zpracování. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem napsané aplikace Python:

* Aplikaci simulovaného zařízení, která odpovídá na přímé metody volané z back-endové aplikace. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.

* Back-endovou aplikaci, která na simulovaném zařízení volá přímé metody. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu centra IoT na straně služby.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pokud jste to ještě neudělali, stáhněte si ukázkový projekt Python z webu https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-python.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-python.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    **MyPythonDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyPythonDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste si poznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení po provedení přímé metody pošle potvrzení zpátky do vašeho centra.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.py**.

    Nahraďte hodnotu `CONNECTION_STRING` proměnné pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve. Pak změny uložte do **SimulatedDevice.py**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Spuštěním následujících příkazů v okně místního terminálu spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace umožňuje přímé volání metod do zařízení prostřednictvím služby IoT Hub a naslouchá potvrzením. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application**.

1. V libovolném textovém editoru otevřete soubor **BackEndApplication.py**.

    Nahraďte hodnotu proměnné `CONNECTION_STRING` připojovacím řetězcem služby, který jste si poznamenali dříve. Pak změny uložte do **BackEndApplication.py**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Spuštěním následujících příkazů v okně místního terminálu spusťte back-endovou aplikaci:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém aplikace představuje přímé volání metody do zařízení a přijímá potvrzení:

    ![Spuštění back-endové aplikace](./media/quickstart-control-device-python/BackEndApplication.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Změna simulovaného klienta](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpovídali na volání přímé metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)