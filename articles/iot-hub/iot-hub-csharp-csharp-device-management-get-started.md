---
title: Začínáme se správou zařízení Azure IoT Hub (.NET/.NET) | Dokumentace Microsoftu
description: Jak používat správu zařízení Azure IoT Hub k zahajte restartování vzdáleném zařízení. Implementace aplikace s Simulovaná zařízení, která obsahuje metodu s přímým přístupem a službu Azure IoT SDK pro .NET k implementaci app service, která vyvolá přímou metodu pomocí zařízení Azure IoT SDK pro .NET.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 556c10cc5ec5e528857a120dadb16c2a10202ed3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250947"
---
# <a name="get-started-with-device-management-netnet"></a>Začínáme se správou zařízení (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.

* Vytvoření aplikace simulovaného zařízení, která obsahuje přímé metody, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvoření konzolové aplikace .NET, která volá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **SimulateManagedDevice**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží restartování přímé metody, simuluje fyzické restartování a ohlásí, čas poslední restartování.

* **TriggerReboot**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaný ohlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Sada Visual Studio 2017.

* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační událost vzdálené restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci .NET (s použitím jazyka C#), které vyvolává vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazů na dvojčata zařízení ke zjištění poslední čas restartování pro dané zařízení.

1. V sadě Visual Studio přidejte k novému řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Zkontrolujte, zda máte verzi rozhraní .NET Framework 4.5.1 nebo novější. Pojmenujte projekt **TriggerReboot**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerReboot** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet**.

3. V **Správce balíčků NuGet** okně **Procházet**, vyhledejte **Microsoft.Azure.Devices**vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [pro službu Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet balíček a jeho závislosti.

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem služby IoT Hub pro rozbočovač, kterou jste vytvořili v části "Vytvoření služby IoT hub." 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Do třídy **Program** přidejte následující metodu.  Tento kód získá dvojčeti zařízení restartuje zařízení a vypíše ohlášené vlastnosti.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Do třídy **Program** přidejte následující metodu.  Tento kód inicializuje restartování na zařízení s využitím přímé metody.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Nakonec do metody **Main** přidejte následující řádky:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Sestavte řešení.

> [!NOTE]
> V tomto kurzu provádí pouze pomocí jediného dotazu pro ohlášených vlastností zařízení. V produkčním kódu doporučujeme dotazování zjišťovat změny v ohlášené vlastnosti.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části provedete následující operace:

* Vytvoření konzolové aplikace .NET, která bude reagovat na přímou metodu volanou cloudem.

* Aktivujte restartování simulované zařízení.

* Pomocí ohlášených vlastností umožníte dotazů na dvojčata zařízení identifikovat zařízení a kdy byly naposledy restartován.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Pojmenujte projekt **SimulateManagedDevice**.
   
    ![Nový Visual C# Windows klasické aplikace pro zařízení](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateManagedDevice** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okně **Procházet** a vyhledejte **Microsoft.Azure.Devices.Client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na [zařízení Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet balíček a jeho závislosti.
   
    ![Klientská aplikace okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Přidejte následující k implementaci přímé metody v zařízení:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Nakonec přidejte následující kód, který **hlavní** metody k otevření připojení do služby IoT hub a inicializovat naslouchací proces – metoda:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na své řešení a pak klikněte na **Nastavit projekty po spuštění...**. Vyberte **jeden spouštěný projekt**a pak vyberte **SimulateManagedDevice** projektu v rozevírací nabídce. Sestavte řešení.       

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. Chcete-li spustit aplikaci .NET pro zařízení **SimulateManagedDevice**. Klikněte pravým tlačítkem myši **SimulateManagedDevice** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. By se měl spustit naslouchání pro volání metody ze služby IoT hub. 

2. Teď, když je zařízení připojené a čeká volání metod, spusťte .NET **TriggerReboot** aplikace k vyvolání metody restartování v aplikaci simulovaného zařízení. Chcete-li to provést, klikněte pravým tlačítkem **TriggerReboot** projekt, vyberte **ladění**a pak vyberte **zahájit novou instanci**. Měli byste vidět "Restartování!" napsané v **SimulatedManagedDevice** konzoly a ohlášených vlastností zařízení, mezi které patří poslední čas napsané v restartování **TriggerReboot** konzoly.
   
    ![Běh aplikace služby a zařízení](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
