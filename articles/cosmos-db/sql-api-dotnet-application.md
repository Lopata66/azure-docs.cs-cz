---
title: Kurz webové aplikace ASP.NET Core MVC pomocí Azure Cosmos DB
description: Kurz ASP.NET Core MVC k vytvoření webové aplikace MVC pomocí Azure Cosmos DB. Data JSON a Access se budou ukládat z aplikace TODO hostované v kurzu Azure App Service-ASP NET Core MVC krok za krokem.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.openlocfilehash: c7e164420b02be35069103ac06238d56449eb7ef
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996738"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Kurz: vývoj webové aplikace ASP.NET Core MVC pomocí Azure Cosmos DB pomocí sady .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

V tomto kurzu se dozvíte, jak pomocí Azure Cosmos DB ukládat data a přistupovat k nim z aplikace ASP.NET MVC hostované v Azure. V tomto kurzu použijete sadu .NET SDK v3. Následující obrázek ukazuje webovou stránku, kterou vytvoříte pomocí ukázky v tomto článku:

![Snímek webové aplikace MVC seznamu úkolů vytvořené v tomto kurzu – Kurz ASP NET Core MVC krok za krokem](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Pokud nemáte čas k dokončení kurzu, můžete si stáhnout kompletní ukázkový projekt z [GitHubu][GitHub].

Tento kurz zahrnuje:

> [!div class="checklist"]
>
> * Vytvoření účtu Azure Cosmos
> * Vytvoření aplikace ASP.NET Core MVC
> * Připojení aplikace k Azure Cosmos DB
> * Provádění operací vytvoření, čtení, aktualizace a odstranění (CRUD) na datech

> [!TIP]
> V tomto kurzu se předpokládá, že máte předchozí zkušenosti s používáním ASP.NET Core MVC a Azure App Service. Pokud ASP.NET Core nebo požadované [nástroje](#prerequisites)nepoužíváte, doporučujeme si stáhnout kompletní ukázkový projekt z [GitHubu][GitHub], přidat požadované balíčky NuGet a spustit ho. Po sestavení projektu si můžete projít tento článek, abyste získali přehled o kódu v kontextu projektu.

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte následující zdroje:

* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Všechny snímky obrazovky v tomto článku jsou z Microsoft Visual Studio Community 2019. Pokud používáte jinou verzi, vaše obrazovky a možnosti se nemusí zcela shodovat. Řešení by mělo fungovat, pokud splňujete požadavky.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Krok 1: vytvoření účtu Azure Cosmos

Pojďme začít vytvořením účtu Azure Cosmos. Pokud již máte Azure Cosmos DB účet rozhraní SQL API nebo pokud používáte emulátor Azure Cosmos DB, přeskočte na [Krok 2: vytvoření nové aplikace ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

V další části vytvoříte novou ASP.NET Core aplikaci MVC.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Krok 2: vytvoření nové ASP.NET Core aplikace MVC

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V nástroji **vytvořit nový projekt**vyhledejte a vyberte **ASP.NET Core webové aplikace** pro C#. Pokračujte výběrem tlačítka **Next** (Další).

   ![Vytvořit nový projekt ASP.NET Core webové aplikace](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. V části **Konfigurovat nový projekt**zadejte název *projektu a vyberte* **vytvořit**.

1. V v možnosti **vytvořit novou webovou aplikaci ASP.NET Core**vyberte možnost **Webová aplikace (model-zobrazení-kontroler)**. Pokračujte výběrem **vytvořit** .

   Visual Studio vytvoří prázdnou aplikaci MVC.

1. Pro místní spuštění aplikace ASP.NET vyberte **ladit** > **Spustit ladění** nebo F5.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Krok 3: Přidání balíčku Azure Cosmos DB NuGet do projektu

Teď, když máme většinu ASP.NET Core rozhraní MVC, které potřebujeme pro toto řešení, můžeme přidat balíčky NuGet, které jsou potřebné pro připojení k Azure Cosmos DB.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyhledejte a vyberte **Microsoft. Azure. Cosmos**. Vyberte **Install** (Nainstalovat).

   ![Nainstalovat balíček NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Sada Visual Studio stáhne a nainstaluje balíček Azure Cosmos DB a jeho závislosti.

   K instalaci balíčku NuGet můžete použít taky **konzolu Správce balíčků** . Provedete to tak, že vyberete **nástroje** > **správce** > balíčků NuGet**Konzola správce balíčků**. Na příkazovém řádku zadejte následující příkaz:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Krok 4: nastavení aplikace ASP.NET Core MVC

Nyní přidáme do této aplikace MVC modely, zobrazení a řadiče.

### <a name="add-a-model"></a><a name="add-a-model"></a>Přidání modelu

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku **modely** , vyberte **Přidat** > **třídu**.

1. V **položce Přidat novou položku**pojmenujte novou třídu *Item.cs* a vyberte **Přidat**.

1. Obsah třídy *Item.cs* nahraďte následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB používá k přesunu a uložení dat JSON. Můžete použít `JsonProperty` atribut k řízení, jak JSON serializace a deserializace objekty. `Item` Třída demonstruje `JsonProperty` atribut. Tento kód řídí formát názvu vlastnosti, který odkazuje na JSON. Zároveň přejmenuje vlastnost `Completed`.NET.

### <a name="add-views"></a><a name="add-views"></a>Přidání zobrazení

Nyní vytvoříme následující tři zobrazení.

* Přidat zobrazení položky seznamu
* Přidat zobrazení nové položky
* Přidání zobrazení upravit položku

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Přidat zobrazení položky seznamu

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku **zobrazení** a vyberte **Přidat** > **novou složku**. Pojmenujte *položku*složky.

1. Klikněte pravým tlačítkem na složku prázdná **položka** a pak vyberte **Přidat** > **zobrazení**.

1. V **zobrazení přidat MVC**zadejte následující hodnoty:

   * Do **název zobrazení**zadejte *index*.
   * V **šabloně**vyberte **seznam**.
   * V seznamu **třída modelu**vyberte **položku (todo. Modely)**.
   * Vyberte možnost **použít stránku rozložení** a zadejte *~/views/Shared/_Layout. cshtml*.

   ![Snímek obrazovky se zobrazeným dialogovým oknem přidat zobrazení MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Po přidání těchto hodnot vyberte **Přidat** a nechte Visual Studio vytvořit nové zobrazení šablony.

Po dokončení aplikace Visual Studio otevře soubor *cshtml* , který vytvoří. Tento soubor můžete zavřít v aplikaci Visual Studio. Později se k ní vrátíme.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Přidat zobrazení nové položky

Podobně jako při vytváření zobrazení pro položky seznamu vytvořte nové zobrazení pro vytváření položek pomocí následujících kroků:

1. V **Průzkumník řešení**znovu klikněte pravým tlačítkem na složku **položky** a vyberte **Přidat** > **zobrazení**.

1. V okně **Přidat zobrazení MVC**proveďte následující změny:

   * Do **zobrazení název**zadejte *vytvořit*.
   * V **šabloně**vyberte **vytvořit**.
   * V seznamu **třída modelu**vyberte **položku (todo. Modely)**.
   * Vyberte možnost **použít stránku rozložení** a zadejte *~/views/Shared/_Layout. cshtml*.
   * Vyberte **Přidat**.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Přidání zobrazení upravit položku

A nakonec přidejte zobrazení pro úpravu položky pomocí následujících kroků:

1. Z **Průzkumník řešení**znovu klikněte pravým tlačítkem na složku **položky** a vyberte **Přidat** > **zobrazení**.

1. V okně **Přidat zobrazení MVC**proveďte následující změny:

   * Do pole **Název zobrazení** zadejte *Edit*.
   * V poli **Šablona** vyberte **Edit**.
   * V poli **Třída modelu** vyberte **Položka (todo.Models)**.
   * Vyberte možnost **použít stránku rozložení** a zadejte *~/views/Shared/_Layout. cshtml*.
   * Vyberte **Přidat**.

Po dokončení těchto kroků zavřete všechny dokumenty *cshtml* v aplikaci Visual Studio při návratu do těchto zobrazení později.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Deklarovat a inicializovat služby

Nejprve přidáme třídu, která obsahuje logiku pro připojení a použití Azure Cosmos DB. Pro tento kurz zapouzdřeme tuto logiku do třídy s názvem `CosmosDBService` a z rozhraní s názvem. `ICosmosDBService` Tato služba provádí operace CRUD. Také provádí operace čtení informačního kanálu, jako je například výpis neúplných položek, vytváření, úpravy a odstraňování položek.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **novou složku**. Pojmenujte složku *služby*.

1. Klikněte pravým tlačítkem na složku **služby** a vyberte **Přidat** > **třídu**. Pojmenujte novou třídu *CosmosDBService* a vyberte **Přidat**.

1. Obsah *CosmosDBService.cs* nahraďte následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klikněte pravým tlačítkem na složku **služby** a vyberte **Přidat** > **třídu**. Pojmenujte novou třídu *ICosmosDBService* a vyberte **Přidat**.

1. Do třídy *ICosmosDBService* přidejte následující kód:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Ve svém řešení otevřete soubor *Startup.cs* a přidejte následující metodu **InitializeCosmosClientInstanceAsync**, která přečte konfiguraci a inicializuje klienta.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. Na stejném souboru nahraďte `ConfigureServices` metodu pomocí:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   Kód v tomto kroku inicializuje klienta na základě konfigurace jako instance typu Singleton, která se má vložit prostřednictvím [Injektáže závislosti v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

   A nezapomeňte změnit výchozí kontroler MVC na `Item` tak, že upravíte trasy v `Configure` metodě stejného souboru:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Definujte konfiguraci v souboru *appSettings. JSON* projektu, jak je znázorněno v následujícím fragmentu kódu:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Přidání kontroleru

1. V **Průzkumník řešení**klikněte pravým tlačítkem na složku **řadiče** a vyberte **Přidat** > **kontroler**.

1. V nástroji **Přidat generování uživatelského rozhraní**vyberte možnost **kontroler MVC – prázdné** a vyberte **Přidat**.

   ![Vybrat kontroler MVC – prázdné v přidat generování uživatelského rozhraní](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Pojmenujte svůj nový kontroler *ItemController*.

1. Obsah *ItemController.cs* nahraďte následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Atribut **ValidateAntiForgeryToken** se tady používá ke zvýšení ochrany této aplikace proti útokům proti padělání žádostí mezi lokalitami. Vaše zobrazení by měla spolupracovat i s tímto tokenem ochrany proti padělání. Další informace a příklady najdete v tématu [prevence útoků CSRF (site-to Request) v aplikaci ASP.NET MVC][Preventing Cross-Site Request Forgery]. Zdrojový kód dostupný na [GitHubu][GitHub] má toto plně implementováno.

Pro zajištění ochrany před útoky na převzetí služeb při selhání používáme také atribut **BIND** u parametru Method. Další informace najdete v tématu [kurz: implementace funkce CRUD s Entity Framework v ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Krok 5: místní spuštění aplikace

K otestování aplikace na místním počítači použijte následující postup:

1. Stisknutím klávesy F5 v aplikaci Visual Studio sestavte aplikaci v režimu ladění. Po sestavení aplikace by se měl spustit prohlížeč se stránkou s prázdnou mřížkou, kterou jsme viděli dříve:

   ![Snímek webové aplikace seznamu úkolů vytvořené v tomto kurzu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Pokud se místo toho aplikace otevře na domovské stránce, připojí `/Item` se k adrese URL.

1. Vyberte odkaz **vytvořit nový** a přidejte hodnoty do polí **název** a **Popis** . Zrušte zaškrtnutí políčka **dokončeno** . Pokud ho vyberete, aplikace přidá novou položku do dokončeného stavu. Položka se již nezobrazuje v počátečním seznamu.

1. Vyberte **Vytvořit**. Aplikace vás pošle zpátky do zobrazení **indexu** a položka se zobrazí v seznamu. Do seznamu **úkolů** můžete přidat několik dalších položek.

    ![Snímek obrazovky s zobrazením indexu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Vyberte **Upravit** vedle **položky** v seznamu. Aplikace otevře zobrazení pro **Úpravy** , kde můžete aktualizovat jakoukoli vlastnost objektu, včetně příznaku **dokončeno** . Pokud vyberete **dokončeno** a vyberete **Uložit**, aplikace zobrazí **položku** jako dokončenou v seznamu.

   ![Snímek obrazovky zobrazení indexu se zaškrtnutým políčkem dokončeno](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Ověřte stav dat ve službě Azure Cosmos DB pomocí [Průzkumníka Cosmos](https://cosmos.azure.com) nebo Průzkumník dat emulátoru Azure Cosmos DB.

1. Po otestování aplikace vyberte CTRL + F5 a zastavte ladění aplikace. Jste připraveni aplikaci nasadit!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Krok 6: nasazení aplikace

Nyní, když je aplikace dokončena a správně funguje se službou Azure Cosmos DB, nasadíme tuto webovou aplikaci ve službě Azure App Service.  

1. Pokud chcete tuto aplikaci publikovat, klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.

1. V **rozevíracím App Service vybrat cíl publikování**vyberte možnost **App Service**.

1. Pokud chcete použít existující profil App Service, zvolte **Vybrat existující**a pak vyberte **publikovat**.

1. V **App Service**vyberte **předplatné**. Použijte filtr **zobrazení** k řazení podle skupiny prostředků nebo typu prostředku.

1. Najděte svůj profil a pak vyberte **OK**. V dalším hledání požadovaného Azure App Service a vyberte **OK**.

   ![Dialogové okno App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Další možností je vytvořit nový profil:

1. Jako v předchozím postupu klikněte pravým tlačítkem myši na projekt v **Průzkumník řešení** a vyberte **publikovat**.
  
1. V **rozevíracím App Service vybrat cíl publikování**vyberte možnost **App Service**.

1. V **rozevíracím výběru vybrat cíl publikování**vyberte **vytvořit novou** a vyberte **publikovat**.

1. V **App Service**zadejte název vaší webové aplikace a příslušné předplatné, skupinu prostředků a plán hostování a pak vyberte **vytvořit**.

   ![Dialogové okno Vytvořit plán App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Během několika sekund Visual Studio publikuje vaši webovou aplikaci a spustí prohlížeč, kde vidíte svůj projekt spuštěný v Azure!

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit webovou aplikaci ASP.NET Core MVC. Vaše aplikace má přístup k datům uloženým v Azure Cosmos DB. Teď můžete pokračovat s těmito prostředky:

* [Dělení ve službě Azure Cosmos DB](./partitioning-overview.md)
* [Začínáme s dotazy SQL](./how-to-sql-query.md)
* [Modelování a dělení dat ve službě Azure Cosmos DB s využitím příkladu z reálného světa](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
