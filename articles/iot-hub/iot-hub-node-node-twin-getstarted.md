---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (Node) | Dokumentace Microsoftu
description: Jak používat dvojče zařízení Azure IoT Hub pro přidání značek a následné použití k dotazu služby IoT Hub. Implementace aplikace simulovaného zařízení a app service, které přidá značky a spustí dotaz služby IoT Hub pomocí sad Azure IoT SDK pro Node.js.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: f93abac563d47f6505f42d29e882698ef31174bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795881"
---
# <a name="get-started-with-device-twins-node"></a>Začínáme s dvojčaty zařízení (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Node.js:

* **AddTagsAndQuery.js**, back endové aplikace Node.js, které přidá značky a dotazy dvojčata zařízení.

* **TwinSimulatedDevice.js**, aplikace v Node.js, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Tento článek [sad SDK Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.
>

K dokončení tohoto kurzu budete potřebovat následující:

* Node.js verze 4.0.x nebo novější.

* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Node.js, která přidává umístění metadat do dvojčete zařízení spojené s **myDeviceId**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub výběrem umístěné v USA a ty, které vykazují mobilní připojení zařízení.

1. Vytvořte novou prázdnou složku s názvem **addtagsandqueryapp**. V **addtagsandqueryapp** složku, vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```
    npm init
    ```

2. Na příkazovém řádku v **addtagsandqueryapp** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku:
   
    ```
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte nový **AddTagsAndQuery.js** soubor **addtagsandqueryapp** složky.

4. Přidejte následující kód, který **AddTagsAndQuery.js** soubor a nahradit **{připojovacího řetězce centra iot}** zástupného symbolu připojovacím řetězcem služby IoT Hub jste zkopírovali, když vytvoříte centrum:

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Registru** objekt poskytuje všechny metody, které jsou nutné k interakci s dvojčaty zařízení ze služby. Předchozí kód nejprve inicializuje **registru** a pak načte dvojče zařízení pro **myDeviceId**a nakonec aktualizuje její klíčová slova s informacemi o požadované umístění.

    Po aktualizaci značky, které volá **queryTwins** funkce.

5. Přidejte následující kód na konci **AddTagsAndQuery.js** k implementaci **queryTwins** funkce:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Předchozí kód provede dva dotazy: první vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** kategorie plant a druhá zpřesnění dotaz pro výběr pouze do zařízení, které jsou také připojené přes mobilní síť.

    Předchozí kód, když vytváří **dotazu** objektu, určuje maximální počet vrácených dokumentů. **Dotazu** obsahuje objekt **hasMoreResults** logická vlastnost, která můžete použít k vyvolání **nextAsTwin** metody víc než jednou načíst všechny výsledky. Volána metoda **Další** je k dispozici pro výsledky, které není dvojčata zařízení, třeba výsledky dotazů agregace.

6. Spusťte aplikaci:

    ```
        node AddTagsAndQuery.js
    ```

   Měli byste vidět jedno zařízení ve výsledcích pro kladení dotazů pro všechna zařízení nachází v **Redmond43** a žádné dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.
   
    ![Zařízení zobrazí v jedné výsledky dotazu](media/iot-hub-node-node-twin-getstarted/service1.png)

V další části můžete vytvořit aplikace pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která se připojuje k vašemu centru jako **myDeviceId**a pak aktualizuje jeho dvojče zařízení uživatele ohlášené vlastnosti k obsahují informace, že je připojený pomocí mobilní síti.

1. Vytvořte novou prázdnou složku s názvem **reportconnectivity**. V **reportconnectivity** složku, vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```

2. Na příkazovém řádku v **reportconnectivity** složky, spusťte následující příkaz k instalaci **azure-iot-device**, a **azure-iot-device-mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte nový **ReportConnectivity.js** soubor **reportconnectivity** složky.

4. Přidejte následující kód, který **ReportConnectivity.js** soubor a nahradit **{připojovací řetězec zařízení}** zástupného symbolu připojovacím řetězcem zařízení jste zkopírovali, když jste vytvořili **myDeviceId** identity zařízení:

    ```
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    **Klienta** objekt poskytuje všechny metody, které budete potřebovat pro interakci s dvojčaty zařízení ze zařízení. Předchozí kód, až se inicializuje **klienta** objekt, načte dvojče zařízení pro **myDeviceId** a jeho ohlášených vlastností aktualizuje informace o připojení.

5. Spuštění aplikace pro zařízení

    ```   
        node ReportConnectivity.js
    ```

    Zobrazí se zpráva `twin state reported`.

6. Teď, když zařízení uvádět jeho informace o připojení, měl by se zobrazit v obou dotazech. Přejděte zpět **addtagsandqueryapp** složky a znovu spusťte dotazy:

    ```   
        node AddTagsAndQuery.js
    ```

    Tentokrát **myDeviceId** by se měla objevit v obou výsledky dotazu.

    ![Zobrazit myDeviceId v obou výsledky dotazu](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidá metadata zařízení jako značky z back endové aplikace a aplikace s Simulovaná zařízení zapsáno do sestavy informací o připojení k zařízení ve dvojčeti zařízení. Také jste zjistili, jak zadávat dotazy na tyto informace pomocí dotazovacího jazyka podobného SQL IoT Hub.

Pomocí následujících zdrojích se dozvíte jak:

* Odesílání telemetrie ze zařízení s [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurzu

* Konfigurace zařízení pomocí požadované vlastnosti dvojčete zařízení s [použití požadované vlastnosti ke konfiguraci zařízení](tutorial-device-twins.md) kurzu

* Kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem), se [použití přímých metod](quickstart-control-device-node.md) kurzu.