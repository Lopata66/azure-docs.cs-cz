---
title: 'Rychlý start: Rozhraní API Cassandra s využitím Node.js – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Apache Cassandra API služby Azure Cosmos DB k vytvoření aplikace profilu pomocí Node.js.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77210340"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Úvodní příručka: Vytvoření aplikace Cassandra s sadou Node.js SDK a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

V tomto rychlém startu vytvoříte účet rozhraní API Azure Cosmos DB Cassandra a pomocí aplikace Cassandra Node.js naklonované z GitHubu vytvoříte databázi a kontejner Cassandra. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternativně můžete [vyzkoušet službu Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

Kromě toho je potřeba toto:
* [Verze Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) v0.10.29 nebo vyšší
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujte aplikaci Cassandra API z GitHubu, nastavíme připojovací řetězec a spusťte ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek. Vytvořte novou složku s názvem `git-samples`. Pak zavřete příkazový řádek.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například git bash. Pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se pomocí kódu vytvoří prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu pocházejí ze souboru `uprofile.js` ve složce `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

* Hodnoty uživatelského jména a hesla se nastaví pomocí stránky připojovacího řetězce na webu Azure Portal. Cesta `path\to\cert` představuje cestu k certifikátu X509. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* Inicializuje se proměnná `client` s informacemi contactPoint. Hodnota contactPoint se načte z webu Azure Portal.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` se připojí k rozhraní Azure Cosmos DB Cassandra API.

    ```javascript
    client.connect(next);
    ```

* Vytvoří se nový prostor klíčů.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Vytvoří se nová tabulka.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Vloží se entity klíč-hodnota.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Spustí se dotaz pro získání všech hodnot klíčů.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Spustí se dotaz pro získání páru klíč-hodnota.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází.

1. Ve svém účtu Azure Cosmos DB na [webu Azure Portal](https://portal.azure.com/)vyberte Spojovací **řetězec**. 

    Pomocí tlačítka ![Tlačítko pro kopírování](./media/create-cassandra-nodejs/copy.png) na pravé straně obrazovky zkopírujte horní hodnotu KONTAKTNÍ BOD.

    ![Zobrazení a zkopírování hodnot KONTAKTNÍ BOD, UŽIVATELSKÉ JMÉNO a HESLO ze stránky připojovacího řetězce na webu Azure Portal](./media/create-cassandra-nodejs/keys.png)

2. Otevřete soubor `config.js`. 

3. Vložte hodnotu KONTAKTNÍ BOD z portálu místo `<FillMEIN>` na řádku 4.

    Řádek 4 by teď měl vypadat nějak takto: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Zkopírujte z portálu hodnotu UŽIVATELSKÉ JMÉNO a vložte ji místo `<FillMEIN>` na řádku 2.

    Řádek 2 by teď měl vypadat nějak takto: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Zkopírujte z portálu hodnotu HESLO a vložte ji místo `<FillMEIN>` na řádku 3.

    Řádek 3 by teď měl vypadat nějak takto:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Uložte soubor `config.js`.
    
## <a name="use-the-x509-certificate"></a>Použití certifikátu X509

1. Stáhněte si certifikát Baltimore CyberTrust Root místně od společnosti [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Přejmenujte příponu souboru na `.cer`.

   Certifikát má sériové číslo `02:00:00:b9` a otisk SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Otevřete `uprofile.js` a změňte cestu `path\to\cert` tak, aby odkazovala na váš nový certifikát.

3. Uložte `uprofile.js`.

> [!NOTE]
> Pokud se v pozdějších krocích vyskytne chyba související s certifikátem a jsou spuštěny v počítači se systémem Windows, ujistěte se, že jste postupovali podle postupu správného převodu souboru CRT do níže uvedeného formátu Microsoft .cer.
> 
> Poklepáním na soubor CRT jej otevřete do zobrazení certifikátu. 
>
> ![Zobrazení a ověření výstupu](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Stisknutím klávesy Další v Průvodci certifikátem. Vyberte Base-64 kódované X.509 (. CER), pak Další.
>
> ![Zobrazení a ověření výstupu](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Vyberte Procházet (vyhledejte cíl) a zadejte název souboru.
> Vyberte další a hotovo.
>
> Nyní byste měli mít správně formátovaný soubor CER. Ujistěte se, `uprofile.js` že cesta v odkazuje na tento soubor.

## <a name="run-the-nodejs-app"></a>Spuštění aplikace Node.js

1. V okně terminálu git se ujistěte, že jste v ukázkovém adresáři, který jste naklonovali dříve:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Spusťte `npm install` instalaci požadovaných modulů npm.

3. Spuštěním příkazu `node uprofile.js` spusťte aplikaci uzlu.

4. Na příkazovém řádku zkontrolujte očekávané výsledky.

    ![Zobrazení a ověření výstupu](./media/create-cassandra-nodejs/output.png)

    Stisknutím kláves CTRL+C ukončíte provádění programu a zavřete okno konzoly. 

5. Na portálu Azure Portal otevřete **Data Explorer**, abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi. 

    ![Zobrazení dat v Průzkumníku dat](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB s rozhraním CASSANDRA API a spustit aplikaci Cassandra Node.js, která vytvoří databázi a kontejner Cassandra. Teď můžete importovat další data do účtu Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)