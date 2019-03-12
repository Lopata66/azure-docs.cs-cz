---
title: Začínáme s identitou modulu a dvojčetem modulu služby Azure IoT Hub (portál a .NET) | Microsoft Docs
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí portálu a .NET.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: f323eb777d98a812ead8dd8ac78ee3d6b36a748a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545816"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí portálu a zařízení .NET

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.
>

V tomto kurzu se dozvíte:

1. jak vytvořit modul identit na portálu. 

2. jak používat aktualizace sady SDK .NET zařízení dvojčete modulu na vašem zařízení.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub][lnk-hub-sdks].
>

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Vytvoření identity modulu na portálu

V rámci jedné identity zařízení můžete vytvořit až 20 identit modulů. Kliknutím na tlačítko **Přidat identitu modulu** v horní části vytvořte první identitu modulu s názvem **myFirstModule**. 

  ![Detaily zařízení][9]

Uložte právě vytvořenou identitu modulu a klikněte na ni. Zobrazí se podrobnosti o identitě modulu. Uložte připojovací řetězec – primární klíč. Použijete ho v další části, kde nastavíte svůj modul na zařízení.

  ![Detaily zařízení][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

Úspěšně jste vytvořili identitu modulu ve službě IoT Hub. Teď zkuste navázat komunikaci mezi simulovaným zařízením a cloudem. Po vytvoření identity modulu se ve službě IoT Hub implicitně vytvoří dvojče modulu. V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V sadě Visual Studio, přidejte Visual C# Windows klasický desktopový projekt do stávajícího řešení s použitím **Konzolová aplikace (.NET Framework)** šablony projektu. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější. Pojmenujte projekt **UpdateModuleTwinReportedProperties**.

  ![Vytvoření projektu v sadě Visual Studio][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Nainstalujte nejnovější zařízení Azure IoT Hub .NET SDK

Dvojče zařízení identit a modul je ve verzi public preview. Je dostupná jenom v zařízení služby IoT Hub předběžnou verzi sady SDK. V sadě Visual Studio otevřete Nástroje > Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení. Vyhledejte Microsoft.Azure.Devices.Client. Nezapomeňte zaškrtnout políčko Zahrnout předběžnou verzi. Vyberte nejnovější verzi a instalaci. Teď máte přístup ke všem funkcím modulu. 

  ![Instalace sady SDK služby Azure IoT Hub pro .NET verze V1.16.0-preview-005][14]

## <a name="get-your-module-connection-string"></a>Získání připojovacího řetězce modulu

Přihlaste se k [webu Azure portal][lnk-portal]. Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Najít myFirstDevice, otevřít a zobrazit myFirstModule byl úspěšně vytvořen. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

  ![Podrobnosti o modulu na webu Azure Portal][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Vytvoření konzolové aplikace v UpdateModuleTwinReportedProperties

Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem modulu.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Do třídy **Program** přidejte následující metodu **OnDesiredPropertyChanged**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

Nakonec do metody **Main** přidejte následující řádky:

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit. V sadě Visual Studio v Průzkumníku řešení klikněte pravým tlačítkem na řešení a potom klikněte na tlačítko **Nastavit projekty po spuštění**. Vyberte **Více projektů po spuštění** a pak jako akci pro konzolovou aplikaci vyberte **Spustit**. A pak stisknutím klávesy F5 spusťte obě aplikace. 

## <a name="next-steps"></a>Další postup

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme s IoT Hub identit a modul dvojče zařízení pomocí zálohování .NET a .NET zařízení][lnk-csharp-csharp-getstarted]
* [Začínáme se službou IoT Edge][lnk-iot-edge]


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
