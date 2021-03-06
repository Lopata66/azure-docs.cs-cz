---
title: Ingestování telemetrie z IoT Hubu
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ingestovat zprávy telemetrie zařízení z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051084"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingestování IoT Hub telemetrie do digitálních vláken Azure

Digitální vlákna Azure se řídí daty ze zařízení IoT a dalších zdrojů. Běžný zdroj dat zařízení, která se mají používat v rámci digitálních vláken Azure, je [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pro ingestování dat do digitálních vláken Azure je nastavení externího výpočetního prostředku, jako je například funkce, kterou provedete pomocí [Azure Functions](../azure-functions/functions-overview.md). Funkce přijme data a pomocí [rozhraní DigitalTwins API](/rest/api/digital-twins/dataplane/twins) nastaví vlastnosti nebo události telemetrie pro [digitální vlákna](concepts-twins-graph.md) . 

Tento postup popisuje, jak dokumentovat pomocí procesu vytváření funkce, která může ingestovat telemetrie z IoT Hub.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v tomto příkladu, budete muset nastavit následující prostředky jako požadavky:
* **Centrum IoT**. Pokyny najdete v části *vytvoření IoT Hub* [tohoto IoT Hub rychlé](../iot-hub/quickstart-send-telemetry-cli.md)spuštění.
* **Funkce** se správnými oprávněními pro volání vaší digitální zdvojené instance. Pokyny najdete v tématu [*Postup: nastavení funkce v Azure pro zpracování dat*](how-to-create-azure-function.md). 
* **Instance digitálního vlákna Azure** , která bude přijímat telemetrii zařízení. Pokyny najdete v tématu [*Postup: nastavení instance a ověřování digitálních vláken Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Ukázkový scénář telemetrie

Tento postup popisuje, jak odesílat zprávy z IoT Hub do digitálních vláken Azure pomocí funkce v Azure. K dispozici je celá řada možných konfigurací a vyhovujících strategií, které můžete použít pro posílání zpráv, ale příklad tohoto článku obsahuje následující části:
* Zařízení teploměr v IoT Hub se známým ID zařízení
* Digitální vlákna představující zařízení s ID odpovídajícího

> [!NOTE]
> V tomto příkladu se používá jednoznačné ID mezi ID zařízení a odpovídajícím ID digitálního vlákna, ale je možné poskytnout propracovanější mapování ze zařízení na jeho dvojitou hodnotu (například s tabulkou mapování).

Pokaždé, když je událost telemetrie teploty odeslána zařízením termostatu, funkce zpracuje telemetrii a vlastnost *teploty* digitálního vlákna by měla aktualizovat. Tento scénář je popsaný v diagramu níže:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce v Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje." border="false":::

## <a name="add-a-model-and-twin"></a>Přidání modelu a vlákna

Model můžete přidat nebo nahrát pomocí příkazu CLI níže a pak vytvořit dvojitou hodnotu pomocí tohoto modelu, který se bude aktualizovat informacemi z IoT Hub.

Model vypadá takto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pokud chcete **Tento model nahrát do instance vláken**, otevřete Azure CLI a spusťte následující příkaz:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Pak budete muset **vytvořit jeden z těchto vláken pomocí tohoto modelu**. Pomocí následujícího příkazu vytvořte dvojitou hodnotu a nastavte 0,0 jako počáteční hodnotu teploty.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

Výstup úspěšného zdvojeného příkazu CREATE by měl vypadat takto:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Vytvoření funkce

V této části se používají stejné kroky při spuštění a kostra funkcí sady Visual Studio z tématu [*Postupy: nastavení funkce pro zpracování dat*](how-to-create-azure-function.md). Kostra zpracovává ověřování a vytváří klienta služby, který je připravený na zpracování dat a volání rozhraní API digitálních vláken Azure v reakci. 

V následujících krocích přidáte konkrétní kód pro zpracování událostí telemetrie IoT z IoT Hub.  

### <a name="add-telemetry-processing"></a>Přidat zpracování telemetrie
    
Události telemetrie přicházejí do formy zpráv ze zařízení. Prvním krokem při přidávání kódu pro zpracování telemetrie je extrakce příslušné části této zprávy zařízení z události Event Grid. 

Různá zařízení mohou strukturovat své zprávy různě, takže kód pro **Tento krok závisí na připojeném zařízení.** 

Následující kód ukazuje příklad jednoduchého zařízení, které odesílá telemetrii jako JSON. Tato ukázka je plně proprozkoumána v [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Následující kód vyhledá ID zařízení, které zprávu odeslalo, a také hodnotu teploty.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

Další ukázka kódu vezme ID a teplotu a použije je k opravě (provedení aktualizací), které jsou vytvářené.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>Aktualizace kódu funkce

Teď, když rozumíte kódu z předchozích ukázek, otevřete funkci z oddílu [*požadavky*](#prerequisites) v aplikaci Visual Studio. (Pokud nemáte funkci vytvořenou v Azure, navštivte odkaz v části požadavky a vytvořte si ho hned teď).

Nahraďte kód vaší funkce tímto ukázkovým kódem.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Uložte kód funkce a publikujte aplikaci Function App do Azure. Informace o tom, jak najdete v tématu [*publikování aplikace Function App*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) , [*jak v Azure nastavit funkci pro zpracování dat*](how-to-create-azure-function.md).

Po úspěšném publikování se zobrazí výstup v okně příkazového řádku sady Visual Studio, jak je znázorněno níže:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
Můžete také ověřit stav procesu publikování v [Azure Portal](https://portal.azure.com/). Vyhledejte _skupinu prostředků_ , přejděte do _protokolu aktivit_ a vyhledejte _profil publikování webové aplikace_ v seznamu a ověřte, jestli je stav úspěšný.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Snímek obrazovky Azure Portal, který zobrazuje stav procesu publikování.":::

## <a name="connect-your-function-to-iot-hub"></a>Připojte funkci k IoT Hub

Nastavte cíl události pro data centra.
V [Azure Portal](https://portal.azure.com/)přejděte na instanci IoT Hub, kterou jste vytvořili v části [*požadavky*](#prerequisites) . V části **události** Vytvořte předplatné pro vaši funkci.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Snímek obrazovky Azure Portal, který ukazuje přidání odběru události":::

Na stránce **vytvořit odběr události** vyplňte pole následujícím způsobem:
  1. V části **název** zadejte název předplatného, co byste chtěli.
  2. V části **schéma události** vyberte _Event Grid schéma_.
  3. V části **typy událostí** vyberte zaškrtávací políčko _telemetrie zařízení_ a zrušte zaškrtnutí ostatních typů událostí.
  4. V části **Typ koncového bodu** vyberte _Azure Function_.
  5. V části **koncový bod** zvolte _možnost vybrat odkaz na koncový bod_ a vytvořte koncový bod.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Snímek obrazovky Azure Portal k vytvoření podrobností odběru události":::

Na stránce _Vybrat funkci Azure_ , která se otevře, ověřte níže uvedené podrobnosti.
 1. **Předplatné:** Vaše předplatné Azure.
 2. **Skupina prostředků**: vaše skupina prostředků
 3. **Aplikace Function App**: název vaší aplikace Function App
 4. **Slot**: _Výroba_
 5. **Funkce**: vyberte funkci z rozevíracího seznamu.

Kliknutím na tlačítko _potvrdit výběr_ uložte podrobnosti.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Snímek obrazovky Azure Portal pro výběr funkce":::

Vyberte _vytvořit_ a vytvořte odběr událostí.

## <a name="send-simulated-iot-data"></a>Odeslat Simulovaná data IoT

Pokud chcete otestovat novou funkci příchozího přenosu dat, použijte simulátor zařízení z [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Tento kurz se řídí ukázkovým projektem napsaným v jazyce C#. Vzorový kód je umístěný tady: [Azure Digital dokončí ukázky](/samples/azure-samples/digital-twins-samples/digital-twins-samples). V tomto úložišti budete používat projekt **DeviceSimulator** .

V tomto koncovém kurzu proveďte následující kroky:
1. [*Zaregistrujte simulované zařízení s IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurace a spuštění simulace*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Ověřit výsledky

Při současném spuštění simulátoru zařízení se změní hodnota teplota digitálního vlákna. V Azure CLI spuštěním následujícího příkazu zobrazte hodnotu teploty.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Výstup by měl obsahovat hodnotu teploty, například:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Chcete-li zobrazit změnu hodnoty, opakovaně spusťte příkaz dotazu výše.

## <a name="next-steps"></a>Další kroky

Přečtěte si o příchozím a odchozím přenosu dat pomocí digitálních vláken Azure:
* [*Koncepty: integrace s jinými službami*](concepts-integration.md)
