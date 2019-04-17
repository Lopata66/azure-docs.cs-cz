---
title: Plánování úloh s Azure IoT Hub (Python) | Dokumentace Microsoftu
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody v různá zařízení. Implementace služby aplikace na spuštění úlohy a aplikace simulovaného zařízení pomocí sady Azure IoT SDK pro Python.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608805"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Úlohy vysílání a plánování (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back endové aplikace vytvořit a sledovat úlohy, které naplánovat a aktualizovat miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání přímých metod

Úloha koncepčně, zabalí jednu z těchto akcí a sleduje průběh provádění sady zařízení, která je definovaná dotazem dvojčete zařízení.  Například back endové aplikace úlohu můžete použít k vyvolání metody restartu na 10 000 zařízení, vybraných podle dotazu dvojče zařízení a naplánované na budoucí dobu.  Tuto aplikaci pak můžete sledovat průběh každé z těchto zařízení přijímat a proveďte metodu restartování.

Další informace o každé z těchto funkcí v těchto článcích:

* Dvojče zařízení a vlastnosti: [Začínáme s dvojčaty zařízení](iot-hub-python-twin-getstarted.md) a [kurzu: Jak používat vlastnosti dvojčat zařízení](tutorial-device-twins.md)

* Přímé metody: [Příručka pro vývojáře IoT Hub - přímých metod](iot-hub-devguide-direct-methods.md) a [kurz: přímé metody](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení v Pythonu, který má přímé metody, která umožňuje **lockDoor**, které je možné vyvolat v back-endu řešení.

* Vytvoření konzolové aplikace v Pythonu, který volá **lockDoor** přímé metody v aplikaci simulovaného zařízení pomocí úlohy a aktualizace požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu budete mít dvě aplikace v Pythonu:

**simDevice.py**, která se připojuje ke službě IoT hub s identitou zařízení a přijímá **lockDoor** přímá metoda.

**scheduleJobService.py**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení a aktualizace dvojčete zařízení požadované vlastnosti pomocí úlohy.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).

* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145).

* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

> [!NOTE]
> **Azure IoT SDK pro Python** přímo nepodporuje **úlohy** funkce. Místo toho tento kurz nabízí alternativní řešení využívající asynchronní vláken a časovače. Další aktualizace, najdete **klientské sady SDK služby** seznamu funkcí na [Azure IoT SDK pro Python](https://github.com/Azure/azure-iot-sdk-python) stránky. 
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která bude reagovat na přímou metodu volanou cloudem, který aktivuje Simulovaná **lockDoor** metody.

1. Na příkazovém řádku spusťte následující příkaz k instalaci **azure-iot-device-client** balíčku:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Pomocí textového editoru vytvořte nový **simDevice.py** soubor ve svém pracovním adresáři.

3. Přidejte následující `import` příkazy a proměnné na začátku **simDevice.py** souboru. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení, které jste vytvořili výše:

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující funkci zpětného volání pro zpracování **lockDoor** metody:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Přidáte další funkce zpětného volání pro zpracování aktualizace dvojčat zařízení:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Přidejte následující kód pro obslužnou rutinu pro registraci **lockDoor** metody. Také `main` rutinu:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. Uložte a zavřete **simDevice.py** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plánování úloh pro volání přímé metody a aktualizují se vlastnosti dvojčete zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, které vyvolává vzdálené **lockDoor** na zařízení pomocí přímé metody a aktualizovat vlastnosti dvojčete zařízení.

1. Na příkazovém řádku spusťte následující příkaz k instalaci **azure-iot-service-client** balíčku:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. Pomocí textového editoru vytvořte nový **scheduleJobService.py** soubor ve svém pracovním adresáři.

3. Přidejte následující `import` příkazy a proměnné na začátku **scheduleJobService.py** souboru:

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Přidejte následující funkci, která se používá k dotazování pro zařízení:

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. Přidejte následující metody můžete spouštět úlohy, které volají dvojčeti přímé metody a zařízení:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Přidejte následující kód k plánování úloh a aktualizujte stav úlohy. Také `main` rutinu:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Uložte a zavřete **scheduleJobService.py** souboru.

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve vašem pracovním adresáři spusťte následující příkaz, který zahájit naslouchání pro přímé metody restartování:

    ```cmd/sh
    python simDevice.py
    ```

2. Jiné příkazového řádku ve vašem pracovním adresáři spusťte následující příkaz k aktivaci úlohy, které mají zamknout dveře a aktualizovat dvojčeti:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Zobrazí zařízení odpovědi na přímé metody a dvojčata zařízení aktualizaci v konzole.

    ![Příklad 1 – výstupní zařízení IoT Hub Job](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Výstup ukázkové 2 – zařízení IoT Hub Job](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu používají úlohy k plánování přímé metody, která zařízení a aktualizace vlastnosti dvojčete zařízení.

Chcete-li pokračovat v seznamování se službou IoT Hub a schémata správy zařízení jako vzdálené přes aktualizace firmwaru air, přečtěte si téma [jak provést upgrade firmwaru](tutorial-firmware-update.md).