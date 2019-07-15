---
title: Kurz k Node.js pro rozhraní SQL API pro službu Azure Cosmos DB
description: Kurz Node.js ukazující připojení ke službě Azure Cosmos DB a její dotazování pomocí rozhraní SQL API
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: c98d52e95a285c2979742c8572a86718238548f4
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985632"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Kurz: Sestavení aplikace konzoly Node.js pomocí sady JavaScript SDK ke správě dat SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají dat dokumentů typu NoSQL. Účet rozhraní SQL API ve službě Azure Cosmos DB můžete použít k ukládání a přístup k těmto datům dokumentu. V tomto kurzu se dozvíte, jak vytvořit konzolovou aplikaci Node.js a vytvořte prostředky služby Azure Cosmos DB a jejich dotazování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření a připojení k účtu služby Azure Cosmos DB.
> * Nastavení aplikace.
> * Vytvoření databáze.
> * Vytvořte kontejner.
> * Přidání položek do kontejneru.
> * Provádění základních operací, o položkách, kontejnerů a databáze.

## <a name="prerequisites"></a>Požadavky 

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 nebo vyšší.

## <a name="create-azure-cosmos-db-account"></a>Vytvoření účtu Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení aplikace Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Nastavení aplikace Node.js

Než začnete psát kód pro vytvoření aplikace, můžete vytvořit rozhraní pro vaši aplikaci. Provedením následujících kroků pro nastavení aplikace Node.js, který má kód framework:

1. Otevřete svůj oblíbený terminál.
2. Vyhledejte složku nebo adresář, do kterého chcete uložit aplikaci Node.js.
3. Vytvořte dva prázdné javascriptové soubory, které budou obsahovat následující příkazy:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux nebo OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Vytváření a inicializace `package.json` souboru. Použijte následující příkaz:
   * ```npm init -y```

5. Nainstalujte přes npm modul @azure/cosmos. Použijte následující příkaz:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Nastavení konfigurací aplikace

Teď, když vaše aplikace existuje, musíte zajistit, aby že komunikoval s Azure Cosmos DB. Stačí aktualizovat několik nastavení konfigurace, jak je znázorněno v následujícím postupu můžete nastavit aplikaci komunikovat s Azure Cosmos DB:

1. Ve svém oblíbeném textovém editoru otevřete ```config.js```.

1. Zkopírujte a vložte fragment kódu níže a nastavte vlastnosti ```config.endpoint``` a ```config.primaryKey``` na hodnoty identifikátoru URI a primárního klíče vašeho koncového bodu Azure Cosmos DB. Obě tyto konfigurace je možné najít na webu [Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky se získáním klíčů z webu Azure Portal][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Zkopírujte údaje ```database```, ```container``` a ```items``` a vložte je do objektu ```config``` pod nastavení vlastností ```config.endpoint``` a ```config.primaryKey```. Pokud již máte data, která chcete uložit do databáze, můžete použít nástroj pro migraci dat do služby Azure Cosmos DB místo definování dat tady. Je soubor config.js by měl mít následující kód:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   Sada JavaScript SDK používá obecné podmínky *kontejneru* a *položky*. Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. 
   
   `module.exports = config;` Kód slouží k exportu vašeho ```config``` tak, že jste na něj mohli odkazovat v rámci objektu ```app.js``` souboru.

## <a id="Connect"></a>Připojte se k účtu služby Azure Cosmos DB

1. V textovém editoru otevřete prázdný soubor ```app.js```. Zkopírováním a vložením kódu níže importujte modul ```@azure/cosmos``` a nově vytvořený modul ```config```.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Zkopírujte a vložte kód, který použije dříve uložené ```config.endpoint``` a ```config.primaryKey``` k vytvoření nové instance CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> Pokud propojíte **emulátor služby Cosmos DB**, vypnout ověřování SSL tak, že vytvoříte vlastní připojení zásad.
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Teď, když máte kód pro inicializaci klienta Azure Cosmos DB, se budeme věnovat práci s prostředky Azure Cosmos DB.

## <a name="create-a-database"></a>Vytvoření databáze

1. Zkopírujte a vložte kód níže, který nastaví ID databáze a ID kontejneru. Tyto identifikátory jsou, jak bude klient služby Azure Cosmos DB najít správnou databázi a kontejner.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Databázi lze vytvořit buď pomocí `createIfNotExists` nebo vytvořit funkci **databází** třídy. Databáze je logický kontejner položek rozdělených napříč kontejnery. 

2. Zkopírujte metody **createDatabase** a **readDatabase** a vložte je do souboru app.js pod definici ```databaseId``` a ```containerId```. **CreateDatabase** funkce vytvoří novou databázi s ID ```FamilyDatabase```ze zadané ```config``` objektu, pokud již neexistuje. Funkce **readDatabase** přečte definici databáze a ověří, jestli databáze existuje.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Zkopírujte a pod nastavení funkcí **createDatabase** a **readDatabase** vložte následující kód, který přidá pomocnou funkci **exit**, která vypíše závěrečnou zprávu. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Zkopírujte a pod nastavení funkce **exit** vložte následující kód, který volá funkce **createDatabase** a **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   V tuto chvíli by váš kód v souboru ```app.js``` měl vypadat jako následující kód:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Vytvoření kontejneru

Dále vytvořte kontejner v účtu Azure Cosmos DB, tak, že můžete ukládat a dotazovat data. 

> [!WARNING]
> Vytvoření kontejneru se hradí. Navštivte naše [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) abyste věděli, co mají očekávat.

Kontejner můžete vytvořit buď pomocí `createIfNotExists` nebo vytvoření funkce z **kontejnery** třídy.  Kontejner se skládá z položek (v případě rozhraní SQL API to jsou dokumenty JSON) a přidružené logiky javascriptové aplikace.

1. Zkopírujte funkce **createContainer** a **readContainer** a vložte je pod funkci **readDatabase** v souboru app.js. Funkce **createContainer** vytvoří nový kontejner s ID ```containerId``` definovaným v objektu ```config```, pokud ještě neexistuje. Funkce **readContainer** přečte definici kontejneru a ověří, jestli kontejner existuje.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Zkopírujte a pod volání funkce **readDatabase** vložte následující kód, který spustí funkce **createContainer** a **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   V tuto chvíli by váš kód v souboru ```app.js``` měl vypadat přibližně takto:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Vytvoření položky

Položky můžete vytvořit pomocí funkce vytvořit **položky** třídy. Pokud používáte rozhraní SQL API, položky se vykreslují jako dokumenty, které jsou uživatelem definovaný (libovolný) obsah JSON. Teď můžete do služby Azure Cosmos DB vložit položku.

1. Zkopírujte funkci **createFamilyItem** a vložte ji pod funkci **readContainer**. Funkce **createFamilyItem** vytvoří položky obsahující data JSON uložená v objektu ```config```. Zkontrolujeme, abyste měli jistotu, že položka se stejným ID před vytvořením ho už neexistuje.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. Zkopírujte a pod volání funkce **readContainer** vložte následující kód, který spustí funkci **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v jednotlivých kontejnerech. Následující vzorový kód ukazuje dotaz, který můžete spouštět proti dokumentům v kontejneru.

1. Zkopírujte funkci **queryContainer** a vložte ji pod funkci **createFamilyItem** v souboru app.js. Jak je vidět níže, Azure Cosmos DB podporuje dotazy podobné jazyku SQL.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Zkopírujte a pod volání funkce **createFamilyItem** vložte následující kód, který spustí funkci **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Nahraďte položku
Azure Cosmos DB podporuje nahrazování obsahu položek.

1. Zkopírujte funkci **replaceFamilyItem** a vložte ji pod funkci **queryContainer** v souboru app.js. Všimněte si, že jsme změnili vlastnost grade podřízeného prvku z předchozí hodnoty 5 na hodnotu 6.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. Zkopírujte a pod volání funkce **queryContainer** vložte následující kód, který spustí funkci **replaceFamilyItem**. Také znovu přidejte kód volání funkce **queryContainer**, aby bylo možné ověřit, že se položka úspěšně změnila.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Odstranění položky

Azure Cosmos DB podporuje odstraňování položek JSON.

1. Zkopírujte funkci **deleteFamilyItem** a vložte ji pod funkci **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Zkopírujte a pod druhé volání funkce **queryContainer** vložte následující kód, který spustí funkci **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Odstranění databáze

Odstraněním vytvořené databáze dojde k odstranění databáze a všech jejích podřízených prostředků (kontejnerů, položek atd.).

1. Zkopírujte a pod funkci **deleteFamilyItem** vložte funkci **cleanup**, která odebere databázi a všechny podřízené prostředky.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Zkopírujte a pod volání funkce **deleteFamilyItem** vložte následující kód, který spustí funkci **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Spustit aplikaci v Node.js

Jako celek by váš kód měl vypadat přibližně takto:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

```bash 
node app.js
```

Měl by se zobrazit výstup počáteční aplikace. Výstup by měl odpovídat ukázkovému textu níže.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Získání úplného řešení kurzu k Node.js 

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jenom chcete stáhnout kód, můžete ho získat z [GitHubu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Pokud chcete spustit načítání Začínáme řešení, které obsahuje veškerý kód v tomto článku, budete potřebovat: 

* [Účtu služby Azure Cosmos DB][create-account]. 
* Řešení [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), které je k dispozici na GitHubu. 

Instalace závislostí projektu přes npm. Použijte následující příkaz: 

* ```npm install``` 

Vedle ```config.js``` souboru, jak je popsáno v aktualizujte hodnoty config.endpoint a config.primaryKey [krok 3: Nastavení konfigurací aplikace](#Config).  

Potom v terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky už nepotřebujete, můžete odstranit skupinu prostředků, účet služby Azure Cosmos DB a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků, který jste použili pro účet služby Azure Cosmos DB, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování účtu služby Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
