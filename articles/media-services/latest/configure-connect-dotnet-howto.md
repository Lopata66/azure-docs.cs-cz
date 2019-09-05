---
title: Připojení k Azure Media Services V3 API – .NET
description: Přečtěte si, jak se připojit k rozhraní Media Services V3 API pomocí .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 3ddf5a1ab37ac0af25379394b4513627139fcbd5
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307952"
---
# <a name="connect-to-media-services-v3-api---net"></a>Připojení k Media Services V3 API – .NET

V tomto článku se dozvíte, jak se připojit k sadě Azure Media Services V3 .NET SDK pomocí metody Login objektu služby.

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services
- Nainstalujte nástroj, který byste chtěli použít pro vývoj pro .NET. Postup v tomto článku ukazuje, jak používat [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Visual Studio Code lze použít v tématu [Working with C# ](https://code.visualstudio.com/docs/languages/csharp). Nebo můžete použít jiný Editor kódu.

> [!IMPORTANT]
> Přečtěte si [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio. 
1. V nabídce **soubor** klikněte na příkaz **Nový** > **projekt**. 
1. Vytvořte konzolovou aplikaci **.NET Core** .

Ukázková aplikace v tomto tématu cílí na `netcoreapp2.0`cíle. Kód používá Async Main, který je k dispozici počínaje verzí C# 7,1. Další podrobnosti najdete v tomto [blogu](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) .

## <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

1. V aplikaci Visual Studio vyberte **nástroje** >  > **Správce balíčků NuGet** **Konzola správce NuGet**.
2. V okně **konzoly Správce balíčků** přidejte následující balíčky `Install-Package` NuGet pomocí příkazu. Například, `Install-Package Microsoft.Azure.Management.Media`.

|Balíček|Popis|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Abyste měli jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Knihovna ověřování ADAL pro sadu Azure SDK pro NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Čtení hodnot konfigurace z proměnných prostředí a místních souborů JSON|
|`Microsoft.Extensions.Configuration.Json`|Čtení hodnot konfigurace z proměnných prostředí a místních souborů JSON
|`WindowsAzure.Storage`|Sada SDK pro úložiště|

## <a name="create-and-configure-the-app-settings-file"></a>Vytvoření a konfigurace souboru nastavení aplikace

### <a name="create-appsettingsjson"></a>Vytvořit appSettings. JSON

1. Přejít na **obecný** > **textový soubor**
1. Pojmenujte ho "appSettings. JSON".
1. Nastavte vlastnost kopírovat do výstupního adresáře souboru. JSON na kopírovat, pokud je novější (takže aplikace bude mít k nim přístup, když je publikovaná).

### <a name="set-values-in-appsettingsjson"></a>Nastavení hodnot v souboru appSettings. JSON

Spusťte příkaz `az ams account sp create` , jak je popsáno v tématu [přístupová rozhraní API](access-api-cli-how-to.md). Příkaz vrátí JSON, který byste měli zkopírovat do souboru appSettings. JSON.
 
## <a name="add-configuration-file"></a>Přidání konfiguračního souboru

Pro usnadnění práce přidejte konfigurační soubor, který je zodpovědný za čtení hodnot z "appSettings. JSON".

1. Přidejte do projektu novou třídu. cs. Pojmenujte ji `ConfigWrapper`. 
1. Do tohoto souboru vložte následující kód (Tento příklad předpokládá, že máte obor názvů `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Připojení k klientovi .NET

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V následujícím kódu GetCredentialsAsync funkce vytvoří objekt ServiceClientCredentials na základě přihlašovacích údajů dodaných v místním konfiguračním souboru.

1. Otevřít `Program.cs`.
1. Vložte následující kód:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Další postup

- [Kurz: Nahrávání, kódování a streamování videí – .NET](stream-files-tutorial-with-api.md) 
- [Kurz: Živé streamování s Media Services V3 – .NET](stream-live-tutorial-with-api.md)
- [Kurz: Analýza videí pomocí Media Services V3 – .NET](analyze-videos-tutorial-with-api.md)
- [Vytvoření vstupu úlohy z místního souboru – .NET](job-input-from-local-file-how-to.md)
- [Vytvoření vstupu úlohy z adresy URL protokolu HTTPS – .NET](job-input-from-http-how-to.md)
- [Kódování pomocí vlastní transformace – .NET](customize-encoder-presets-how-to.md)
- [Použití dynamického šifrování AES-128 a služby pro doručování klíčů – .NET](protect-with-aes128.md)
- [Použití dynamického šifrování DRM a služby doručování licencí – .NET](protect-with-drm.md)
- [Získat podpisový klíč ze stávající zásady – .NET](get-content-key-policy-dotnet-howto.md)
- [Vytváření filtrů pomocí Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Příklady pokročilých videí na vyžádání Azure Functions v2 s Media Services V3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Viz také:

[Reference k .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
