---
title: 'Ověřování služba služba: sady .NET SDK s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování služba služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí sady .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be8d4172476ca0613e80e62739b9ab36f8ab4c3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881150"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování služba služba s Azure Data Lake Storage Gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

V tomto článku se dozvíte o tom, jak pomocí sady .NET SDK provádět ověřování služba služba s Azure Data Lake Storage Gen1. Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady .NET SDK, přečtěte si téma [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Je nutné dokončit kroky v [ověřování služba služba Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
1. Otevřete Visual Studio a vytvořte konzolovou aplikaci.
2. V nabídce **Soubor** klikněte na položku **Nový** a potom klikněte na položku **Projekt**.
3. V části **Nový projekt** zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Hodnota |
   | --- | --- |
   | Kategorie |Šablony/Visual C#/Windows |
   | Šablona |Konzolová aplikace |
   | Název |VytvořeníAplikaceADL |
4. Projekt vytvoříte kliknutím na **OK**.

5. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   2. Ujistěte se, že na kartě **Správce balíčků NuGet** je položka **Zdroj balíčku** nastavena na hodnotu **nuget.org** a že je zaškrtnuto políčko **Zahrnout předběžné verze**.
   3. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` – Tento kurz používá verzi v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.2.12.

        ![Přidání zdroje NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Vytvoření nového účtu Azure Data Lake")
   4. Zavřete **Správce balíčků NuGet**.

6. Otevřete soubor **Program.cs**, odstraňte stávající kód a potom vložte následující příkazy, čímž přidáte odkazy na obory názvů.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Ověřování služba služba s tajným klíčem klienta
Přidejte tento fragment kódu do klientské aplikace .NET. Nahraďte zástupné hodnoty pomocí hodnot načtených z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje ověřování vaší aplikace **neinteraktivně** s Data Lake Storage Gen1 pomocí klíč/tajný kód klienta pro webovou aplikaci Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Předchozí fragment kódu používá pomocnou funkci `GetCreds_SPI_SecretKey`. Kód pro tato pomocná funkce je k dispozici [si na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Ověřování služba služba s certifikátem

Přidejte tento fragment kódu do klientské aplikace .NET. Nahraďte zástupné hodnoty pomocí hodnot načtených z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje ověřování vaší aplikace **neinteraktivně** s Data Lake Storage Gen1 pomocí certifikátu pro webovou aplikaci Azure AD. Pokyny o tom, jak vytvořit aplikaci Azure AD najdete v tématu [vytvoření instančního objektu s certifikáty](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Předchozí fragment kódu používá pomocnou funkci `GetCreds_SPI_Cert`. Kód pro tato pomocná funkce je k dispozici [si na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat k ověření pomocí Data Lake Storage Gen1 ověřování služba služba pomocí sady .NET SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu .NET SDK pro práci s Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
