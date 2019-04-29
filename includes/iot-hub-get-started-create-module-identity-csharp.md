---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741806"
---
## <a name="create-a-module-identity"></a>Vytvoření identity modulu

V této části vytvoříte konzolovou aplikaci .NET, která v registru identit v centru IoT vytvoří identitu zařízení a identitu modulu. Zařízení nebo modul je možné připojit k centru IoT, pouze pokud má záznam v registru identit. Další informace najdete v tématu [registr identit tématu Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.


1. **Vytvoření projektu v sadě Visual Studio** – V sadě Visual Studio přidejte k novému řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější. Projekt pojmenujte **CreateIdentities** a řešení pojmenujte **IoTHubGetStarted**.

    ![Vytvoření řešení v sadě Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Instalace sady SDK služby Azure IoT Hub pro .NET verze V1.16.0-preview-001** – Identita modulu a dvojče modulu jsou ve verzi Public Preview. Je dostupná jenom v sadách SDK předběžné verze služby IoT Hub. V sadě Visual Studio otevřete Nástroje > Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení. Vyhledejte Microsoft.Azure.Devices. Nezapomeňte zaškrtnout políčko Zahrnout předběžnou verzi. Vyberte verzi 1.16.0-preview-001 a spusťte instalaci. Teď máte přístup ke všem funkcím modulu. 

    ![Instalace sady SDK služby Azure IoT Hub pro .NET verze V1.16.0-preview-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Přidejte následující kód, který **hlavní** třídy.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Do třídy **Program** přidejte následující metody:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Metoda AddDeviceAsync() vytvoří identitu zařízení s ID **myFirstDevice**. (Pokud toto ID zařízení již v registru identit existuje, kód jednoduše načte informace o stávajícím zařízení.) Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč v aplikaci simulovaného zařízení slouží k připojení ke službě IoT Hub.

    Metoda AddModuleAsync() vytvoří identitu modulu s ID **myFirstModule** pro zařízení **myFirstDevice**. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč v aplikaci simulovaného modulu slouží k připojení k centru IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Spusťte aplikaci a poznamenejte si klíč zařízení a klíč modulu.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v tématu [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
