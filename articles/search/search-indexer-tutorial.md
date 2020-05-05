---
title: Kurz C# – indexování dat SQL Azure
titleSuffix: Azure Cognitive Search
description: V tomto kurzu C# se připojte ke službě Azure SQL Database, extrahujte hledaná data a načtěte ji do indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: cab996eb7c0bfccf31ed49294c6aa4b3e8cefc8f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780755"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Kurz: indexování dat SQL Azure pomocí sady .NET SDK

Konfigurace [indexeru](search-indexer-overview.md) pro extrakci prohledávatelných dat ze služby Azure SQL Database a jejich odeslání do indexu vyhledávání v Azure kognitivní hledání. 

Tento kurz používá jazyk C# a [sadu .NET SDK](https://aka.ms/search-sdk) k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření zdroje dat, který se připojuje k Azure SQL Database
> * Vytvoření indexeru
> * Spuštění indexeru pro načtení dat do indexu
> * Dotazování indexu jako ověřovacího kroku

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

Zdrojový kód pro tento kurz je ve složce [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) v úložišti GitHub [Azure-Samples/Search-dotnet-getzačínáme-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) .

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy a Azure SQL Database jako externí zdroj dat. Pokud je to možné, vytvořte obě služby ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být Azure SQL Database v jakékoli oblasti.

### <a name="start-with-azure-sql-database"></a>Začínáme s Azure SQL Database

V tomto kroku vytvoříte externí zdroj dat na Azure SQL Database, který může indexer Procházet. Datovou sadu v Azure SQL Database můžete vytvořit pomocí Azure Portal a souboru *hotelů. SQL* z ukázkového stažení. Azure Kognitivní hledání spotřebovává sloučené sady řádků, jako je například jedna vygenerovaná ze zobrazení nebo dotazu. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Pokud máte existující prostředek Azure SQL Database, můžete do něj přidat tabulku hotelů, počínaje krokem 4.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/).

1. Najděte nebo vytvořte **SQL Database**. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Stránka Nová databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png "Stránka Nová databáze")

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** nasaďte nový server a databázi. Počkejte na nasazení serveru a databáze.

1. V navigačním podokně klikněte na **Editor dotazů (Preview)** a zadejte uživatelské jméno a heslo správce serveru. 

   Pokud přístup odepřete, zkopírujte IP adresu klienta z chybové zprávy a potom klikněte na odkaz **nastavit bránu firewall serveru** a přidejte pravidlo, které umožní přístup z klientského počítače, a to pomocí klientské IP adresy pro daný rozsah. Než se pravidlo projeví, může to trvat několik minut.

1. V editoru dotazů klikněte na **Otevřít dotaz** a přejděte do umístění souboru *hotely. SQL* na místním počítači. 

1. Vyberte tento soubor a klikněte na **Otevřít**. Váš skript by měl vypadat jako na následujícím snímku obrazovky:

   ![Skript SQL](./media/search-indexer-tutorial/sql-script.png "Skript SQL")

1. Kliknutím na **Spustit** dotaz spusťte. V podokně výsledků by se na 3 řádcích měla zobrazit zpráva o úspěšném provedení dotazu.

1. Pokud chcete vrátit sadu řádků z této tabulky, můžete jako krok ověření spustit následující dotaz:

    ```sql
    SELECT * FROM Hotels
    ```

1. Zkopírujte připojovací řetězec ADO.NET pro databázi. V části **Nastavení** > **připojovacích řetězců**zkopírujte připojovací řetězec ADO.NET podobně jako v následujícím příkladu.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Tento připojovací řetězec budete potřebovat při dalším cvičení a nastavení prostředí.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Další součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

Volání rozhraní API vyžadují adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

1. Spusťte Visual Studio a otevřete **DotNetHowToIndexers. sln**.

1. V Průzkumník řešení otevřete **appSettings. JSON** a poskytněte informace o připojení.

1. `searchServiceName`Pokud je úplná adresa URL "https://my-demo-service.search.windows.net", název služby, který se má poskytnout, je "Moje ukázka-služba".

1. Pro `AzureSqlConnectionString`je formát řetězce podobný tomuto:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. V připojovacím řetězci se ujistěte, že připojovací řetězec obsahuje platné heslo. I když se databáze a uživatelská jména zkopírují, je nutné zadat heslo ručně.

## <a name="3---create-the-pipeline"></a>3. vytvoření kanálu

Indexery vyžadují objekt zdroje dat a index. Relevantní kód je ve dvou souborech:

  + **Hotel.cs**obsahující schéma, které definuje index
  + **Program.cs**, obsahující funkce pro vytváření a správu struktur ve vaší službě

### <a name="in-hotelcs"></a>Soubor hotel.cs

Schéma indexu definuje kolekci polí, včetně atributů určujících povolené operace, například jestli je pole fulltextově prohledávatelné, filtrovatelné nebo seřaditelné, jak je znázorněno v následující definici pole pro HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schéma může obsahovat také další elementy, včetně profilů vyhodnocování pro zvýšení skóre vyhledávání, vlastních analyzátorů a dalších konstrukcí. Pro naše účely je však schéma definováno řídce a skládá se pouze z polí, která jsou v ukázkových datových sadách.

### <a name="in-programcs"></a>Soubor Program.cs

Hlavní program obsahuje logiku pro vytváření klienta, index, zdroj dat a indexer. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

Objekt zdroje dat je nakonfigurovaný s nastavením, která jsou specifická pro prostředky Azure SQL Database, včetně [částečného nebo přírůstkového indexování](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) pro využití integrovaných [funkcí detekce změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) v Azure SQL. Ukázková databáze hotelů v Azure SQL má sloupec "obnovitelné odstranění" s názvem **IsDeleted**. Pokud je tento sloupec v databázi nastavený na hodnotu true, indexer odebere odpovídající dokument z indexu služby Azure Kognitivní hledání.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Objekt indexeru je Platform-nezávislá, kde konfigurace, plánování a volání jsou stejné bez ohledu na zdroj. Tento příklad indexeru zahrnuje plán, možnost resetu, která vymaže historii indexeru a volá metodu pro vytvoření a spuštění indexeru hned.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4. sestavení řešení

Stisknutím klávesy F5 Sestavte a spusťte řešení. Program se spustí v režimu ladění. V okně konzoly se bude hlásit stav jednotlivých operací.

   ![Výstup konzoly](./media/search-indexer-tutorial/console-output.png "Výstup konzoly")

Váš kód se spouští místně v sadě Visual Studio a připojuje se k vaší vyhledávací službě v Azure, která se zase připojuje k Azure SQL Database a načítá datovou sadu. U této řady operací existuje několik potenciálních bodů selhání. Pokud se zobrazí chyba, nejdřív ověřte následující podmínky:

+ Zadané informace o připojení k vyhledávací službě se omezují na název služby v tomto kurzu. Pokud jste zadali úplnou adresu URL, operace se zastaví při vytváření indexu s chybou značící selhání připojení.

+ Informace o připojení k databázi v souboru **appsettings.json**. Měl by to být připojovací řetězec ADO.NET získaný z portálu a upravený tak, aby obsahoval platné uživatelské jméno a heslo pro vaši databázi. Uživatelský účet musí mít oprávnění k načtení dat. IP adresa místního klienta musí mít povolený přístup.

+ Omezení prostředků. Vyvoláte si, že úroveň Free má omezení 3 indexy, indexerů a zdrojů dat. Služba, která dosáhne maximálního omezení, nemůže vytvářet nové objekty.

## <a name="5---search"></a>5 – hledání

K ověření vytvoření objektu použijte Azure Portal a pak pomocí **Průzkumníka vyhledávání** Dotazujte index.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby otevřete jednotlivé seznamy a ověřte, zda je objekt vytvořen. **Indexy**, **indexery**a **zdroje dat** budou mít "hotely", "Azure-SQL-indexer" a "Azure-SQL" v uvedeném pořadí.

   ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)

1. Vyberte index hotelů. Na stránce hotely je jako první karta **Průzkumník vyhledávání** . 

1. Kliknutím na tlačítko **Hledat** vydejte prázdný dotaz. 

   Vrátí se tři položky ve vašem indexu jako dokumenty JSON. Průzkumník služby Search vrací dokumenty ve formátu JSON, abyste mohli zobrazit celou jejich strukturu.

   ![Dotazování indexu](./media/search-indexer-tutorial/portal-search.png "Dotazování indexu")
   
1. Dále zadejte hledaný řetězec `search=river&$count=true`. 

   Tento dotaz vyvolá fulltextové vyhledávání výrazu `river` a výsledek bude obsahovat počet odpovídajících dokumentů. Vrácení počtu odpovídajících dokumentů je užitečné ve scénářích testování, když máte velký index s tisíci nebo miliony dokumentů. V tomto případě dotazu odpovídá pouze jeden dokument.

1. Nakonec zadejte hledaný řetězec, který omezí pole ve výstupu JSON pouze na ta, která vás zajímají: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpověď na dotaz je omezená na vybraná pole, což ve výsledku znamená stručnější výstup.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Vzorový kód pro tento kurz kontroluje existující objekty a odstraní je, abyste mohli znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a zdrojů dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení se základy indexování SQL Database, se podíváme na konfiguraci indexeru.

> [!div class="nextstepaction"]
> [Konfigurace indexeru služby Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)