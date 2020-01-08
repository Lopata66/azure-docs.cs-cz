---
title: Hledání dat Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importuje data z Azure Cosmos DB do indexu s možností vyhledávání v Azure Kognitivní hledání. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: ef136345c7c41c720efd3c79923b6ce646de41e2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75642161"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Jak indexovat data Cosmos DB pomocí indexeru v Azure Kognitivní hledání 

> [!IMPORTANT] 
> Rozhraní SQL API je všeobecně dostupné.
> MongoDB API, rozhraní Gremlin API a podpora rozhraní API Cassandra jsou momentálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview)můžete požádat o přístup k náhledům. [REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce ve verzi Preview. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

> [!WARNING]
> Azure Kognitivní hledání podporuje jenom kolekce Cosmos DB s nastavenou [zásadou indexování](https://docs.microsoft.com/azure/cosmos-db/index-policy) jako [konzistentní](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) . Indexování kolekcí se zásadami opožděného indexování se nedoporučuje a může mít za následek chybějící data. Kolekce s zakázaným indexováním nejsou podporovány.

V tomto článku se dozvíte, jak nakonfigurovat [indexer](search-indexer-overview.md) Azure Cosmos DB pro extrakci obsahu a zpřístupnění jeho prohledávání v Azure kognitivní hledání. Tento pracovní postup vytvoří index služby Azure Kognitivní hledání a načte ho s existujícím extrahovaným textem z Azure Cosmos DB. 

Vzhledem k tomu, že terminologie může být matoucí, je třeba poznamenat, že indexování [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) a služba [Azure kognitivní hledání indexování](search-what-is-an-index.md) jsou odlišné operace, které jsou pro každou službu jedinečné. Než začnete s indexováním Azure Kognitivní hledání, databáze Azure Cosmos DB už musí existovat a obsahovat data.

Indexer Cosmos DB ve službě Azure Kognitivní hledání může procházet [Azure Cosmos DB položky](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) , ke kterým přistupovaly prostřednictvím různých protokolů.

+ Pro [rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), které je všeobecně dostupné, můžete použít [portál](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)nebo [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet).

+ V případě [rozhraní MongoDB API (Preview)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) a [rozhraní Gremlin API (Preview)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)můžete vytvořit indexer pomocí [portálu](#cosmos-indexer-portal) nebo [REST API verze 2019-05-06-Preview](search-api-preview.md) na volání metody [Create indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) .

+ Pro [rozhraní API Cassandra (Preview)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)můžete použít jenom [REST API verze 2019-05-06-Preview](search-api-preview.md) na volání metody [Create indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) .


> [!Note]
> Pokud se chcete podívat, jak to podporuje Azure Kognitivní hledání, můžete přetypovat hlas na uživatelský hlas pro [rozhraní API pro tabulky](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) .
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

> [!Note]
> Portál aktuálně podporuje rozhraní API SQL a rozhraní MongoDB API (Preview).

Nejjednodušší způsob indexování Azure Cosmos DBch položek je použití Průvodce v [Azure Portal](https://portal.azure.com/). Pomocí vzorkování dat a čtení metadat v kontejneru může průvodce [**importem dat**](search-import-data-portal.md) v Azure kognitivní hledání vytvořit výchozí index, mapovat zdrojová pole na pole cílového indexu a načíst index v rámci jedné operace. V závislosti na velikosti a složitosti zdrojových dat můžete mít během několika minut provozní fulltextový index vyhledávání.

Pro Azure Kognitivní hledání i Azure Cosmos DB doporučujeme použít pro nižší latenci stejnou oblast nebo umístění a vyhnout se tak poplatkům za šířku pásma.

### <a name="1---prepare-source-data"></a>1\. Příprava zdrojových dat

Měli byste mít účet Cosmos DB, databázi Azure Cosmos DB namapovanou na rozhraní SQL API, rozhraní MongoDB API (Preview) nebo rozhraní Gremlin API (Preview) a obsah v databázi.

Ujistěte se, že vaše databáze Cosmos DB obsahuje data. [Průvodce importem dat](search-import-data-portal.md) načte metadata a provede vzorkování dat pro odvození schématu indexu, ale také načte data z Cosmos DB. Pokud data chybí, průvodce se zastaví s touto chybou: Chyba při zjišťování schématu indexu ze zdroje dat: nelze sestavit index prototypu, protože DataSource ' emptycollection ' nevrátil žádná data.

### <a name="2---start-import-data-wizard"></a>2\. spuštění Průvodce importem dat

Průvodce můžete [Spustit](search-import-data-portal.md) z příkazového řádku na stránce služby Azure kognitivní hledání, nebo pokud se připojujete k Cosmos DB rozhraní SQL API, můžete kliknout na **Přidat Azure kognitivní hledání** v části **Nastavení** v levém navigačním podokně účtu Cosmos DB.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

Na stránce **zdroj dat** musí být zdroj **Cosmos DB**, a to s následujícími specifikacemi:

+ **Name** je název objektu zdroje dat. Po vytvoření si ho můžete vybrat pro jiné úlohy.

+ **Cosmos DB účet** by měl být primárním nebo sekundárním připojovacím řetězcem z Cosmos DB s `AccountEndpoint` a `AccountKey`. Pro kolekce MongoDB přidejte **ApiKind = MongoDB** do konce připojovacího řetězce a oddělte ho od připojovacího řetězce středníkem. Pro rozhraní Gremlin API a rozhraní API Cassandra použijte pokyny pro [REST API](#cosmosdb-indexer-rest).

+ **Databáze** je existující databáze z účtu. 

+ **Kolekce** je kontejner dokumentů. Aby import proběhl úspěšně, musí dokumenty existovat. 

+ **Dotaz** může být prázdný, pokud chcete všechny dokumenty, jinak můžete zadat dotaz, který vybere podmnožinu dokumentu. **Dotaz** je k dispozici pouze pro rozhraní SQL API.

   ![Cosmos DB definice zdroje dat](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definice zdroje dat")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 – přeskočí stránku "obohacení obsahu" v Průvodci

Přidání dovedností rozpoznávání (nebo obohacení) není požadavkem na import. Pokud nemáte konkrétní nutnost [Přidat rozšíření AI](cognitive-search-concept-intro.md) do kanálu indexování, měli byste tento krok přeskočit.

Chcete-li tento krok přeskočit, klikněte na modré tlačítka v dolní části stránky pro možnost "Další" a "Přeskočit".

### <a name="5---set-index-attributes"></a>5\. nastavení atributů indexu

Na stránce **index** byste měli vidět seznam polí s datovým typem a řadu zaškrtávacích políček pro nastavení atributů indexu. Průvodce může vygenerovat seznam polí založený na metadatech a vzorkováním zdrojových dat. 

Atributy můžete hromadně vybírat kliknutím na zaškrtávací políčko v horní části sloupce atributu. Vyberte možnost získatelné a **prohledávatelné** pro každé pole, které by se mělo vrátit do klientské **aplikace a podléhá** fulltextovým zpracování fulltextového vyhledávání. Všimnete si, že celá čísla nejsou fulltextová nebo přibližná prohledávání (čísla jsou vyhodnocována v doslovném znění a jsou často užitečná ve filtrech).

Další informace najdete v popisu [atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [analyzátorů jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Věnujte prosím chvíli kontrole vašich výběrů. Po spuštění Průvodce se vytvoří fyzické datové struktury a nebudete moct tato pole upravovat, aniž byste museli odstraňovat a znovu vytvářet všechny objekty.

   ![Definice indexu Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definice indexu Cosmos DB")

### <a name="6---create-indexer"></a>6\. vytvoření indexeru

V rámci vaší vyhledávací služby vytvoří průvodce tři odlišné objekty. Objekt zdroje dat a objekt indexu se ukládají jako pojmenované prostředky ve službě Azure Kognitivní hledání. Poslední krok vytvoří objekt indexeru. Pojmenování indexeru umožňuje, aby existoval jako samostatný prostředek, který můžete naplánovat a spravovat nezávisle na objektu index a zdroj dat, který jste vytvořili ve stejné sekvenci průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředkem v Azure kognitivní hledání, který prochází externím zdrojem dat pro prohledávatelný obsah. Výstupem průvodce **importem dat** je indexer, který prochází Cosmos DB zdroj dat, extrahuje prohledávatelný obsah a importuje ho do indexu v Azure kognitivní hledání.

Následující snímek obrazovky ukazuje výchozí konfiguraci indexeru. Pokud chcete indexer spustit **jednorázově** , můžete přepnout na jeden. Kliknutím na **Odeslat** spusťte průvodce a vytvořte všechny objekty. Indexování se okamžitě zahájí.

   ![Cosmos DB definice indexeru](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB definice indexeru")

Data importování můžete monitorovat na stránkách portálu. Oznámení o průběhu označují stav indexování a počet odeslaných dokumentů. 

Po dokončení indexování můžete pomocí [Průzkumníka služby Search Vyhledat](search-explorer.md) dotaz na svůj index.

> [!NOTE]
> Pokud nevidíte očekávaná data, možná budete muset nastavit další atributy pro více polí. Odstraňte index a indexer, který jste právě vytvořili, a projděte průvodce znovu a změňte si výběr pro atributy indexu v kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Pomocí REST API můžete indexovat Azure Cosmos DB data, a to za pracovní postup tří částí, který je společný pro všechny Indexery v Azure Kognitivní hledání: vytvořte zdroj dat, vytvořte index a vytvořte indexer. K extrakci dat z Cosmos DB dochází, když odešlete požadavek Create indexer. Po dokončení této žádosti budete mít Queryable index. 

> [!NOTE]
> Pro indexování dat z Cosmos DB rozhraní API Gremlin nebo Cosmos DB rozhraní API Cassandra musíte nejdřív požádat o přístup k ověřovaným náhledům vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po zpracování žádosti obdržíte pokyny, jak pomocí [REST API verze 2019-05-06-Preview](search-api-preview.md) vytvořit zdroj dat.

Dříve v tomto článku se zmiňujeme, že indexování [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) a [Azure kognitivní hledání](search-what-is-an-index.md) indexování je jedinečných operací. Pro Cosmos DB indexování se ve výchozím nastavení automaticky indexují všechny dokumenty s výjimkou rozhraní API Cassandra. Pokud automatické indexování vypnete, můžete k dokumentům přicházet pouze prostřednictvím vlastních odkazů nebo dotazů pomocí ID dokumentu. Indexování služby Azure Kognitivní hledání vyžaduje, aby v kolekci, která bude indexována službou Azure Kognitivní hledání, byla zapnuta Cosmos DB automatické indexování. Při registraci ve verzi Preview služby Cosmos DB rozhraní API Cassandra indexeru budete mít k dispozici pokyny, jak nastavit Cosmos DB indexování.

> [!WARNING]
> Azure Cosmos DB je další generace DocumentDB. Dřív s rozhraním API verze **2017-11-11** můžete použít syntaxi `documentdb`. To znamená, že můžete zadat typ zdroje dat jako `cosmosdb` nebo `documentdb`. Počínaje rozhraním API verze **2019-05-06** rozhraní API a portál Azure kognitivní hledání podporují jenom syntaxi `cosmosdb` podle pokynů v tomto článku. To znamená, že typ zdroje dat musí `cosmosdb`, pokud se chcete připojit ke koncovému bodu Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1\. sestavování vstupů pro požadavek

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Kognitivní hledání (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů BLOB. K posílání požadavků HTTP do Azure Kognitivní hledání můžete použít [post](search-get-started-postman.md) .

Do poznámkového bloku zkopírujte následující čtyři hodnoty, abyste je mohli vložit do žádosti:

+ Název služby Azure Kognitivní hledání
+ Klíč správce Azure Kognitivní hledání
+ Připojovací řetězec Cosmos DB

Tyto hodnoty můžete najít na portálu:

1. Na stránkách portálu pro Azure Kognitivní hledání zkopírujte adresu URL vyhledávací služby na stránce Přehled.

2. V levém navigačním podokně klikněte na **klíče** a zkopírujte buď primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepněte na stránky portálu účtu úložiště Cosmos. V levém navigačním podokně v části **Nastavení**klikněte na **klíče**. Tato stránka poskytuje identifikátor URI, dvě sady připojovacích řetězců a dvě sady klíčů. Zkopírujte jeden z připojovacích řetězců do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2\. vytvoření zdroje dat

**Zdroj dat** určuje data, která mají být indexována, pověření a zásady pro identifikaci změn v datech (například upravené nebo odstraněné dokumenty v kolekci). Zdroj dat je definován jako nezávislý prostředek, aby jej bylo možné použít více indexery.

Chcete-li vytvořit zdroj dat, formulujte požadavek POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Tělo požadavku obsahuje definici zdroje dat, která by měla obsahovat následující pole:

| Pole   | Popis |
|---------|-------------|
| **name** | Povinná hodnota. Vyberte libovolný název, který bude představovat váš objekt zdroje dat. |
|**type**| Povinná hodnota. Musí být `cosmosdb`. |
|**přihlašovací údaje** | Povinná hodnota. Musí se jednat o Cosmos DB připojovací řetězec.<br/>V případě kolekcí SQL jsou připojovací řetězce v tomto formátu: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Pro kolekce MongoDB přidejte **ApiKind = MongoDB** do připojovacího řetězce:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>V případě grafů Gremlin a tabulek Cassandra si zaregistrujte si ve [verzi Preview služby gated indexer](https://aka.ms/azure-cognitive-search/indexer-preview) , abyste získali přístup k verzi Preview a informace o tom, jak tato pověření naformátovat.<br/><br/>Vyhněte se číslům portů v adrese URL koncového bodu. Pokud zadáte číslo portu, Azure Kognitivní hledání nebude moct indexovat databázi Azure Cosmos DB.|
| **container** | Obsahuje následující prvky: <br/>**název**: povinné. Zadejte ID kolekce databází, která se má indexovat.<br/>**dotaz**: volitelné. Můžete zadat dotaz pro sloučení libovolného dokumentu JSON do plochého schématu, které může Azure Kognitivní hledání indexovat.<br/>Pro rozhraní MongoDB API, rozhraní Gremlin API a rozhraní API Cassandra se dotazy nepodporují. |
| **dataChangeDetectionPolicy** | Doporučil. Viz část [indexování změněných dokumentů](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Nepovinný parametr. Viz část [indexování odstraněných dokumentů](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Použití dotazů na indexovaná data obrazců
Můžete zadat dotaz SQL pro sloučení vnořených vlastností nebo polí, vlastností JSON projektu a filtrovat data, která mají být indexována. 

> [!WARNING]
> Vlastní dotazy nejsou podporovány pro **rozhraní API MongoDB**, **rozhraní Gremlin API**a **rozhraní API Cassandra**: parametr `container.query` musí být nastaven na hodnotu null nebo vynechán. Pokud potřebujete použít vlastní dotaz, dejte nám prosím na [uživatelském hlasu](https://feedback.azure.com/forums/263029-azure-search)informace.

Ukázkový dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Dotaz filtru:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Dotaz na sloučení:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Dotaz projekce:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Dotaz na sloučení pole:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3\. Vytvoření cílového vyhledávacího indexu 

Pokud ho ještě nemáte, [vytvořte cílový index Azure kognitivní hledání](/rest/api/searchservice/create-index) . Následující příklad vytvoří index s polem ID a popis:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Ujistěte se, že schéma cílového indexu je kompatibilní se schématem zdrojových dokumentů JSON nebo výstupem vlastního projekce dotazu.

> [!NOTE]
> U dělených kolekcí je výchozí klíč dokumentu Azure Cosmos DB `_rid` vlastnost, která se v Azure Kognitivní hledání automaticky přejmenuje na `rid`, protože názvy polí nemůžou začínat znakem podtržítka. Azure Cosmos DB `_rid` hodnoty taky obsahují znaky, které jsou v klíčích Azure Kognitivní hledání neplatné. Z tohoto důvodu jsou `_rid` hodnoty kódování Base64.
> 
> Pro kolekce MongoDB Azure Kognitivní hledání automaticky přejmenuje vlastnost `_id` na `id`.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapování mezi datovými typy JSON a datovými typy Azure Kognitivní hledání
| Datový typ JSON | Kompatibilní typy polí cílového indexu |
| --- | --- |
| Bool |EDM. Boolean, Edm. String |
| Čísla, která vypadají jako celá čísla |Edm.Int32, Edm.Int64, Edm.String |
| Čísla, která vypadají jako plovoucí body |Edm.Double, Edm.String |
| Řetězec |Edm.String |
| Pole primitivních typů, například ["a", "b", "c"] |Collection(Edm.String) |
| Řetězce, které vypadají jako kalendářní data |Edm.DateTimeOffset, Edm.String |
| Objekty injson, například {"Type": "Point", "souřadnice": [Long, lat]} |Edm.GeographyPoint |
| Jiné objekty JSON |Nevztahuje se |

### <a name="4---configure-and-run-the-indexer"></a>4\. konfigurace a spuštění indexeru

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Tento indexer se spouští každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Použití .NET

Obecně dostupná sada .NET SDK má úplnou paritu s všeobecně dostupnou REST API. Doporučujeme, abyste si přečtěte předchozí část REST API, kde se dozvíte o konceptech, pracovních postupech a požadavcích. Pak se můžete podívat na následující referenční dokumentaci rozhraní .NET API a implementovat indexer JSON ve spravovaném kódu.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexování změněných dokumentů

Účelem zásad detekce změn dat je efektivně identifikovat změněné datové položky. V současné době je jedinou podporovanou zásadou [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) pomocí vlastnosti `_ts` (Timestamp), kterou poskytuje Azure Cosmos DB, která je určena následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Použití této zásady se důrazně doporučuje, aby se zajistil dobrý výkon indexeru. 

Pokud používáte vlastní dotaz, ujistěte se, že je vlastnost `_ts` projekt provedená dotazem.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Přírůstkový průběh a vlastní dotazy

Přírůstkový průběh indexování zajišťuje, že pokud je provádění indexeru přerušeno přechodnými chybami nebo časovým limitem spuštění, může indexer vyzvednutí místa, kde se při příštím spuštění ponechá, místo aby bylo nutné znovu indexovat celou kolekci od začátku. To je obzvláště důležité při indexování velkých kolekcí. 

Pokud chcete povolit přírůstkový průběh při použití vlastního dotazu, ujistěte se, že dotaz seřadí výsledky podle sloupce `_ts`. To umožňuje pravidelné vracení se změnami, které Kognitivní hledání Azure používá k poskytování přírůstkového postupu v případě výskytu selhání.   

V některých případech i v případě, že dotaz obsahuje klauzuli `ORDER BY [collection alias]._ts`, nemusí Azure Kognitivní hledání odvodit, že dotaz je seřazený podle `_ts`. Můžete říct, Kognitivní hledání Azure, že výsledky jsou seřazené pomocí vlastnosti `assumeOrderByHighWaterMarkColumn` konfigurace. Chcete-li zadat tuto nápovědu, vytvořte nebo aktualizujte indexer následujícím způsobem: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexování odstraněných dokumentů

Pokud jsou řádky z kolekce odstraněny, obvykle je chcete odstranit také z indexu vyhledávání. Účelem zásad detekce odstranění dat je efektivně identifikovat odstraněné datové položky. V současné době je jedinou podporovanou zásadou zásada `Soft Delete` (odstranění je označeno příznakem nějakého řazení), které je zadáno následujícím způsobem:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Pokud používáte vlastní dotaz, ujistěte se, že vlastnost, na kterou odkazuje `softDeleteColumnName`, je projektovaná dotazem.

Následující příklad vytvoří zdroj dat se zásadami podmíněného odstranění:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Další kroky

Blahopřejeme! Zjistili jste, jak integrovat Azure Cosmos DB s Azure Kognitivní hledání pomocí indexeru.

* Další informace o Azure Cosmos DB najdete na [stránce služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Další informace o službě Azure Kognitivní hledání najdete na [stránce vyhledávací služby](https://azure.microsoft.com/services/search/).
