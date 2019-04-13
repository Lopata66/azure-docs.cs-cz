---
title: Vytvoření aplikace ASP.NET Core v Linuxu – služba Azure App Service | Dokumentace Microsoftu
description: Během několika minut můžete nasadit svou první aplikaci Hello World v .NET Core do služby App Service v Linuxu.
keywords: azure app service, web app, dotnet, core, linux, oss
services: app-service
documentationCenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: da4d246e098751f650eb6315de3794ad957884d6
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543889"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Vytvoření aplikace ASP.NET Core ve službě App Service v Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Nasazení do služby App Service v _Windows_, naleznete v tématu [vytvoření aplikace ASP.NET Core v Azure](../app-service-web-get-started-dotnet.md).
>

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci [.NET Core](https://docs.microsoft.com/aspnet/core/) ve službě App Service v Linuxu. Vytvoříte aplikaci pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), a pomocí Gitu nasadíte kód .NET Core do aplikace.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://www.microsoft.com/net/core/" target="_blank">Nainstalovat .NET Core</a>.

## <a name="create-the-app-locally"></a>Místní vytvoření aplikace

V okně terminálu na svém počítači vytvořte adresář `hellodotnetcore` a přejděte do něj.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Vytvořte novou aplikaci .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. 

Obnovte balíčky NuGet a spusťte aplikaci.

```bash
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `http://localhost:5000`.

Na stránce se zobrazí zpráva **Hello World** z ukázkové aplikace.

![Testování pomocí prohlížeče](media/quickstart-dotnetcore/dotnet-browse-local.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**. Inicializujte úložiště Git pro projekt .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Přejděte do nově vytvořené aplikace. Nahraďte  _&lt;název aplikace >_ názvem vaší aplikace.

```bash
http://<app name>.azurewebsites.net
```

Zde je, jak by měla vypadat nové aplikace:

![Stránka prázdné aplikace](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód .NET Core je spuštěný ve službě App Service v Linuxu s použitím integrované image.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci .NET Core do služby App Service v Linuxu.

## <a name="update-and-redeploy-the-code"></a>Aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor _Startup.cs_. Proveďte malou změnu textu ve volání metody `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a stiskněte tlačítko Aktualizovat.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Přejděte <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> ke správě aplikace, které jste vytvořili.

V levé nabídce klikněte na tlačítko **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-dotnetcore/portal-app-service-list.png)

Se zobrazí stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)
