---
title: Sestavení procesu víceklientské architektury s použitím koncového bodu Microsoft Identity Platform
description: V tomto kurzu se dozvíte, jak volat webové rozhraní API ASP.NET chráněné Azure Active Directory z aplikace Windows Desktop (WPF). Klient WPF ověří uživatele, požádá o přístupový token a zavolá webové rozhraní API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 635b12cc2ffc4d318eaaa74fffc17e4ce4d58c0b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129964"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Kurz: sestavení procesu víceklientské architektury s využitím koncového bodu Microsoft Identity Platform

V tomto kurzu se naučíte používat Microsoft Identity Platform pro přístup k datům obchodních zákazníků Microsoftu v dlouhodobém, neinteraktivním procesu. Vzorový démon používá [pověření klienta OAuth2](v2-oauth2-client-creds-grant-flow.md) k získání přístupového tokenu. Démon pak pomocí tokenu volá [Microsoft Graph](https://graph.microsoft.io) a přistupuje k datům organizace.

> [!div class="checklist"]
> * Integrace aplikace démona s platformou Microsoft identity
> * Udělení oprávnění k aplikaci přímo aplikaci správcem
> * Získání přístupového tokenu pro volání rozhraní Microsoft Graph API
> * Zavolejte Microsoft Graph rozhraní API.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Aplikace je sestavená jako aplikace ASP.NET MVC. K přihlašování uživatelů používá middleware OWIN OpenID Connect.  

Komponentou "démon" v této ukázce je kontroler rozhraní API `SyncController.cs`. Když se kontroler zavolá, vyžádá si seznam uživatelů v tenantovi Azure Active Directory zákazníka (Azure AD) z Microsoft Graph. `SyncController.cs` aktivuje volání AJAX ve webové aplikaci. K získání přístupového tokenu pro Microsoft Graph používá [Microsoft Authentication Library (MSAL) pro .NET](msal-overview.md) .

>[!NOTE]
> Pokud začínáte s platformou Microsoft identity, doporučujeme začít s rychlým startem [procesu .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scénář

Vzhledem k tomu, že aplikace je víceklientské aplikace pro obchodní zákazníky Microsoftu, musí zákazníkům poskytnout způsob, jak se zaregistrovat nebo připojit k podnikovým datům. Během toku připojení správce společnosti nejprve udělí *oprávnění aplikace* přímo aplikaci, aby mohl přistupovat k firemním datům neinteraktivním způsobem bez přihlášeného uživatele. Většina logiky v této ukázce ukazuje, jak dosáhnout tohoto toku připojení pomocí koncového bodu [souhlasu správce](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) platformy identity.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Další informace o konceptech použitých v této ukázce najdete v dokumentaci k [protokolu přihlašovacích údajů klienta pro koncový bod platformy identity](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete ukázku spustit v tomto rychlém startu, budete potřebovat:

- [Visual Studio 2017 nebo 2019](https://visualstudio.microsoft.com/downloads/).
- Tenanta Azure AD. Další informace najdete v tématu [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md).
- Jeden nebo více uživatelských účtů v tenantovi Azure AD. Tato ukázka nebude fungovat s účet Microsoft (dříve účet Windows Live). Pokud jste se k [Azure Portal](https://portal.azure.com) přihlásili pomocí účet Microsoft a nikdy jste ve svém adresáři nevytvořili uživatelský účet, musíte to udělat hned teď.

## <a name="clone-or-download-this-repository"></a>Klonovat nebo stáhnout toto úložiště

V prostředí nebo příkazovém řádku zadejte tento příkaz:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Nebo [si ukázku Stáhněte v souboru ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrace vaší aplikace

Tato ukázka má jeden projekt. Pokud chcete aplikaci zaregistrovat u svého tenanta Azure AD, můžete:

- Postupujte podle kroků v části [registrace ukázky u svého tenanta Azure Active Directory](#register-your-application) a [nakonfigurujete ukázku, aby používala vašeho tenanta Azure AD](#choose-the-azure-ad-tenant).
- Použijte skripty prostředí PowerShell, které:
  - *Automaticky* vytvořte aplikace Azure AD a související objekty (hesla, oprávnění, závislosti) za vás.
  - Upravte konfigurační soubory projektů sady Visual Studio.

Pokud chcete použít automatizaci:

1. V systému Windows spusťte PowerShell a přejdete do kořenového adresáře klonovaného adresáře.
1. Spusťte tento příkaz:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Spusťte skript pro vytvoření aplikace Azure AD a příslušným způsobem nakonfigurujte kód ukázkové aplikace:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Další způsoby spouštění skriptů jsou popsány v tématu [skripty pro vytváření aplikací](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Otevřete řešení sady Visual Studio a výběrem **Spustit** spusťte kód.

Pokud nechcete používat automatizaci, postupujte podle kroků v následujících částech.

### <a name="choose-the-azure-ad-tenant"></a>Výběr tenanta Azure AD

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. Pokud je váš účet ve více než jednom tenantovi služby Azure AD, vyberte svůj profil v nabídce v horní části stránky a pak vyberte **Přepnout adresář**.
1. Změňte relaci portálu na požadovaného tenanta Azure AD.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrace klientské aplikace (dotnet-web-démon-v2)

1. Přejít na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) na platformě Microsoft identity pro vývojáře.
1. Vyberte **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace. Zadejte například příkaz **dotnet-web-démon-v2**.
   - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři**.
   - V části **identifikátor URI přesměrování (volitelné)** vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátory URI pro přesměrování:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Pokud existuje více než dva identifikátory URI přesměrování, budete je muset přidat z karty **ověřování** později po úspěšném vytvoření aplikace.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**. Potom:
   - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na **https://localhost:44316/Account/EndSession** .
   - V části **Upřesnit nastavení** > **implicitního udělení** přístupu vyberte **přístupové tokeny** a **tokeny ID**. Tato ukázka vyžaduje, aby byl [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele a volání rozhraní API.
1. Vyberte **Save** (Uložit).
1. Na stránce **certifikáty & tajné klíče** v části **tajné klíče klienta** vyberte **nový tajný klíč klienta**. Potom:

   1. Zadejte popis klíče (například **tajný klíč aplikace**),
   1. Vyberte dobu trvání klíče buď **v 1 roce**, **2 roky**, nebo **nikdy nevyprší**.
   1. Vyberte tlačítko **Přidat** . 
   1. Když se zobrazí hodnota klíče, zkopírujte ji a uložte ji v bezpečném umístění. Tento klíč budete potřebovat později ke konfiguraci projektu v aplikaci Visual Studio. Nebude se znovu zobrazovat ani získat jiným způsobem.
1. V seznamu stránek aplikace vyberte **oprávnění rozhraní API**. Potom:
   1. Vyberte tlačítko **Přidat oprávnění**.
   1. Ujistěte se, že je vybraná karta **rozhraní API Microsoftu** .
   1. V části **běžně používaná rozhraní Microsoft API** vyberte **Microsoft Graph**.
   1. V části **oprávnění aplikace** se ujistěte, že jsou vybrána správná oprávnění: **User. Read. All**.
   1. Klikněte na tlačítko **Přidat oprávnění** .

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurace ukázky pro použití vašeho tenanta Azure AD

V následujících krocích je **ClientID** stejný jako "ID aplikace" nebo **AppID**.

Otevřete řešení v aplikaci Visual Studio a nakonfigurujte projekty.

### <a name="configure-the-client-project"></a>Konfigurovat klientský projekt

Pokud jste použili instalační skripty, budou pro vás provedeny následující změny.

1. Otevřete soubor **UserSync\Web.config** .
1. Vyhledejte klíč aplikace **IDA: ClientID**. Nahraďte existující hodnotu ID aplikace aplikace **dotnet-web-démon-v2** zkopírovanou z Azure Portal.
1. Vyhledejte klíč aplikace v aplikaci **IDA: ClientSecret**. Existující hodnotu nahraďte klíčem, který jste uložili během vytváření aplikace **dotnet-web-démon-v2** v Azure Portal.

## <a name="run-the-sample"></a>Spuštění ukázky

Vyčistěte řešení, znovu sestavte řešení, spusťte aplikaci UserSync a pak se přihlaste jako správce tenanta služby Azure AD. Pokud pro testování nemáte tenanta Azure AD, můžete si ho stáhnout [podle těchto pokynů](quickstart-create-new-tenant.md) .

Když se přihlásíte, aplikace si nejdřív vyžádá oprávnění k přihlášení a čtení vašeho profilu uživatele. Díky tomuto souhlasu aplikace zajistíte, že jste firemním uživatelem.

![Souhlas uživatele](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Aplikace se pak pokusí synchronizovat seznam uživatelů z vašeho tenanta Azure AD prostřednictvím Microsoft Graph. Pokud to nepomůže, požádá vás (Správce klienta), aby připojil vašeho tenanta k aplikaci.

Aplikace pak vyzve k zadání oprávnění ke čtení seznamu uživatelů ve vašem tenantovi.

![Souhlas správce](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Po udělení oprávnění jste se z aplikace odhlásili. Toto přihlášení zajišťuje, že všechny existující přístupové tokeny pro Microsoft Graph budou odebrány z mezipaměti tokenů. Po opětovném přihlášení bude mít nově získaný token potřebná oprávnění k volání Microsoft Graph.


Když udělíte oprávnění, aplikace se pak může v libovolném bodě dotazovat na uživatele. Můžete to ověřit tak, že vyberete tlačítko **synchronizovat uživatele** a aktualizujete seznam uživatelů. Zkuste přidat nebo odebrat uživatele a znovu synchronizovat seznam. (Ale Všimněte si, že aplikace synchronizuje jenom první stránku uživatelů.)

## <a name="about-the-code"></a>O kódu

Příslušný kód pro tuto ukázku je v následujících souborech:

- **App_start \Startup.auth.cs**, **Controllers\AccountController.cs**: počáteční přihlášení. Konkrétně akce na řadiči mají atribut **autorizovat** , který vynutí, aby se uživatel přihlásil. Aplikace používá [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) k přihlášení uživatele.
- **Controllers\SyncController.cs**: synchronizuje seznam uživatelů s místním úložištěm v paměti.
- **Controllers\UserController.cs**: zobrazení seznamu uživatelů z místního úložiště v paměti.
- **Controllers\AccountController.cs**: nabývá oprávnění od správce tenanta pomocí koncového bodu souhlasu správce.

## <a name="re-create-the-sample-app"></a>Opětovné vytvoření ukázkové aplikace

1. V aplikaci Visual Studio vytvořte nový projekt **C#** **webové aplikace Visual ASP.NET (.NET Framework)** . 
1. Na další obrazovce vyberte šablonu projektu **MVC** . Přidejte také složku a základní odkazy pro **webové rozhraní API**, protože později přidáte kontroler webového rozhraní API. Nechejte vybraný režim ověřování projektu jako výchozí: **bez ověřování**.
1. V okně **Průzkumník řešení** vyberte projekt a vyberte klávesu **F4** . 
1. V okně Vlastnosti projektu nastavte možnost **SSL povoleno** na **hodnotu true**. Poznamenejte si informace v **adrese URL protokolu SSL**. Budete je potřebovat při konfiguraci registrace této aplikace v Azure Portal.
1. Přidejte následující balíčky NuGet pro middleware OWIN ASP.NET: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. Ve složce **app_start** :
   1. Vytvořte třídu s názvem **Startup.auth.cs**. 
   1. Odebrat **. App_Start** z názvu oboru názvů. 
   1. Nahraďte kód pro **spouštěcí** třídu kódem ze stejného souboru ukázkové aplikace.       
   Nezapomeňte vzít v úvahu definici celé třídy. Definice se změní z **spuštění veřejné třídy** na **veřejnou částečnou třídu.**
1. V **Startup.auth.cs**vyřešte chybějící odkazy přidáním příkazů, které jsou **navrženy pomocí technologie** IntelliSense sady Visual Studio.
1. Klikněte pravým tlačítkem na projekt, vyberte **Přidat**a pak vyberte **Třída**.
1. Do vyhledávacího pole zadejte **Owin**. **Spouštěcí třída Owin** se zobrazuje jako výběr. Vyberte ji a pojmenujte třídu **Startup.cs**.
1. V **Startup.cs**nahraďte kód pro **spouštěcí** třídu kódem ze stejného souboru ukázkové aplikace. Znovu si všimněte, že se změní definice z **spuštění veřejné třídy** na **veřejnou částečnou třídu**.
1. Ve složce **modely** přidejte novou třídu s názvem **MsGraphUser.cs**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler MVC 5 – prázdná instance s** názvem **AccountController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler MVC 5 – prázdná instance s** názvem **UserController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Přidejte nový **kontroler webového rozhraní API 2 – prázdná instance s** názvem **SyncController**. Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Pro uživatelské rozhraní ve složce **Views\Account** přidejte tři **prázdné (bez modelu) zobrazení** instancí s názvem **GrantPermissions**, **index**a **UserMismatch**. Přidejte a jeden pojmenovaný **index** do složky **Views\User** . Nahraďte implementaci obsahem souboru se stejným názvem z ukázky.
1. Aktualizujte **shared\_layout. cshtml** a **Home\Index.cshtml** pro správné propojení různých zobrazení.

## <a name="deploy-the-sample-to-azure"></a>Nasazení ukázky do Azure

Tento projekt má webové aplikace a projekty webového rozhraní API. Pokud je chcete nasadit na Azure websites, proveďte následující kroky pro každé z nich:

1. Vytvořte web Azure.
1. Publikujte webovou aplikaci a webová rozhraní API na web.
1. Aktualizujte klienty pro volání webu místo IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Vytvoření a publikování dotnet-web-démon-v2 na webu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu vyberte **vytvořit prostředek**.
1. Vyberte **Webová** **aplikace**web > a potom zadejte název svého webu. Například pojmenujte ho **dotnet-web-daemon-v2-contoso.azurewebsites.NET**.
1. Vyberte informace pro **předplatné**, **skupinu prostředků**a **plán a umístění služby App Service**. **Operační** systém je **Windows**a **publikování** je **kód**.
1. Vyberte **vytvořit** a počkejte, než se služba App Service vytvoří.
1. Až se vám zobrazí oznámení o **úspěšném nasazení** , vyberte **Přejít k prostředku** a přejít na nově vytvořenou službu App Service.
1. Po vytvoření webu ho Najděte na **řídicím panelu** a vyberte ho a otevřete tak obrazovku s **přehledem** služby App Service.
1. Na kartě **Přehled** služby App Service stáhněte profil publikování, a to tak, že vyberete odkaz **získat profil publikování** a uložíte ho. Můžete použít další mechanismy nasazení, jako je například nasazení ze správy zdrojového kódu.
1. Přepněte do sady Visual Studio a potom:
   1. Přejít do projektu **dotnet-web-démon-v2** . 
   1. Klikněte pravým tlačítkem na projekt v Průzkumník řešení a pak vyberte **publikovat**.
   1. Na dolním panelu vyberte **Importovat profil** a importujte profil publikování, který jste si stáhli dříve.
1. Vyberte **Konfigurovat**.
1. Na kartě **připojení** aktualizujte cílovou adresu URL tak, aby používala https. Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Vyberte **Další**.
1. Na kartě **Nastavení** se ujistěte, že je zaškrtnuté políčko **Povolit ověřování organizace** .  
1. Vyberte **Save** (Uložit). Na hlavní obrazovce vyberte **publikovat** .

Visual Studio projekt zveřejní a automaticky otevře prohlížeč na adrese URL projektu. Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšná.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualizace registrace aplikace tenanta Azure AD pro dotnet-web-démon-v2

1. Vraťte se na portál [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**.
1. Vyberte aplikaci **dotnet-web-démon-v2** .
1. Na stránce **ověřování** pro vaši aplikaci aktualizujte pole **Adresa URL pro odhlášení** pomocí adresy vaší služby. Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. V nabídce **značky** aktualizujte adresu **URL domovské stránky** na adresu vaší služby. Použijte například [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Konfiguraci uložte.
1. Přidejte stejnou adresu URL do seznamu hodnot v nabídce **ověřování** **identifikátory URI přesměrování** > . Pokud máte více adres URL pro přesměrování, ujistěte se, že je k dispozici nová položka, která pro každou adresu URL přesměrování používá identifikátor URI služby App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace](#register-your-application) .  Pokud chcete aplikaci odebrat, postupujte podle pokynů v části [odebrání aplikace vytvořené vámi nebo vaší organizací](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Podpora

K získání podpory od komunity použijte [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) .
Nejdřív si položte otázky na Stack Overflow a Projděte si stávající problémy, abyste viděli, jestli se někdo na svůj dotaz dotazoval.
Ujistěte se, že vaše dotazy nebo komentáře jsou označené "ADAL", "msal" a "dotnet".

Pokud v ukázce zjistíte chybu, vyřešte prosím problém na [problémech na GitHubu](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Pokud v MSAL.NET zjistíte chybu, vyřešte problém na [MSAL.NET GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Pokud chcete zadat doporučení, navštivte [stránku hlas uživatele](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o různých [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md) , které podporuje platforma Microsoft Identity Platform.

Další informace najdete v následující Koncepční dokumentaci:

- [Tenantů v Azure Active Directory](single-and-multi-tenant-apps.md)
- [Prostředí vyjádření souhlasu s aplikací Azure AD](application-consent-experience.md)
- [Přihlášení libovolného Azure Active Directory uživatele pomocí vzoru víceklientské aplikace](howto-convert-app-to-be-multi-tenant.md)
- [Vysvětlení souhlasu uživatele a správce](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Aplikační a instanční objekty v Azure Active Directory](app-objects-and-service-principals.md)
- [Rychlý Start: registrace aplikace s platformou Microsoft identity](quickstart-register-app.md)
- [Rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
- [Získání tokenu pro aplikaci pomocí toků přihlašovacích údajů klienta](msal-client-applications.md)

Pro jednodušší aplikaci démona víceklientské konzole si přečtěte článek [rychlý Start procesu .NET Core](quickstart-v2-netcore-daemon.md).
