---
title: 'Kurz: Hostitelské RESTful API s CORS'
description: Zjistěte, jak Azure App Service pomáhá hostovat rozhraní RESTful API s podporou CORS. Služba App Service může hostovat webové aplikace front-end u obou koncových a zadních api.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/11/2020
ms.custom: mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 766b860e6c711107472645d84db50412aaba0e12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80046768"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Kurz: Hostování rozhraní RESTful API s CORS v Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Kromě toho zahrnuje App Service integrovanou podporu [Sdílení prostředků různého původu (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) pro rozhraní RESTful API. V tomto kurzu se dozvíte, jak do App Service nasadit aplikaci ASP.NET Core API s podporou CORS. Aplikaci nakonfigurujete pomocí nástrojů příkazového řádku a nasadíte pomocí Gitu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředků App Service pomocí Azure CLI
> * Nasazení rozhraní RESTful API do Azure pomocí Gitu
> * Povolení podpory CORS v App Service

Podle kroků v tomto kurzu můžete postupovat v systémech macOS, Linux a Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalujte Git](https://git-scm.com/).
* [Nainstalujte rozhraní .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Vytvoření místní aplikace ASP.NET Core

V tomto kroku nastavíte místní projekt ASP.NET Core. App Service podporuje stejný pracovní postup i pro rozhraní API napsaná v jiných jazycích.

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.  

Ukázkové úložiště naklonujete spuštěním následujícího příkazu. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Toto úložiště obsahuje aplikaci vytvořenou podle následujícího kurzu: [Generování stránek nápovědy webového rozhraní ASP.NET Core API pomocí Swaggeru](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Aplikace s využitím generátoru Swagger poskytuje [uživatelské rozhraní Swagger](https://swagger.io/swagger-ui/) a koncový bod JSON pro Swagger.

### <a name="run-the-application"></a>Spuštění aplikace

Spuštěním následujících příkazů nainstalujte požadované balíčky, spusťte operace migrace databáze a spusťte aplikaci.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

V prohlížeči přejděte na adresu `http://localhost:5000/swagger` a vyzkoušejte si uživatelské rozhraní Swagger.

![Místně spuštěné rozhraní ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Přejděte na adresu `http://localhost:5000/api/todo`, kde se zobrazí seznam položek úkolů ve formátu JSON.

Přejděte na adresu `http://localhost:5000` a vyzkoušejte si aplikaci v prohlížeči. Později otestujete funkce CORS nastavením aplikace v prohlížeči tak, aby odkazovala na vzdálené rozhraní API v App Service. Kód aplikace v prohlížeči najdete v adresáři _wwwroot_ úložiště.

ASP.NET Core můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

V tomto kroku nasadíte aplikaci .NET Core připojenou k databázi SQL do služby App Service.

### <a name="configure-local-git-deployment"></a>Konfigurace nasazení místního gitu

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

V prohlížeči přejděte na adresu `http://<app_name>.azurewebsites.net/swagger` a vyzkoušejte si uživatelské rozhraní Swagger.

![Rozhraní ASP.NET Core API spuštěné v Azure App Service](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Přejděte na adresu `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json`, kde se zobrazí soubor _swagger.json_ pro vaše nasazené rozhraní API.

Přejděte na adresu `http://<app_name>.azurewebsites.net/api/todo`, kde se zobrazí funkční nasazené rozhraní API.

## <a name="add-cors-functionality"></a>Přidání funkcí CORS

Dále pro své rozhraní API povolíte integrovanou podporu CORS v App Service.

### <a name="test-cors-in-sample-app"></a>Test CORS v ukázkové aplikaci

V místním úložišti otevřete soubor _wwwroot/index.html_.

Na řádku 51 nastavte proměnnou `apiEndpoint` na adresu URL vašeho nasazeného rozhraní API (`http://<app_name>.azurewebsites.net`). Nahraďte _ \<název aplikace>_ názvem aplikace ve službě App Service.

V místním okně terminálu znovu spusťte ukázkovou aplikaci.

```bash
dotnet run
```

Přejděte do aplikace v prohlížeči na adrese `http://localhost:5000`. Otevřete okno vývojářských nástrojů`Ctrl` + `Shift` + `i` v prohlížeči (v Chromu pro Windows) a zkontrolujte kartu **Konzola.** Nyní by se měla `No 'Access-Control-Allow-Origin' header is present on the requested resource`zobrazit chybová zpráva .

![Chyba CORS v prohlížeči](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Kvůli neshodě domén aplikace v prohlížeči (`http://localhost:5000`) a vzdáleného prostředku (`http://<app_name>.azurewebsites.net`) a skutečnosti, že vaše rozhraní API v App Service neodesílá hlavičku `Access-Control-Allow-Origin`, zabránil váš prohlížeč aplikaci v načtení obsahu z jiné domény.

V produkčním prostředí by vaše aplikace v prohlížeči měla místo adresy URL místního hostitele veřejnou adresu URL, ale postup pro povolení CORS pro adresu URL místního hostitele je stejný jako u veřejné adresy URL.

### <a name="enable-cors"></a>Povolení CORS 

V prostředí Cloud Shell povolte CORS na adresu [`az resource update`](/cli/azure/resource#az-resource-update) URL vašeho klienta pomocí příkazu. Nahraďte _ &lt;zástupný_ symbol názvu aplikace>.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

Ve vlastnosti `properties.cors.allowedOrigins` můžete nastavit více než jednu adresu URL klienta (`"['URL1','URL2',...]"`). Můžete také povolit všechny adresy URL klientů pomocí `"['*']"`.

> [!NOTE]
> Pokud vaše aplikace vyžaduje, aby byly odeslány přihlašovací údaje, jako `ACCESS-CONTROL-ALLOW-CREDENTIALS` jsou soubory cookie nebo ověřovací tokeny, může prohlížeč vyžadovat záhlaví odpovědi. Chcete-li to povolit `properties.cors.supportCredentials` `true` ve službě App Service, nastavte na v konfiguraci CORS. Tuto možnost nelze `allowedOrigins` `'*'`povolit, pokud obsahuje .

### <a name="test-cors-again"></a>Opětovný test CORS

Aktualizujte aplikaci v prohlížeči na adrese `http://localhost:5000`. Chybová zpráva v okně **Console** (Konzola) už je pryč a zobrazí se data z nasazeného rozhraní API, se kterými můžete pracovat. Vaše vzdálené rozhraní API nyní podporuje CORS pro vaši místně spuštěnou aplikaci v prohlížeči. 

![Úspěch CORS v prohlížeči](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Blahopřejeme! Teď máte v Azure App Service spuštěné rozhraní API s podporou CORS.

## <a name="app-service-cors-vs-your-cors"></a>CORS v App Service vs. vlastní CORS

Místo CORS v App Service můžete použít vlastní CORS poskytující větší flexibilitu. Například můžete chtít určit různé povolené zdroje pro různé trasy a metody. Vzhledem k tomu že CORS v App Service umožňuje zadat jednu sadu povolených zdrojů pro všechny trasy a metody rozhraní API, chtěli byste použít vlastní kód CORS. Informace o tom, jak se to dělá v ASP.NET Core, najdete tématu o [povolení prostředků různého původů (CORS)](/aspnet/core/security/cors).

> [!NOTE]
> Nedoporučujeme používat společně CORS v App Service a vlastní kód CORS. Při společném použití bude mít přednost CORS v App Service a váš vlastní kód CORS nebude mít žádný vliv.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvoření prostředků App Service pomocí Azure CLI
> * Nasazení rozhraní RESTful API do Azure pomocí Gitu
> * Povolení podpory CORS v App Service

V dalším kurzu se dozvíte, jak ověřovat a autorizovat uživatele.

> [!div class="nextstepaction"]
> [Kurz: Komplexní ověřování a autorizace uživatelů](app-service-web-tutorial-auth-aad.md)
