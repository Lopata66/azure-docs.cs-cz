---
title: Azure AD v2.0 rychlý úvod k aplikacím web ASP.NET Core | Dokumentace Microsoftu
description: Zjistěte, jak implementovat přihlašování společnosti Microsoft o webové aplikace ASP.NET Core pomocí OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dfa78177974499badc29b7e83556b6a91db7979
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005653"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rychlý start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak webové aplikace ASP.NET Core můžete přihlašovat osobní účty (hotmail.com, outlook.com, ostatní) a pracovní a školní účty z libovolné instance služby Azure Active Directory (Azure AD).

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přejděte [portál Azure – registrace aplikací](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Zaregistrujte si vaši aplikaci a ručně přidejte informace o registraci aplikace do svého řešení, postupujte podle těchto kroků:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-Quickstart`.
>    - V **adresy URL odpovědi**, přidejte `https://localhost:44321/`a vyberte **zaregistrovat**.
> 1. Vyberte **ověřování** nabídky a potom přidejte následující informace:
>    - V **adresy URL odpovědi**, přidejte `https://localhost:44321/signin-oidc`a vyberte **zaregistrovat**.
>    - V **upřesňující nastavení** nastavte **odhlašovací adresa URL** k `https://localhost:44321/signout-oidc`.
>    - V části **implicitní grant**, zkontrolujte **tokeny typu ID**.
>    - Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat adresy URL odpovědí jako `https://localhost:44321/` a `https://localhost:44321/signin-oidc`, přidejte odhlašovací adresa URL jako `https://localhost:44321/signout-oidc`a požádat o tokeny typu ID chcete vystavit koncový bod autorizace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provedení této změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurovali](media/quickstart-v2-aspnet-webapp/green-check.png) vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Stáhněte si svůj projekt ASP.NET Core

- [Stáhněte si řešení sady Visual Studio 2017](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky v kořenové složce – například **C:\Azure-Samples**
1. Pokud používáte Visual Studio 2017, otevřete řešení v sadě Visual Studio (volitelné).
1. Upravit **appsettings.json** souboru. Najít `ClientId` a aktualizujte hodnotu `ClientId` s **ID aplikace (klient)** hodnotu aplikace, které jste právě zaregistrovali. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here` -je **ID aplikace (klient)** pro aplikace, které jste zaregistrovali na webu Azure Portal. Můžete najít **ID aplikace (klient)** aplikace **přehled** stránky.
> - `Enter_the_Tenant_Info_Here` -je jedním z následujících možností:
>   - Pokud vaše aplikace podporuje **účty v tomto adresáři organizace jenom**, nahradí tato hodnota se **ID Tenanta** nebo **název Tenanta** (například) contoso.microsoft.com)
>   - Pokud vaše aplikace podporuje **účty v libovolném adresáři organizace**, nahradí tato hodnota se `organizations`
>   - Pokud vaše aplikace podporuje **uživatele účtu Microsoft všechny**, nahradí tato hodnota se `common`
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

## <a name="more-information"></a>Další informace

Tato část obsahuje základní informace o kódu, který je nutný pro přihlášení uživatelů. To může být užitečné k pochopení, jak kód funguje, hlavní argumenty i pokud budete chtít přidání přihlašování do stávající aplikace ASP.NET Core.

### <a name="startup-class"></a>Třída při spuštění

*Microsoft.AspNetCore.Authentication* middleware použije při spuštění třídy, která se spustí v případě hostitelského procesu inicializuje:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoda `AddAuthentication` konfiguruje službu architektura pro přidání ověřování na základě souboru cookie, což je používat v prohlížeči scénáře, stejně jako nastavit před obrovskou výzvou k OpenID Connect. 

Na řádek obsahující `.AddAzureAd` přidává ověřování Azure AD pro vaši aplikaci. To se pak nakonfiguruje k přihlášení pomocí koncového bodu Azure AD v2.0.

> |Kde  |  |
> |---------|---------|
> | ClientId  | ID aplikace (klient) z aplikace zaregistrované na webu Azure Portal. |
> | Autorita | Koncový bod služby tokenů zabezpečení pro uživatele ověřit. To je obvykle <https://login.microsoftonline.com/{tenant}/v2.0> pro veřejný cloud, kde {klient} je název vašeho klienta nebo ID vašeho tenanta, nebo *běžné* odkazu na společný koncový bod (používá se pro aplikace s více tenanty) |
> | Parametry tokenvalidationparameters. | Seznam parametrů pro ověřování tokenů; V takovém případě `ValidateIssuer` je nastavena na `false` k označení, aby mohl přijímat přihlášení z jakékoli osobní, nebo pracovní nebo školní účty. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Můžete chránit kontroleru nebo pomocí metody kontroleru `[Authorize]` atribut. Tento atribut omezuje přístup k řadiči nebo metody povolením jenom ověřené uživatele, což znamená, že lze spustit výzvu ověřování pro přístup k řadiči, pokud uživatel není ověřen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další postup

Projděte si úložiště GitHub pro tento rychlý start ASP.NET Core pro další informace včetně informací o tom, jak přidat ověřování do zcela nové aplikace ASP.NET Core Web:

> [!div class="nextstepaction"]
> [Vzorový kód aplikace Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

