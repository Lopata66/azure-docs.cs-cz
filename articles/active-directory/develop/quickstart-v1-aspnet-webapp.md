---
title: Přidání přihlášení účtem Microsoft do webové aplikace ASP.NET | Microsoft Docs
description: Zjistěte, jak přidat přihlašování společnosti Microsoft v řešení technologie ASP.NET s tradiční webové aplikace založené na prohlížeči pomocí OpenID Connect standard.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: andret
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aca42aa13ef78647b591eb0be7083f932ce0c35
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191027"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Rychlý start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

V tomto rychlém startu dozvíte, jak implementovat přihlášení u Microsoftu pomocí řešení ASP.NET řadiče MVC (Model View) s tradiční webové aplikace založené na prohlížeči pomocí OpenID Connect. Naučíte se povolit přihlášení z pracovního a školního účtu v aplikaci ASP.NET.

Na konci tohoto rychlého startu přijmete přihlášení pracovního a školního účtu z organizace, která má integrovanou službu Azure Active Directory (Azure AD).

> [!NOTE]
> Pokud je potřeba povolit přihlášení pro osobní účty kromě pracovním a školním účtům, můžete použít  *[koncového bodu Microsoft identity platform](azure-ad-endpoint-comparison.md)*. Další informace najdete v tématu [tento kurz ASP.NET](tutorial-v2-asp-webapp.md) stejně jako [v tomto článku](active-directory-v2-limitations.md) s vysvětlením, *koncového bodu Microsoft identity platform*.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, musíte splnit tyto předpoklady:

* Nainstalována aplikace Visual Studio 2015 Update 3 nebo Visual Studio 2019. Nemáte ji? [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/)

## <a name="scenario-sign-in-users-from-work-and-school-accounts-in-your-aspnet-app"></a>Scénář: Přihlášení uživatele z pracovním a školním účtům v aplikaci ASP.NET

![Jak funguje tento průvodce](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

V prohlížeči přejde na web ASP.NET a požádá uživatele o ověření pomocí přihlášení tlačítku v tomto scénáři. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

Rychlý start ukazuje, jak přihlásit uživatele na spuštění z prázdné šablony webové aplikace ASP.NET. Také obsahuje kroky, jako je například přidávání tlačítko přihlásit a každý kontroler a metody a tento článek popisuje koncepty za tyto úlohy. Můžete také vytvořit projekt k přihlášení uživatelů Azure AD (pracovní a školní účty) s použitím [šablony webové aplikace Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) a vyberete **účty organizace** a pak jednu z možností cloudu – to možnost používá šablonu bohatší s další řadiče, metody a zobrazení.

## <a name="libraries"></a>Knihovny

Tento rychlý start používá následující balíčky:

| Knihovna | Popis |
|---|---|
| [Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) | Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování |
| [Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies) |Middleware, který aplikaci umožňuje udržovat relaci uživatele pomocí souborů cookie |
| [Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb) | Aplikacím založeným na specifikaci OWIN umožňuje spouštět se ve službě IIS pomocí kanálu požadavků ASP.NET |
|  |  |

## <a name="step-1-set-up-your-project"></a>Krok 1: Nastavení projektu

Tento postup ukazuje, jak nainstalovat a nakonfigurovat kanál ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect.

Pokud si místo toho chcete stáhnout projekt sady Visual Studio této ukázky, postupujte takto:
1. [Stáhněte si projekt z GitHubu](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip).
1. Pokračujte krokem konfigurace před provedením konfigurace vzorový kód.

## <a name="step-2-create-your-aspnet-project"></a>Krok 2: Vytvoření projektu ASP.NET

1. V sadě Visual Studio přejděte na **Soubor > Nový > Projekt**.
2. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)**.
3. Pojmenujte aplikaci a vyberte **OK**.
4. Vyberte **Prázdné** a zaškrtnutím políčka přidejte reference **MVC**.

## <a name="step-3-add-authentication-components"></a>Krok 3: Přidání součástí ověřování

1. V sadě Visual Studio přejděte na **Nástroje > Správce balíčků NuGet > Konzola Správce balíčků**.
2. Přidejte **balíčky NuGet middlewaru OWIN** tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Informace o těchto balíčcích
>Knihovny uvedené výše umožňují jednotné přihlašování pomocí OpenID Connect přes ověřování na základě souborů cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Prohlížeč potom použijte tento soubor cookie v následných žádostech, aby se uživatel nemusel opakovaně ověřovat. Žádné další ověření není potřeba.
<!--end-collapse-->

## <a name="step-4-configure-the-authentication-pipeline"></a>Krok 4: Konfigurace ověřovacího kanálu

Podle následujícího postupu vytvořte *počáteční třídu* middlewaru OWIN a nakonfigurujte ověřování OpenID Connect. Tato třída se spouští automaticky.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:<br/>
> 1. Pravým tlačítkem myši klikněte na kořenovou složku projektu: >    **Přidat > Nová položka... > Třída OWIN Startup**.<br/>
> 2. Pojmenujte ji `Startup.cs`<br/>
>
>> Zkontrolujte, že vybraná třída je počáteční třídou OWIN a nikoli standardní třídou C#. Ověříte si to tak, že nad oborem názvů bude uvedeno `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

Postup vytvoření *počáteční třídy* middlewaru OWIN:

1. Přidejte obor názvů *OWIN* a *Microsoft.IdentityModel* do souboru `Startup.cs`:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Nahraďte počáteční třídu následujícím kódem:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=Startup "Startup.cs")]

<!--start-collapse-->
> [!NOTE]
> Parametry, které zadáte v *OpenIDConnectAuthenticationOptions*, slouží jako souřadnice pro komunikaci aplikace s Azure AD. Middleware OpenID Connect používá soubory cookie, a proto také musíte nastavit ověřování pomocí souborů cookie, jak ukazuje předchozí kód. Hodnota *ValidateIssuer* dává middlewaru OpenIDConnect pokyn, aby neomezoval přístup na jednu konkrétní organizaci.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="step-5-add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Krok 5: Přidání kontroleru pro zpracování požadavků na přihlášení a odhlášení

Vytvořte nový kontroler, který zpřístupní metody přihlášení a odhlášení.

1.  Pravým tlačítkem myši klikněte na složku **Kontrolery** a vyberte **Přidat > Kontroler**.
2.  Vyberte **Kontroler MVC (verze .NET) – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte ho `HomeController` a vyberte **Přidat**.
5.  Přidejte obory názvů **OWIN** do této třídy:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Do kontroleru přidejte následující metody, které budou zajišťovat přihlášení a odhlášení. Uděláte to tak, že prostřednictvím kódu spustíte výzvu ověřování:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=SigInAndSignOut "HomeController.cs")]

## <a name="step-6-create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Krok 6: Vytvoření aplikace domovskou stránku pro přihlášení prostřednictvím tlačítka přihlašování uživatelů

V sadě Visual Studio vytvořte nové zobrazení, do kterého přidáte tlačítko pro přihlášení a ve kterém se po ověření zobrazí informace o uživateli:

1. Pravým tlačítkem myši klikněte na složku **Views\Home** a vyberte **Přidat zobrazení**.
1. Pojmenujte ho **Index**.
1. Do souboru přidejte následující kód HTMP, který obsahuje tlačítko pro přihlášení:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
Tato stránka přidá tlačítko přihlášení ve formátu SVG s černým pozadím:<br/>![Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> Další informace o tlačítkách pro přihlášení najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
<!--end-collapse-->

## <a name="step-7-display-users-claims-by-adding-a-controller"></a>Krok 7: Přidání kontroleru zobrazení deklarací identity uživatele

Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup ke kontroleru tím, že povolí pouze ověřené uživatele. Následující kód využívá atribut k zobrazení deklarací identity uživatelů, které se načetly při přihlášení.

1. Pravým tlačítkem myši klikněte na složku **Kontrolery** a vyberte **Přidat > Kontroler**.
1. Vyberte **Kontroler MVC {version} – prázdný**.
1. Vyberte **Přidat**.
1. Pojmenujte ho **ClaimsController**.
1. Nahraďte kód třídy kontroleru následující kód – v tomto příkladu přidá `[Authorize]` atribut pro třídu:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Protože je použit atribut `[Authorize]`, všechny metody tohoto kontroleru lze spustit pouze v případě, že je uživatel ověřen. Pokud se uživatel neověří a pokusí se o přístup ke kontroleru, OWIN spustí výzvu ověřování a přinutí uživatele, aby se ověřil. Výše uvedený kód vyhledá v kolekci deklarací identity uživatele konkrétní atributy, které jsou součástí tokenu uživatele. Tyto atributy zahrnují celé jméno uživatele a jeho uživatelské jméno, ale také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID organizace uživatele.
<!--end-collapse-->

## <a name="step-8-create-a-view-to-display-the-users-claims"></a>Krok 8: Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1. Pravým tlačítkem myši klikněte na složku **Views\Claims** a vyberte **Přidat zobrazení**.
1. Pojmenujte ho **Index**.
1. Přidejte do souboru následující kód HTML:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]

<!--end-use-->

<!--start-configure-->

## <a name="step-9-configure-your-webconfig-and-register-an-application"></a>Krok 9: Konfigurace vašeho *web.config* a zaregistrovat aplikaci

1. V sadě Visual Studio přidejte následující kód do souboru `web.config` (nachází se v kořenové složce) v části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" />
    ```
2. V Průzkumníku řešení vyberte projekt a přejděte do okna <i>Vlastnosti</i> (pokud toto okno není zobrazené, stiskněte F4).
3. Možnost Protokol SSL je povolený změňte na <code>True</code>.
4. Zkopírujte adresu URL protokolu SSL projektu do schránky:<br/><br/>![Vlastnosti projektu](./media/quickstart-v1-aspnet-webapp/visual-studio-project-properties.png)<br />
5. V souboru <code>web.config</code> nahraďte řetězec <code>Enter_the_Redirect_URL_here</code> adresou URL protokolu SSL projektu.

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registrace vaší aplikace na webu Azure Portal a pak přidejte informace o jeho *web.config*

1. Abyste mohli zaregistrovat aplikaci, přejděte na [Microsoft Azure Portal – Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
2. Vyberte **Registrace nové aplikace**.
3. Zadejte název své aplikace.
4. Vložte *adresu URL protokolu SSL* projektu sady Visual Studio do pole **Přihlašovací adresa URL**. Tato adresa URL je také automaticky přidán do seznamu adres URL odpovědí pro aplikaci, kterou jste registrace.
5. Aplikaci zaregistrujte výběrem možnosti **Vytvořit**. Tato akce vás převede zpět do seznamu aplikací.
6. Nyní vyhledejte a vyberte aplikaci, kterou jste právě vytvořili, a otevřete její vlastnosti.
7. Zkopírujte identifikátor GUID v části **ID aplikace** do schránky.
8. Vraťte se do sady Visual Studio a v `web.config`, nahraďte `Enter_the_Application_Id_here` s ID aplikace z aplikace, které jste zaregistrovali.

> [!TIP]
> Pokud máte účet nakonfigurovaný na přístup k více adresářům, nezapomeňte vybrat správný adresář pro organizaci, pro kterou chcete aplikaci zaregistrovat. Klikněte na název účtu v pravém horním rohu webu Azure Portal a potom zkontrolujte vybraný adresář, jak je uvedeno na obrázku:<br/>![Výběr správného adresáře](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="step-10-configure-sign-in-options"></a>Krok 10: Konfigurovat možnosti přihlášení

Můžete nakonfigurovat svoji aplikaci povolit pouze uživatele, kteří patří do jedné organizace instancí Azure AD pro přihlášení, nebo přijměte přihlášení od uživatele, kteří patří do organizace. Postupujte podle pokynů jednoho z následujících možností:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Konfigurace aplikace na povolení přihlášení pracovním a školním účtem z libovolné společnosti nebo organizace (více tenantů)

Podle následujících pokynů postupujte, pokud chcete povolit přihlášení pracovním a školním účtem z libovolné společnosti nebo organizace, které jsou integrovány s Azure AD. Tento scénář je běžné, že *aplikací SaaS*:

1. Přejděte zpět na [portálu Microsoft Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a vyhledejte aplikaci, které jste zaregistrovali.
2. V části **Všechna nastavení** vyberte **Vlastnosti**.
3. Změňte vlastnost **Více tenantů** na **Ano** a vyberte **Uložit**.

Další informace o tomto nastavení a konceptu aplikací s více tenanty najdete v článku, který se zabývá [přehledem pro více tenantů](howto-convert-app-to-be-multi-tenant.md).

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Omezení přihlášení k aplikaci na uživatele pouze z instance Active Directory jedné organizace (jeden tenant)

Tato možnost představuje společný scénář pro obchodní aplikace.

Pokud chcete, aby aplikace povolovala přihlášení pouze z účtů, které patří do konkrétní instance Azure AD (včetně *účtů hostů* dané instance), postupujte takto:

1. V parametru `Tenant` v souboru *web.config* zadejte místo hodnoty `Common` název tenanta organizace, například *contoso.onmicrosoft.com*.
1. Změňte argument `ValidateIssuer` v [*počáteční třídě OWIN*](#step-4-configure-the-authentication-pipeline) na `true`.

Pokud chcete povolit přihlášení pouze uživatelů, kteří jsou uvedeni v seznamu konkrétních organizací, postupujte takto:

1. Nastavte `ValidateIssuer` na true.
1. K zadání seznamu organizací použijte parametr `ValidIssuers`.

Další možností je implementovat vlastní metodu pro ověření vystavitelů pomocí parametru *IssuerValidator*. Další informace o `TokenValidationParameters`, naleznete v tématu [článku na webu MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "článku na webu MSDN parametry tokenvalidationparameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](./media/quickstart-v1-aspnet-webapp/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->

## <a name="step-11-test-your-code"></a>Krok 11: Testování kódu

1. Stisknutím klávesy **F5** spusťte projekt v sadě Visual Studio. Otevře se prohlížeč a přesměruje vás na `http://localhost:{port}`, kde uvidíte tlačítko **Přihlásit se účtem Microsoft**.
1. Kliknutím na toto tlačítko se přihlaste.

### <a name="sign-in"></a>Přihlášení

Až budete připraveni kód otestovat, přihlaste se pracovním účtem (Azure AD).

![Okno prohlížeče Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin.png)

![Okno prohlížeče Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Očekávané výsledky

Po přihlášení uživatele, uživatel je přesměrován na domovskou stránku vašeho webu, což je adresa URL HTTPS, zadaný v informace o registraci vaší aplikace v portálu pro registraci aplikací společnosti Microsoft. Tato stránka nyní zobrazuje *Vítejte, {User}*, odkaz pro odhlášení a odkaz pro zobrazení deklarací identity uživatele. To je odkaz na ověřovací kontroler, který jste vytvořili dříve.

### <a name="see-users-claims"></a>Zobrazení deklarací identity uživatele

Výběrem odkazu zobrazíte deklarace identity uživatele. Tato akce vás převede do kontroleru a zobrazení, které je dostupné pouze ověřeným uživatelům.

#### <a name="expected-results"></a>Očekávané výsledky

 Měli byste vidět tabulku obsahující základní vlastnosti přihlášeného uživatele:

| Vlastnost | Hodnota | Popis |
|---|---|---|
| Název | {User Full Name} | Jméno a příjmení uživatele |
| Uživatelské jméno | <span>user@domain.com</span> | Uživatelské jméno identifikující přihlášeného uživatele |
| Subjekt| {Subject} |Řetězec k jednoznačné identifikaci uživatele přihlásit na webu |
| ID tenanta | {Guid} | Identifikátor *guid*, který jedinečně identifikuje organizaci Azure AD uživatele |

Kromě toho uvidíte tabulku obsahující všechny deklarace identity uživatele, které jsou obsaženy v žádosti o ověření. Seznam všech deklarací identity v tokenu ID a jejich vysvětlení najdete v [seznamu deklarací identity v tokenu ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="optional-access-a-method-that-has-an-authorize-attribute"></a>(Volitelné) Přístup k metodě obsahující atribut *[Authorize]*

V tomto kroku otestujete přístup ke kontroleru deklarací identity jako anonymní uživatel:<br/>
Výběrem odkazu odhlaste uživatele a dokončete proces odhlášení.<br/>
V prohlížeči zadejte `http://localhost:{port}/claims`, abyste získali přístup ke kontroleru, který je chráněn atributem `[Authorize]`.

#### <a name="expected-results"></a>Očekávané výsledky

Měla by se zobrazit výzva, abyste se ověřili a zpřístupnilo se vám zobrazení.

## <a name="additional-information"></a>Další informace

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Ochrana celého webu

Pokud chcete chránit celý web, přidejte `AuthorizeAttribute` do `GlobalFilters` v `Global.asax` metodu `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->

## <a name="next-steps"></a>Další postup

Můžete teď přejít na další scénáře.

> [!div class="nextstepaction"]
> [Kurz ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-asp-webapp)
