---
title: Profilování webových aplikací ASP.NET Core Azure s Linuxem pomocí Application Insights Profiler | Dokumentace Microsoftu
description: Koncepční přehled a podrobný kurz o tom, jak používat Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306486"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilování webových aplikací ASP.NET Core Azure s Linuxem pomocí Application Insights Profiler

Tato funkce je aktuálně ve verzi Preview.

Zjistěte, jak dlouho se stráven v každé metody živé webové aplikace při používání [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler je teď k dispozici pro webové aplikace ASP.NET Core, které jsou hostované v systému Linux ve službě Azure App Service. Tento průvodce obsahuje podrobný návod jak se můžou shromažďovat trasování Profiler pro ASP.NET Core Linuxové webové aplikace.

Po dokončení tohoto návodu, vaše aplikace může shromažďovat trasování Profiler jako trasování, které jsou uvedené na obrázku. V tomto příkladu trasování Profiler označuje, že je na konkrétní webový požadavek pomalé z důvodu čekací čas strávený. *Kritickou cestu* v kódu, který je zpomalení aplikace je označené ikonou bezpečnostní opatření. **o** metoda ve **HomeController** části zpomaluje webové aplikace, protože volání metody **Thread.Sleep** funkce.

![Trasování Profileru](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Požadavky
Následující pokyny platí pro všechna prostředí vývoj pro Windows, Linux a Mac:

* Nainstalujte [.NET Core SDK 2.1.2 nebo novější](https://dotnet.microsoft.com/download/archives).
* Instalace Gitu pomocí následujících pokynů na adrese [Začínáme - instalaci Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Nastavení projektu místně

1. Otevřete na svém počítači okno příkazového řádku. Postupujte podle následujících pokynů pro všechna prostředí vývoj pro Windows, Linux a Mac fungovat.

1. Vytvořte webovou aplikaci ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Změňte v pracovním adresáři do kořenové složky projektu.

1. Přidání balíčku NuGet pro shromažďování trasování Profiler:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Povolte Application Insights v souboru Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Povolte Profiler v souboru Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Přidat řádek kódu **HomeController.cs** části náhodně zpoždění pár sekund:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Uložte a potvrďte změny do místního úložiště:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Vytvořit Linuxovou webovou aplikaci k hostování projektu

1. Vytvoření prostředí webové aplikace pomocí služby App Service v Linuxu:

    ![Vytvořit Linuxovou webovou aplikaci](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Vytvořte přihlašovací údaje pro nasazení:

    > [!NOTE]
    > Zaznamenejte heslo pro pozdější použití při nasazení vaší webové aplikace.

    ![Vytvořte přihlašovací údaje nasazení](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Vyberte možnosti nasazení. Nastavení místního úložiště Git ve webové aplikaci podle pokynů na portálu Azure portal. Automaticky se vytvoří úložiště Git.

    ![Nastavení úložiště Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Další možnosti nasazení najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Nasazení projektu

1. V okně příkazového řádku přejděte do kořenové složky pro váš projekt. Přidáte vzdálené úložiště Git tak, aby odkazoval na úložiště ve službě App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Použití **uživatelské jméno** , který jste použili k vytvoření přihlašovací údaje pro nasazení.
    * Použití **název aplikace** , který jste použili k vytvoření webové aplikace pomocí služby App Service v Linuxu.

2. Nasadíte projekt doručením (push) změny do Azure:

    ```
    git push azure master
    ```

Byste měli vidět výstup podobný následujícímu příkladu:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Přidejte Application Insights a monitorujte vaše webové aplikace

1. [Vytvořte prostředek Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Kopírovat **Instrumentační klíč** hodnotu prostředku Application Insights a nastavte následující nastavení ve službě web apps:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Při změně nastavení aplikací, lokality se automaticky restartuje. Jsou použita nová nastavení, Profiler okamžitě se spustí po dobu dvou minut. Profiler pak spustí dvě minuty každou hodinu.

3. Generovat část provozu na váš web. Provoz můžete vygenerovat aktualizací webu **o** stránky několikrát.

4. Počkejte přibližně 2 až 5 minut, než se události, které mají agregované na Application Insights.

5. Přejděte do služby Application Insights **výkonu** podokně webu Azure Portal. Můžete zobrazit trasování Profiler v pravém dolním podokně.

    ![Zobrazit trasy Profiler](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Známé problémy

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Profil se tlačítko nefunguje pro Linux Profiler
App Insights profiler verzi Linuxu zatím nepodporuje na vyžádání profilace pomocí daného profilu teď tlačítko.


## <a name="next-steps"></a>Další postup
Pokud používáte vlastní kontejnery, které jsou hostované ve službě Azure App Service, postupujte podle pokynů v [ povolit Profiler služby pro kontejnerizované aplikace ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) povolit Application Insights Profiler.

Nahlásit případné problémy nebo návrhy k úložišti Application Insights GitHub: [ApplicationInsights-Profiler-AspNetCore: Problémy s](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
