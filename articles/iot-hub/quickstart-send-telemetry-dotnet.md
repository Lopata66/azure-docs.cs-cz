---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (C#) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C#, které odesílají simulovaná telemetrická data do centra IoT a čtou telemetrická data z centra IoT, aby se mohla zpracovávat v cloudu.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: c8bfb159dc56ff701f8d3c7eff00f04e28f8704a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667822"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Rychlý start: Posílání telemetrie ze zařízení do služby IoT Hub a jejich čtení pomocí back-endové aplikace (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se používají dvě předem napsané aplikace C#, jedna k odesílání telemetrických dat a jedna ke čtení telemetrických dat z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Na počítači používaném pro vývoj musíte mít .NET Core SDK 2.1.0 nebo vyšší.

Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
dotnet --version
```

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stáhněte si ukázkový projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

   **MyDotnetDevice**: Název zařízení, které se chystáte registrovat. Použijte **MyDotnetDevice** , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, budete ho muset použít v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDotnetDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDotnetDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. K povolení back-endové aplikace pro připojení ke službě IoT Hub a k načtení zpráv budete potřebovat taky _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_a _primární klíč služby_ z vašeho centra IoT. Následující příkazy načtou tyto hodnoty pro centrum IoT:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name service --query primaryKey --hub-name YourIoTHubName
    ```

    Tyto tři hodnoty si poznamenejte, použijete je později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

2. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    Hodnotu proměnné `s_connectionString` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Změny pak uložte do souboru **SimulatedDevice.cs**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujícího příkazu v okně místního terminálu sestavte a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Back-endová aplikace se připojí ke koncovému bodu **Events** na straně služby ve vašem centru IoT. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Potom přejděte ke složce **iot-hub\Quickstarts\read-d2c-messages**.

2. V libovolném textovém editoru otevřete soubor **ReadDeviceToCloudMessages.cs**. Aktualizujte následující proměnné a uložte provedené změny souboru.

    | Proměnná | Hodnota |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Hodnotu proměnné nahraďte koncovým bodem kompatibilním s Event Hubs, který jste si předtím poznamenali. |
    | `s_eventHubsCompatiblePath`     | Hodnotu proměnné nahraďte cestou kompatibilní s Event Hubs, kterou jste si předtím poznamenali. |
    | `s_iotHubSasKey`                | Nahraďte hodnotu proměnné primárním klíčem služby, na který jste si poznamenali dříve. |

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro back-endovou aplikaci:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujících příkazů v okně místního terminálu sestavte a spusťte back-endovou aplikaci:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco back-endová aplikace přijímá telemetrická data odeslaná simulovaným zařízením do centra:

    ![Spuštění back-endové aplikace](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace C# a přečetli telemetrická data z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Řízení zařízení připojeného k centru IoT](quickstart-control-device-dotnet.md)