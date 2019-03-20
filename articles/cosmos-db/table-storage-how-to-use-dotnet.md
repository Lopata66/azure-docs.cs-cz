---
title: Začínáme se službou Azure Table Storage a rozhraním API pro tabulky služby Azure Cosmos DB s využitím .NET
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 08/17/2018
ms.openlocfilehash: 8d9037a0f557a39ba4384981d9f31daa5f1ca6e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842647"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>Začínáme se službou Azure Table Storage a rozhraním Table API služby Azure Cosmos DB pomocí .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Můžete použít Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB k ukládání strukturovaných dat NoSQL do cloudu a poskytování úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že jsou služba Table Storage a rozhraní Table API služby Azure Cosmos DB bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage a rozhraní Table API služby Azure Cosmos DB je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage nebo rozhraní Table API služby Azure Cosmos DB můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště nebo účet rozhraní Table API může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště nebo účtu rozhraní Table API.

### <a name="about-this-sample"></a>O této ukázce
Tento příklad ukazuje, jak používat knihovnu [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) v obvyklých scénářích Azure Table Storage a Table API. Název balíčku značí, že je určený k použití se službou Azure Cosmos DB, ale stejně tak funguje i s rozhraním Table API služby Azure Cosmos DB a se službou Azure Table Storage, obě služby mají pouze jiný koncový bod. Tyto scénáře se zkoumají na příkladech v C# a znázorňují následující postupy:
* Vytváření a odstraňování tabulek
* Vložení, aktualizace a odstranění řádků
* Dotazy na tabulky

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage Common Library for .NET (Preview)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). – Požadovaný balíček ve verzi Preview, který se podporuje v produkčním prostředí. 
* [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) –Tato knihovna je aktuálně dostupná pouze pro .NET Standard, pro .NET Core ještě dostupná není.
* [Účet služby Azure Storage](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Další ukázky
Další příklady použití Table Storage najdete v článku [Začínáme s Azure Table Storage v rozhraní .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Můžete si stáhnout a spustit ukázkovou aplikaci nebo si prohlédnout kód na GitHubu.

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
* Nejjednodušší způsob, jak vytvořit první účet úložiště Azure, je pomocí [webu Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

* Účet úložiště Azure můžete vytvořit také pomocí prostředí [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](../storage/common/storage-azure-cli.md) nebo pomocí [klientské knihovny pro poskytovatele prostředků úložiště pro rozhraní .NET](/dotnet/api/microsoft.azure.management.storage).

* Pokud teď nechcete vytvářet účet úložiště, můžete také pomocí emulátoru úložiště Azure spustit a otestovat kód v místním prostředí. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

### <a name="create-a-windows-console-application-project"></a>Vytvoření projektu konzolové aplikace pro Windows
V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v sadě Visual Studio 2017. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Vyberte **Soubor** > **Nový** > **Projekt**.
2. Vyberte **Instalováno** > **Visual C#** > **Klasická plocha Windows**.
3. Vyberte **Aplikace konzoly (.NET Framework)**.
4. Do pole **Název** zadejte název vaší aplikace.
5. Vyberte **OK**.

Všechny příklady kódu v této ukázce můžete přidat do metody `Main()` v souboru `Program.cs` vaší konzolové aplikace.

Můžete použít knihovnu Azure CosmosDB Table Library z libovolného typu aplikace .NET, včetně webové aplikace nebo cloudové služby Azure, desktopové nebo mobilní aplikace. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

### <a name="install-the-required-nuget-packages"></a>Instalace požadovaných balíčků NuGet
Abyste mohli tuto ukázku dokončit, potřebujete ze svého projektu odkazovat na tři doporučené balíčky:

* [Azure Storage Common Library for .NET (Preview)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). – Použijte verzi, která je nižší nebo rovna 9.0.0.1 (<= 9.0.0.1).

* [Microsoft Azure Cosmos DB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Tento balíček zajišťuje programový přístup k datovým prostředkům v rámci účtu úložiště Azure Table nebo účtu rozhraní Table API služby Azure Cosmos DB. Tato knihovna je aktuálně dostupná pouze pro .NET Standard, pro .NET Core ještě dostupná není.

Balíčky NuGet získáte pomocí následujících kroků:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.
2. Vyhledejte online text „Microsoft.Azure.Storage.Common“, zvolte verzi <= 9.0.0.1 a výběrem **Instalovat** nainstalujte knihovnu Azure Storage Common Library for .NET (Preview) a její závislosti. Zkontrolujte, že je zaškrtnuté políčko **Zahrnout předběžné verze**, protože se jedná o balíček verze Preview.
3. Vyhledejte online text „Microsoft.Azure.CosmosDB.Table“ a výběrem **Instalovat** nainstalujte knihovnu Microsoft Azure CosmosDB Table Library.

> [!NOTE]
> Závislosti ODataLib v knihovně Storage Common Library for .NET jsou vyřešené prostřednictvím balíčků ODataLib, které jsou dostupné na NuGetu, a ne prostřednictvím služby WCF Data Services. Knihovny ODataLib můžete stáhnout přímo nebo z odkazu ve vašem kódovém projektu prostřednictvím balíčku NuGet. Konkrétní balíčky ODataLib používané knihovnou klienta služby Storage jsou [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/), a [Spatial](https://nuget.org/packages/System.Spatial/). I když tyto knihovny používají třídy Azure Table Storage, představují požadované závislosti pro programování s knihovnou Storage Common Library.
> 
> 

> [!TIP]
> Vývojáři, kteří jsou už obeznámení s Azure Table Storage, mohli v minulosti použít balíček [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Doporučuje se, aby všechny nové tabulkové aplikace používaly knihovny [Azure Storage Common Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common) a [Azure Cosmos DB Table Library](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table), ale balíček WindowsAzure.Storage se stále podporuje. Pokud chcete použít knihovnu WindowsAzure.Storage, zahrňte do příkazů using položku Microsoft.WindowsAzure.Storage.Table.
>
>

### <a name="determine-your-target-environment"></a>Určení cílového prostředí
Ke spuštění příkladů z tohoto průvodce máte tři možnosti prostředí:

* Svůj kód můžete spustit na účtu služby Azure Storage v cloudu. 
* Svůj kód můžete spustit na účtu služby Azure Cosmos DB v cloudu.
* Svůj kód můžete spustit v emulátoru úložiště Azure. Emulátor úložiště je místní prostředí, které emuluje účet služby Azure Storage v cloudu. Emulátor je bezplatnou možností pro testování a ladění kódu během vývoje aplikace. Emulátor používá známý účet a klíč. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../storage/common/storage-use-emulator.md).

Pokud se zaměřujete na účet úložiště v cloudu, zkopírujte z webu Azure Portal primární přístupový klíč svého účtu úložiště. Další informace najdete v článku [Přístupové klíče účtu úložiště](../storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Pokud se chcete vyhnout nákladům spojeným se službou Azure Storage, můžete se zaměřit na emulátor úložiště. I když se zaměříte na účet úložiště Azure v cloudu, budou náklady na vyzkoušení postupů z této ukázky zanedbatelné.
> 
> 

Pokud se zaměřujete na účet služby Azure Cosmos DB, zkopírujte z webu Azure Portal primární přístupový klíč svého účtu rozhraní Table API. Další informace najdete v článku [Aktualizace připojovacího řetězce](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
Knihovna Azure Storage Common Library for .NET podporuje použití připojovacího řetězce úložiště ke konfiguraci koncových bodů a přihlašovacích údajů pro přístup ke službám úložiště. Připojovací řetězec úložiště se nejlépe uchovává v konfiguračním souboru. 

Další informace o připojovacích řetězcích najdete v tématu věnovaném [konfiguraci připojovacího řetězce pro službu Azure Storage](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Klíč účtu je podobný kořenovému heslu vašeho účtu úložiště. Vždy klíč účtu úložiště pečlivě chraňte. Nedávejte ho jiným uživatelům, nezakódovávejte ho ani ho neukládejte do souboru ve formátu prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že klíč je ohrožený, vygenerujte ho znovu pomocí webu Azure Portal.
> 

Pokud chcete konfigurovat připojovací řetězec, otevřete v sadě Visual Studio Průzkumníka řešení a v něm soubor `app.config`. Přidejte obsah níže uvedeného prvku `<appSettings>`. Hodnotu `account-name` nahraďte názvem svého účtu a hodnotu `account-key` nahraďte přístupovým klíčem účtu:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Pokud například používáte účet služby Azure Storage, nastavení konfigurace bude vypadat přibližně takto:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

Pokud používáte účet služby Azure Cosmos DB, nastavení konfigurace bude vypadat přibližně takto:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Pokud se chcete zaměřit na emulátor úložiště, můžete vytvořit zástupce, který se namapuje na název a klíč známého účtu. V takovém případě bude nastavení připojovacího řetězce vypadat následovně:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Přidání direktiv using
Na začátek souboru `Program.cs` přidejte následující direktivy **using**:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Vytvoření klienta služby Table service
Třída [CloudTableClient][dotnet_CloudTableClient] vám umožňuje načítat tabulky a entity, které jsou uložené ve službě Table Storage. Jeden ze způsobů, jak vytvořit klienta služby Table service:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Teď můžete napsat kód, který bude číst data z Table Storage a bude je tam také zapisovat.

## <a name="create-a-table"></a>Vytvoření tabulky
Tento příklad ukazuje, jak vytvořit tabulku, pokud ještě neexistuje:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Entity se mapují na objekty jazyka C# pomocí vlastní třídy odvozené z [TableEntity][dotnet_TableEntity]. Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. V tabulce ji pak jednoznačně identifikuje kombinace klíče oddílu a řádku entity. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Entity, které se mají ukládat do tabulek, musí být podporovaného typu, například odvozené ze třídy [TableEntity][dotnet_TableEntity]. Vlastnosti entity, které chcete uložit do tabulky, musí být veřejné vlastnosti typu a musí podporovat získávání i nastavování hodnot. Typ entity navíc *musí* zveřejňovat konstruktor bez parametrů.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Operace s tabulkou zahrnující entity se provádí prostřednictvím objektu [CloudTable][dotnet_CloudTable], který jste vytvořili dříve v části Vytvoření tabulky. Operace, která se má provést, je reprezentovaná objektem [TableOperation][dotnet_TableOperation]. Následující příklad kódu ukazuje vytvoření objektu [CloudTable][dotnet_CloudTable] a následně objektu **CustomerEntity**. V rámci přípravy na operaci je vytvořen objekt [TableOperation][dotnet_TableOperation] pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním metody [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
V rámci jedné operace zápisu můžete do tabulky vložit dávku entit. Několik dalších poznámek ohledně dávkových operací:

* V rámci jedné dávkové operace můžete provádět aktualizace, odstranění a vložení.
* Jedna dávková operace může obsahovat až 100 entit.
* Všechny entity v jedné dávkové operaci musí mít stejný klíč oddílu.
* Dotaz je sice také možné provést jako dávkovou operaci, musí to být ale jediná operace v dávce.

Následující příklad kódu vytvoří dva objekty entity a každý z nich přidá do [TableBatchOperation][dotnet_TableBatchOperation] pomocí metody [Insert][dotnet_TableBatchOperation_Insert]. Pak se volá metoda [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] pro provedení operace.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu
Pokud chcete zadat dotaz na tabulku pro všechny entity v oddílu, použijte objekt [TableQuery][dotnet_TableQuery]. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Načtení rozsahu entit v oddílu
Pokud nechcete, aby se zadával dotaz na všechny entity v oddílu, můžete určit rozsah nakombinováním filtru klíče oddílu s filtrem klíče řádku. Následující příklad kódu používá dva filtry k získání všech entit v oddílu Smith, kde klíč řádku (jméno) začíná písmenem abecedy před písmenem E, a potom zobrazí výsledky dotazu.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Načtení jedné entity
Můžete napsat dotaz pro načtení jedné konkrétní entity. Následující kód používá objekt [TableOperation][dotnet_TableOperation] k určení zákazníka Ben Smith. Tato metoda vrátí místo kolekce pouze jednu entitu a vrácenou hodnotou při volání metody [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] je objekt **CustomerEntity**. Určení jak klíčů oddílu, tak klíčů řádků v dotazu představuje nejrychlejší způsob, jak načíst jednu entitu ze služby Table service.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Nahrazení entity
Pokud chcete entitu aktualizovat, načtěte ji ze služby Table service, upravte objekt entity a potom uložte změny zpět do služby Table service. Následující kód změní telefonní číslo stávajícího zákazníka. Namísto volání metody [Insert][dotnet_TableOperation_Insert] tento kód používá metodu [Replace][dotnet_TableOperation_Replace]. Metoda [Replace][dotnet_TableOperation_Replace] způsobí, že entita se na serveru plně nahradí, pokud se entita na serveru od načtení nezměnila, protože v takovém případě se operace nezdaří. Toto selhání zabrání vaší aplikaci v nechtěném přepsání změny provedené mezi načtením a aktualizací provedenou jinou součástí vaší aplikace. Toto selhání by se mělo správně zpracovat tak, že entitu znovu načtete, provedete požadované změny (pokud je stále ještě chcete provést) a pak provedete další operaci nahrazení ([Replace][dotnet_TableOperation_Replace]). V další části si ukážeme, jak toto chování potlačit.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Vložení nebo nahrazení entity
Operace [Replace][dotnet_TableOperation_Replace] se nezdaří, pokud byla entita od načtení ze serveru změněna. Kromě toho musíte entitu nejdřív načíst ze serveru, aby operace [Replace][dotnet_TableOperation_Replace] proběhla úspěšně. V některých případech ale nevíte, jestli entita existuje na serveru a jestli jsou hodnoty, které jsou v ní aktuálně uložené, relevantní. Vaše aktualizace by je měla všechny přepsat. K tomu použijete operaci [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Tato operace vloží entitu, pokud neexistuje, nebo ji nahradí, pokud existuje, a to bez ohledu na to, kdy byla provedena poslední aktualizace.

V následujícím příkladu kódu se vytvoří entita zákazníka Fred Jones a vloží se do tabulky people. V dalším kroku použijeme operaci [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] k uložení entity se stejným klíčem oddílu (Jones) a klíčem řádku (Fred) na server, tentokrát s jinou hodnotou pro vlastnost PhoneNumber. Vzhledem k tomu, že používáme operaci [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], budou nahrazeny všechny příslušné hodnoty vlastnosti. Pokud by ovšem entita Fred Jones v tabulce neexistovala, vložila by se.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity
Dotaz na tabulku může místo všech vlastností entity načíst jenom několik z nich. Tato technika, které se říká projekce, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit. Dotaz v následujícím kódu vrátí pouze e-mailové adresy entit v tabulce. To se provádí pomocí dotazu [DynamicTableEntity][dotnet_DynamicTableEntity] a také [EntityResolver][dotnet_EntityResolver]. Emulátor úložiště projekci nepodporuje, takže tento kód bude možné spustit pouze v případě, že používáte účet ve službě Table service.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Odstranění entity
Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Odstranění tabulky
Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. Tabulku, která byla odstraněna, nebude možné po odstranění nějakou dobu znovu vytvořit.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Asynchronní načítání entit na stránkách
Pokud načítáte velký počet entit a chcete entity zpracovávat/zobrazovat tak, jak jsou načítány, a nečekat, až se všechny vrátí, můžete entity načíst pomocí segmentovaného dotazu. Tento příklad ukazuje, jak vracet výsledky na stránkách pomocí vzoru Async-Await, aby čekání na vrácení velké sady výsledků neblokovalo provádění. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Asynchronní programování s Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání služby Table Storage, podívejte se na následujících odkazech na další informace o složitějších úlohách úložiště:

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* Další příklady Table Storage najdete v článku [Začínáme s Azure Table Storage v rozhraní .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/).
* Projděte si referenční dokumentaci ke službě Table service, kde najdete úplné podrobnosti o dostupných rozhraních API:
* [Klientská knihovna Storage pro .NET – referenční informace](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API – referenční informace](https://msdn.microsoft.com/library/azure/dd179355)
* Zjistěte, jak můžete zjednodušit kód, který vytváříte, aby fungoval s Azure Storage, pomocí sady [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.
* [Začínáme s Azure Blob Storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) pro ukládání nestrukturovaných dat
* [Připojení k SQL Database s použitím rozhraní .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) pro uložení relačních dat

[Download and install the Azure SDK for .NET]: /develop/net/

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx