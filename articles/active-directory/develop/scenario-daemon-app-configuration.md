---
title: Konfigurace aplikací démona, které volají webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se konfigurovat kód pro aplikaci démona, která volá webová rozhraní API (konfigurace aplikace).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/08/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 85c9bd3ecd5af861cfb93c18528416e06479ae93
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119177"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikace démona, která volá webovou rozhraní API – konfigurace kódu

Naučte se konfigurovat kód pro aplikaci démona, která volá webová rozhraní API.

## <a name="msal-libraries-that-support-daemon-apps"></a>MSAL knihovny, které podporují aplikace démona

Tyto knihovny Microsoftu podporují aplikace démona:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Platformy .NET Framework a .NET Core jsou podporovány pro vytváření aplikací démona. (UWP, Xamarin. iOS a Xamarin. Android se nepodporují, protože tyto platformy slouží k sestavování veřejných klientských aplikací.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Podpora pro aplikace démona v Pythonu
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL v Javě | Podpora pro aplikace démona v jazyce Java.

## <a name="configure-the-authority"></a>Konfigurace autority

Aplikace démona místo delegovaných oprávnění používají oprávnění aplikace. Proto jejich podporovaný typ účtu nemůže být účet v žádné organizační složce ani v žádné osobní účet Microsoft (například Skype, Xbox, Outlook.com). Není k dispozici žádný správce tenanta pro udělení souhlasu aplikaci démona pro osobní účet Microsoft. V organizaci nebo účtech budete muset zvolit *účty* *v libovolné organizaci*.

Proto by měl být autorita zadaná v konfiguraci aplikace tenantů (zadáním ID tenanta nebo názvu domény přidruženého k vaší organizaci).

Pokud jste nezávislý výrobce softwaru a chcete poskytnout víceklientské nástroje, můžete použít `organizations` . Mějte ale na paměti, že budete taky muset vysvětlit zákazníkům, jak udělit souhlas správce. Podrobnosti najdete v článku [o žádosti o souhlas pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). V současné době je také omezení MSAL: `organizations` je povoleno pouze v případě, že jsou pověřením klienta tajný klíč aplikace (nikoli certifikát).

## <a name="configure-and-instantiate-the-application"></a>Konfigurace a vytvoření instance aplikace

V knihovnách MSAL se přihlašovací údaje klienta (tajný kód nebo certifikát) předávají jako parametr konstrukce důvěrné klientské aplikace.

> [!IMPORTANT]
> I v případě, že je vaše aplikace Konzolová aplikace, která běží jako služba, pokud se jedná o aplikaci démona, musí to být důvěrná klientská aplikace.

### <a name="configuration-file"></a>Konfigurační soubor

Konfigurační soubor definuje:

- Autorita nebo instance cloudu a ID tenanta.
- ID klienta, které jste získali z registrace aplikace.
- Buď tajný klíč klienta, nebo certifikát.

> [!NOTE]
> Fragmenty kódu .NET ve zbývající části článku referenční [Konfigurace](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs) najdete v ukázce [Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.js](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) z ukázky [procesu démona konzoly .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Zadáte buď `ClientSecret` nebo `CertificateName` . Tato nastavení jsou exkluzivní.

# <a name="python"></a>[Python](#tab/python)

Když vytváříte důvěrného klienta s klientskými tajnými kódy, [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) v souboru config v ukázce [démona Pythonu](https://github.com/Azure-Samples/ms-identity-python-daemon) je následující:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Když vytváříte důvěrného klienta s certifikáty, [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) v souboru config v ukázce [démona Pythonu](https://github.com/Azure-Samples/ms-identity-python-daemon) je následující:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Vytvoření instance aplikace MSAL

Chcete-li vytvořit instanci aplikace MSAL, je nutné přidat, odkazovat nebo importovat balíček MSAL (v závislosti na jazyku).

Konstrukce se liší v závislosti na tom, zda používáte klientské tajné klíče nebo certifikáty (nebo jako pokročilé scénáře, podepsané kontrolní výrazy).

#### <a name="reference-the-package"></a>Odkaz na balíček

Odkazování na balíček MSAL v kódu aplikace.

# <a name="net"></a>[.NET](#tab/dotnet)

Přidejte do své aplikace balíček NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) .
V MSAL.NET je aplikace důvěrného klienta reprezentovaná `IConfidentialClientApplication` rozhraním.
Použijte obor názvů MSAL.NET ve zdrojovém kódu.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Vytvoření instance aplikace důvěrného klienta s tajným klíčem klienta

Zde je kód pro vytvoření instance aplikace důvěrného klienta s tajným klíčem klienta:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Vytvoření instance aplikace důvěrného klienta pomocí klientského certifikátu

Zde je kód pro sestavení aplikace s certifikátem:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

V MSAL Java existují dva tvůrci pro vytvoření instance aplikace důvěrného klienta s certifikáty:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

nebo

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Rozšířený scénář: vytvoření instance aplikace důvěrného klienta s kontrolními výrazy klienta

# <a name="net"></a>[.NET](#tab/dotnet)

Místo tajného klíče klienta nebo certifikátu může důvěrná klientská aplikace také prokázat svoji identitu pomocí kontrolních výrazů klienta.

MSAL.NET má dvě metody pro poskytování podepsaných kontrolních výrazů do aplikace důvěrného klienta:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Když použijete `WithClientAssertion` , musíte poskytnout podepsaný token JWT. Tento rozšířený scénář je podrobně popsán v [kontrolním výrazu klienta](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Když použijete `WithClientClaims` , MSAL.NET vytvoří podepsaný kontrolní výraz, který obsahuje deklarace očekávané službou Azure AD a další deklarace identity klienta, které chcete odeslat.
Tento kód ukazuje, jak to provést:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Další podrobnosti najdete v tématu [kontrolní výrazy klienta](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

V MSAL Pythonu můžete poskytovat deklarace identity klientů pomocí deklarací identity, které budou podepsány tímto `ConfidentialClientApplication` privátním klíčem.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Podrobnosti najdete v referenční dokumentaci k MSAL Pythonu pro [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Další kroky

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikace démona – získávání tokenů pro aplikaci](./scenario-daemon-acquire-token.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikace démona – získávání tokenů pro aplikaci](./scenario-daemon-acquire-token.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikace démona – získávání tokenů pro aplikaci](./scenario-daemon-acquire-token.md?tabs=java)

---