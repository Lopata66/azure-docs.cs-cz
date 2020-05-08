---
title: Kurz C# – optimalizace indexování pomocí rozhraní API push
titleSuffix: Azure Cognitive Search
description: Naučte se efektivně indexovat data pomocí rozhraní push API služby Azure Kognitivní hledání. Tento kurz a vzorový kód jsou v jazyce C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 85ac56eb20eabf308d6686a047d8c5ede914fed9
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966439"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Kurz: optimalizace indexování pomocí rozhraní API push

Azure Kognitivní hledání podporuje [dva základní přístupy](search-what-is-data-import.md) k importu dat do indexu vyhledávání: *vkládání* dat do indexu prostřednictvím kódu programu nebo nasměrování služby [Azure kognitivní hledání indexerem](search-indexer-overview.md) na podporovaný zdroj dat, který se v datech *vyžádá* .

V tomto kurzu se dozvíte, jak efektivně indexovat data pomocí [modelu nabízených oznámení](search-what-is-data-import.md#pushing-data-to-an-index) pomocí dávkování požadavků a pomocí exponenciální strategie omezení rychlosti opakování. Aplikaci si můžete [Stáhnout a spustit](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Tento článek vysvětluje klíčové aspekty aplikace a faktory, které je potřeba vzít v úvahu při indexování dat.

Tento kurz používá jazyk C# a [sadu .NET SDK](https://aka.ms/search-sdk) k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření indexu
> * Testování různých velikostí dávek a určení nejefektivnější velikosti
> * Asynchronní data indexu
> * Použití více vláken ke zvýšení rychlosti indexování
> * Použití exponenciální strategie omezení rychlosti opakování k opakovanému pokusu o neúspěšné položky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu jsou vyžadovány následující služby a nástroje.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), libovolná edice. Vzorový kód a pokyny byly testovány na bezplatnou edici Community.

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného.

<a name="get-service-info"></a>

## <a name="download-files"></a>Stažení souborů

Zdrojový kód tohoto kurzu je ve složce [optimzize-data-Indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) v úložišti GitHub [Azure-Samples/Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="key-considerations"></a>Klíčové aspekty

Při vkládání dat do indexu existuje několik klíčových důležitých informací, které mají vliv na rychlost indexování. Další informace o těchto faktorech najdete v [článku index rozsáhlých datových sad](search-howto-large-index.md).

Šest klíčových faktorů, které je potřeba vzít v úvahu:

+ **Úroveň služby a počet oddílů/replik** – přidávání oddílů a zvýšení úrovně zvýší obě rychlosti indexování.
+ **Schéma indexu** – přidávání polí a přidávání dalších vlastností do polí (například *prohledávatelné*, *plošky*nebo *filtrovatelné*) snižují rychlost indexování.
+ **Velikost dávky** – optimální velikost dávky se liší v závislosti na schématu indexu a datové sadě.
+ **Počet vláken/pracovníků** – jedno vlákno nebude plně využívat rychlosti indexování.
+ **Strategie opakování** – pro optimalizaci indexování by se měla použít exponenciální strategie omezení rychlosti opakování.
+ **Rychlosti přenosu dat v síti** – rychlosti přenosu dat můžou být omezující faktor. Indexujte data v rámci vašeho prostředí Azure, abyste zvýšili rychlost přenosu dat.


## <a name="1---create-azure-cognitive-search-service"></a>1. vytvoření služby Azure Kognitivní hledání

K dokončení tohoto kurzu budete potřebovat službu Azure Kognitivní hledání, kterou můžete [vytvořit na portálu](search-create-service-portal.md). Doporučujeme používat stejnou úroveň, kterou plánujete používat v produkčním prostředí, abyste mohli přesně testovat a optimalizovat rychlosti indexování.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

Volání rozhraní API vyžadují adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

1. Spusťte Visual Studio a otevřete **OptimizeDataIndexing. sln**.
1. V Průzkumník řešení otevřete **appSettings. JSON** a poskytněte informace o připojení.
1. `searchServiceName`Pokud je úplná adresa URL "https://my-demo-service.search.windows.net", název služby, který se má poskytnout, je "Moje ukázka-služba".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3. Prozkoumejte kód

Po aktualizaci souboru *appSettings. JSON*by měl být ukázkový program v **OptimizeDataIndexing. sln** připravený k sestavování a spouštění.

Tento kód je odvozen z rychlého startu v [C#](search-get-started-dotnet.md). Podrobnější informace o základech práce se sadou .NET SDK najdete v tomto článku.

Tato jednoduchá aplikace konzoly C#/.NET provádí následující úlohy:

+ Vytvoří nový index na základě struktury dat třídy hotelu v jazyce C# (která také odkazuje na třídu Address).
+ Testuje různé velikosti dávek a určí tak nejúčinnější velikost.
+ Asynchronně indexuje data
    + Použití více vláken ke zvýšení rychlosti indexování
    + Použití exponenciální strategie omezení rychlosti opakování k opakovanému pokusu o neúspěšné položky

 Před spuštěním programu chvíli počkejte a prostudujte si kód a definice indexu pro tuto ukázku. Příslušný kód je v několika souborech:

  + **Hotel.cs** a **Address.cs** obsahuje schéma, které definuje index.
  + **DataGenerator.cs** obsahuje jednoduchou třídu, která usnadňuje vytváření velkých objemů dat hotelů.
  + **ExponentialBackoff.cs** obsahuje kód pro optimalizaci procesu indexování, jak je popsáno níže.
  + **Program.cs** obsahuje funkce, které vytvářejí a odstraňují index služby Azure kognitivní hledání, indexuje dávky dat a testuje různé velikosti dávek.

### <a name="creating-the-index"></a>Vytvoření indexu

Tento ukázkový program používá sadu .NET SDK k definování a vytvoření indexu služby Azure Kognitivní hledání. Využívá třídu [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) k vygenerování struktury indexu z třídy datového modelu jazyka C#.

Datový model je definován třídou hotelu, která také obsahuje odkazy na třídu Address. FieldBuilder projde k podrobnostem v různých definicích tříd a vygeneruje složitou strukturu dat pro index. Značky metadat slouží k definování atributů každého pole, jako je například, zda je možné prohledávat nebo řadit.

Následující fragmenty kódu ze souboru **Hotel.cs** ukazují, jak lze určit jedno pole a odkaz na jinou třídu datového modelu.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

V souboru **program.cs** je index definován s názvem a kolekcí polí generovaných `FieldBuilder.BuildForType<Hotel>()` metodou a pak vytvořen takto:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>Generování dat

V souboru **DataGenerator.cs** je implementována jednoduchá třída pro generování dat pro testování. Jediným účelem této třídy je, aby bylo snadné generovat velký počet dokumentů s jedinečným ID pro indexování.

Chcete-li získat seznam 100 000 hotelů s jedinečnými identifikátory, spusťte následující dva řádky kódu:

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

K dispozici jsou dvě velikosti hotelů pro testování v této ukázce: **malá** a **Velká**.

Schéma indexu může mít výrazný vliv na rychlosti indexování. Z důvodu tohoto dopadu má smysl převést tuto třídu tak, aby po spuštění tohoto kurzu generovala data odpovídající schématu indexu.

## <a name="4---test-batch-sizes"></a>4. velikosti dávek testu

Azure Kognitivní hledání podporuje následující rozhraní API pro načtení jednoho nebo více dokumentů do indexu:

+ [Přidávání, aktualizace a odstraňování dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Třída indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) nebo [třída indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

Indexování dokumentů v dávkách významně vylepšuje výkon při indexování. Tyto dávky můžou mít až 1000 dokumentů nebo až o 16 MB na jednu dávku.

Stanovení optimální velikosti dávky pro vaše data je klíčovou součástí Optimalizace rychlosti indexování. Optimální velikost dávky ovlivňují tyto dva primární faktory:

+ Schéma indexu
+ Velikost dat

Vzhledem k tomu, že je optimální velikost dávky závislá na vašem indexu a datech, nejlepším způsobem je otestovat různé velikosti dávek, abyste zjistili, jaké jsou výsledky nejrychlejší indexování pro váš scénář.

Následující funkce ukazuje jednoduchý přístup k testování velikosti dávek.

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

Vzhledem k tomu, že ne všechny dokumenty mají stejnou velikost (i když jsou v této ukázce), odhadneme velikost dat, která posíláme do vyhledávací služby. Provedeme to pomocí funkce níže, která nejprve převede objekt na JSON a pak určí jeho velikost v bajtech. Tato technika nám umožňuje určit, které velikosti dávek jsou nejúčinnější z hlediska rychlosti indexování MB/s.

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

Funkce vyžaduje `ISearchIndexClient` a také počet pokusů, které chcete testovat u každé velikosti dávky. V případě, že pro každou dávku může dojít k nějakým proměnlivým výsledkům, zkusíme každou dávku ve výchozím nastavení třikrát udělat, aby byly výsledky lépe statisticky důležité.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

Po spuštění funkce by se ve vaší konzole měl zobrazit výstup podobný následujícímu:

   ![Výstup funkce velikosti dávky testu](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Výstup funkce velikosti dávky testu")

Určete, která velikost dávky je nejúčinnější a pak použijte tuto velikost dávky v dalším kroku tohoto kurzu. Může se zobrazit stabilní úrovně v MB/s napříč různými velikostmi dávek.

## <a name="5---index-data"></a>5. indexovat data

Teď, když jsme identifikovali velikost dávky, kterou hodláte použít, je dalším krokem začít indexovat data. Chcete-li efektivně indexovat data, Tato ukázka:

* Používá více vláken a pracovních procesů.
* Implementuje exponenciální strategii omezení rychlosti opakování.

### <a name="use-multiple-threadsworkers"></a>Použití více vláken/pracovníků

Abyste plně využili výhod indexování Kognitivní hledání Azure, budete pravděpodobně muset použít více vláken k posílání požadavků dávkového indexování do služby současně.  

Několik klíčových doporučení uvedených výše má vliv na optimální počet vláken. Tuto ukázku a test můžete upravit pomocí různých počtů vláken, abyste zjistili optimální počet vláken pro váš scénář. Pokud ale máte několik souběžně běžících vláken, měli byste být schopni využít většinu zisků z hlediska efektivity.

Při navýšení požadavků na službu vyhledávání dojde v případě, že se zobrazí [stavové kódy http](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) indikující, že požadavek nebyl zcela úspěšný. Při indexování jsou dva běžné stavové kódy HTTP:

+ **služba 503 není k dispozici** – Tato chyba znamená, že systém je zatížen velkým zatížením a váš požadavek nejde v tuto chvíli zpracovat.
+ **207 s více stavy** – Tato chyba znamená, že některé dokumenty byly úspěšně dokončeny, ale nejméně jedna se nezdařila.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementace exponenciální strategie omezení rychlosti opakování

Pokud dojde k selhání, žádosti by se měly opakovat pomocí [exponenciální strategie omezení rychlosti opakování](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Sada .NET SDK pro Azure Kognitivní hledání automaticky opakuje 503s a další neúspěšné požadavky, ale budete muset implementovat vlastní logiku a zkusit to znovu 207s. K implementaci strategie opakování můžete také použít Open Source nástroje, jako je [Polly](https://github.com/App-vNext/Polly) . 

V této ukázce implementujeme naši vlastní exponenciální strategii omezení rychlosti opakování. K implementaci této strategie Začínáme definováním některých proměnných, včetně `maxRetryAttempts` a počátečního `delay` pro neúspěšnou žádost:

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Je důležité zachytit [IndexBatchException](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet) , protože tyto výjimky signalizují, že operace indexování se pouze částečně zdařila (207s). Položky, které selhaly, by se `FindFailedActionsToRetry` měly opakovat pomocí metody, která usnadňuje vytvoření nové dávky obsahující pouze neúspěšné položky.

Výjimky jiné než `IndexBatchException` by měly být také zachyceny a označovaly, že žádost byla zcela neúspěšná. Tyto výjimky jsou méně běžné, zejména se sadou .NET SDK při opakovaném pokusu o 503s automaticky.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Odtud rozbalíme exponenciální kód omezení rychlosti do funkce, aby ho bylo možné snadno volat.

Pro správu aktivních vláken se pak vytvoří další funkce. Pro zjednodušení Tato funkce není zde obsažena, ale je možné ji najít v [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs). Funkci lze volat pomocí následujícího příkazu, kde `hotels` jsou data, která chceme nahrát, `1000` je velikost dávky a `8` je počet souběžných vláken:

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

Po spuštění funkce by se měl zobrazit výstup podobný následujícímu:

![Výstup funkce index data](media/tutorial-optimize-data-indexing/index-data-start.png "Výstup funkce index data")

Pokud dávka dokumentů selže, je vytištěna chyba indikující selhání a došlo k pokusu o opakování dávky:

![Chyba z funkce data indexu](media/tutorial-optimize-data-indexing/index-data-error.png "Výstup funkce velikosti dávky testu")

Po dokončení spuštění funkce můžete ověřit, že všechny dokumenty byly přidány do indexu.

## <a name="6---explore-index"></a>6. Prozkoumejte index

Po spuštění programu programově nebo pomocí [**Průzkumníka vyhledávání**](search-explorer.md) na portálu můžete prozkoumat vyplněný index vyhledávání.

### <a name="programatically"></a>Programově

Existují dvě hlavní možnosti kontroly počtu dokumentů v indexu: rozhraní [API pro počet dokumentů](https://docs.microsoft.com/rest/api/searchservice/count-documents) a [rozhraní API pro statistiku získání indexu](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics). Obě cesty můžou vyžadovat nějakou další dobu k aktualizaci, takže se neobjeví alarm, pokud je počet vrácených dokumentů menší, než jste očekávali původně.

#### <a name="count-documents"></a>Počet dokumentů

Operace počet dokumentů načte počet dokumentů ve vyhledávacím indexu:

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>Získat statistiku indexu

Operace získat statistiku indexu vrátí počet dokumentů pro aktuální index a využití úložiště. Statistiky indexu budou trvat déle než počet dokumentů, které se mají aktualizovat.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>portál Azure

V Azure Portal otevřete stránku **Přehled** služby Search a v seznamu **indexy** najděte index **optimalizace a indexování** .

  ![Seznam indexů Kognitivní hledání Azure](media/tutorial-optimize-data-indexing/portal-output.png "Seznam indexů Kognitivní hledání Azure")

*Počet dokumentů* a *velikost úložiště* jsou založené na [získání rozhraní API statistiky indexu](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) a aktualizace může trvat několik minut.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Vzorový kód pro tento kurz zkontroluje existující indexy a odstraní je, abyste mohli znovu spustit kód.

Pomocí portálu můžete také odstranit indexy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení s konceptem ingestování dat, Pojďme se podíváme na architekturu dotazů Lucene a na tom, jak funguje fulltextové vyhledávání v Azure Kognitivní hledání.

> [!div class="nextstepaction"]
> [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
