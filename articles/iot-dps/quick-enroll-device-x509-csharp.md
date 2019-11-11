---
title: 'Rychlý Start: jak zaregistrovat zařízení X. 509 do služby Azure Device Provisioning pomocíC#'
description: V tomto rychlém startu se používají skupinové registrace. V tomto rychlém startu zaregistrujete zařízení X. 509 do Azure IoT Hub Device Provisioning Service C#pomocí.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: e43448337f787115c479f2f53ca57b7a20120108
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903437"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rychlý start: Registrace zařízení X.509 do služby Device Provisioning Service pomocí C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Tento rychlý start ukazuje, jak prostřednictvím kódu programu v C# vytvořit [skupinu registrací](concepts-service.md#enrollment-group), která používá zprostředkující nebo kořenové certifikáty X.509 certifikační autority. Skupina registrace se vytvoří pomocí [sady Microsoft Azure IoT SDK pro .NET](https://github.com/Azure/azure-iot-sdk-csharp) a ukázkové C# aplikace .NET Core. Skupina registrací řídí přístup ke službě zřizování pro zařízení, která ve svém řetězu certifikátů sdílejí společný podpisový certifikát. Další informace najdete v tématu [Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Další informace o používání infrastruktury veřejných klíčů (PKI) založené na certifikátech X.509 se službami Azure IoT Hub a Device Provisioning najdete v tématu [Přehled zabezpečení pomocí certifikátu webu X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

V tomto rychlém startu se očekává, že už máte vytvořenou instanci služby IoT Hub a Device Provisioning. Pokud jste tyto prostředky ještě nevytvořili, dokončete [nastavení IoT Hub Device Provisioning Service s Azure Portal](./quick-setup-auto-provision.md) rychlý Start, než budete pokračovat v tomto článku.

I když postup v tomto článku funguje na počítačích se systémem Windows i Linux, Tento článek používá vývojový počítač s Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Nainstalujte [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Příprava testovacích certifikátů

Pro účely tohoto rychlého startu potřebujete soubor .pem nebo .cer, který obsahuje veřejnou část zprostředkujícího nebo kořenového certifikátu X.509 certifikační autority. Certifikát musí být nahraný do vaší služby zřizování a ověřený touto službou.

[Sada Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) obsahuje testovací nástroje, které vám pomůžou vytvořit řetěz certifikátů X. 509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězu a ověřit certifikát tak, že ho dokončí služba.

> [!CAUTION]
> Používejte certifikáty vytvořené pomocí nástrojů sady SDK pouze pro vývojové testování.
> Nepoužívejte tyto certifikáty v produkčním prostředí.
> Obsahují pevně kódovaná hesla, například *1234*, jejichž platnost vyprší po 30 dnech.
> Informace o získání certifikátů vhodných pro použití v produkčním prostředí najdete v tématu věnovaném [získání certifikátu webu X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) v dokumentaci ke službě Azure IoT Hub.
>

Chcete-li použít toto testovací nástroje pro generování certifikátů, proveďte následující kroky:

1. Otevřete okno příkazového řádku nebo prostředí Git bash a přejděte do pracovní složky ve vašem počítači. Spuštěním následujícího příkazu naklonujte úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

   Testovací nástroje se nacházejí ve složce *azure-iot-sdk-c/tools/CACertificates* úložiště, které jste naklonovali.

1. Ukázky a kurzy najdete v článku [Správa testovacích certifikátů certifikační autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Kromě nástrojů v sadě C SDK je [Ukázka ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) v sadě *Microsoft Azure IoT SDK pro .NET* předvádí, jak C# se má prověřování pomocí existujícího certifikátu X. 509 Intermediate nebo Root CA.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Získání připojovacího řetězce pro službu zřizování

Pro ukázku v tomto rychlém startu potřebujete připojovací řetězec pro vaši službu zřizování.

1. Přihlaste se k Azure Portal, vyberte **všechny prostředky**a službu Device Provisioning.

1. Vyberte **zásady sdíleného přístupu**a pak zvolte zásadu přístupu, kterou chcete použít k otevření jejích vlastností. V části **zásady přístupu**zkopírujte a uložte připojovací řetězec primárního klíče.

    ![Získání připojovacího řetězce služby zřizování z portálu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Vytvoření ukázky skupiny registrací 

V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Core, která do vaší služby zřizování přidá skupinu pro registraci. S určitými úpravami můžete pomocí tohoto postupu vytvořit pro přidání skupiny registrací také konzolovou aplikaci [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot). Další informace o vývoji s využitím IoT Core najdete v [dokumentaci pro vývojáře pro Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**. V části **vytvořit nový projekt**zvolte **Konzolová aplikace (.NET Core)** pro C# šablonu projektu a vyberte **Další**.

1. Pojmenujte projekt *CreateEnrollmentGroup*a pak vyberte **vytvořit**.

    ![Konfigurovat klasický C# desktopový projekt Visual Windows](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **CreateEnrollmentGroup** a pak vyberte **Spravovat balíčky NuGet**.

1. V **nástroji Správce balíčků NuGet**vyberte **Procházet**, vyhledejte a zvolte **Microsoft. Azure. Devices. Provisioning. Service**a pak vyberte **nainstalovat**.

    ![Okno Správce balíčků NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Tento krok stáhne a nainstaluje balíček NuGet [klientské sady SDK služby zřizování pro Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) a jeho závislosti a přidá se na něj odkaz.

1. Po ostatních příkazech `using` na začátku `Program.cs`přidejte následující příkazy `using`:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Přidejte následující pole do třídy `Program` a proveďte uvedené změny.  

   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Nahraďte hodnotu zástupného symbolu `ProvisioningConnectionString` připojovacím řetězcem služby zřizování, pro kterou chcete vytvořit registraci.

   * Nahraďte hodnotu zástupného symbolu `X509RootCertPath` cestou k souboru. pem nebo. cer. Tento soubor představuje veřejnou část certifikátu X. 509 zprostředkujícího nebo kořenového certifikačního úřadu, který se dřív nahrál a ověřil pomocí vaší služby zřizování.

   * Případně můžete změnit hodnotu `EnrollmentGroupId`. Řetězec může obsahovat pouze malá písmena a pomlčky.

   > [!IMPORTANT]
   > V případě produkčního kódu mějte na paměti následující aspekty zabezpečení:
   >
   > * Uložení připojovacího řetězce pro správce služby zřizování v kódu je v rozporu s osvědčenými postupy zabezpečení. Místo toho by se měl připojovací řetězec uchovávat zabezpečeným způsobem, například v zabezpečeném konfiguračním souboru nebo v registru.
   > * Nezapomeňte nahrát pouze veřejnou část podpisového certifikátu. Nikdy nenahrávejte soubory .pfx (PKCS12) ani .pem obsahující privátní klíče ke službě zřizování.

1. Přidejte následující metodu do třídy `Program`. Tento kód vytvoří položku skupiny registrací a potom zavolá metodu `CreateOrUpdateEnrollmentGroupAsync` v `ProvisioningServiceClient`, aby přidala skupinu registrací do služby zřizování.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Nakonec nahraďte text metody `Main` následujícími řádky:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Sestavte řešení.

## <a name="run-the-enrollment-group-sample"></a>Spuštění ukázky skupiny registrací
  
Spusťte ukázku v sadě Visual Studio, aby se vytvořila skupina registrací. Po úspěšném vytvoření se v okně příkazového řádku zobrazí vlastnosti nové skupiny pro registraci.

Můžete ověřit, že se vytvořila skupina pro registraci. Přejít do přehledu služby Device Provisioning a vybrat **spravovat registrace**a pak vybrat **skupiny**registrací. Měla by se zobrazit nová položka registrace odpovídající ID registrace, které jste použili v ukázce.

![Vlastnosti registrace na portálu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Vyberte položku pro ověření kryptografického otisku certifikátu a dalších vlastností položky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete prozkoumat ukázku C# služby, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete okno C# Ukázka výstupu v počítači.

1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace**a pak vyberte **skupiny**registrací. Vyberte *ID registrace* pro položku registrace, kterou jste vytvořili v rámci tohoto rychlého startu, a vyberte **Odstranit**.

1. Ze služby Device Provisioning v Azure Portal vyberte **certifikáty**, zvolte certifikát, který jste nahráli pro tento rychlý Start, a v horní části **podrobností certifikátu**vyberte **Odstranit** .  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu registrací pro certifikát X. 509 zprostředkující nebo kořenové certifikační autority pomocí Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
