---
title: 'Rychlý start: Rozhraní API Cassandra s využitím Pythonu – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní API pro Apache Cassandra služby Azure Cosmos DB k vytvoření aplikace profilu pomocí Pythonu.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134676"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Rychlý Start: Vytvoření aplikace Cassandra pomocí sady Python SDK a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

V tomto rychlém startu vytvoříte účet Azure Cosmos DB rozhraní API Cassandra a pomocí aplikace Python Cassandra, která je klonována z GitHubu, vytvoříte databázi Cassandra a kontejner. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
- [Python 2.7.14 + nebo 3.4 +](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads)
- [Ovladač Python pro Apache Cassandra](https://github.com/datastax/python-driver)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Nyní naklonujte rozhraní API Cassandra aplikaci z GitHubu, nastavíme připojovací řetězec a spustíme ji. Uvidíte, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek. Vytvořte novou složku s názvem `git-samples`. Pak zavřete příkazový řádek.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se pomocí kódu vytvoří prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu jsou pořízeny ze souboru *pyquickstart.py* . Jinak můžete přeskočit přímo k části [Aktualizace připojovacího řetězce](#update-your-connection-string). 

* Hodnoty uživatelského jména a hesla se nastaví pomocí stránky připojovacího řetězce na webu Azure Portal. Cesta `path\to\cert` představuje cestu k certifikátu X509. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* Inicializuje se proměnná `cluster` s informacemi contactPoint. Hodnota contactPoint se načte z webu Azure Portal.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* `cluster` se připojí k rozhraní Azure Cosmos DB Cassandra API.

    ```python
    session = cluster.connect()
    ```

* Vytvoří se nový prostor klíčů.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Vytvoří se nová tabulka.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Vloží se entity klíč-hodnota.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Spustí se dotaz pro získání všech hodnot klíčů.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Spustí se dotaz pro získání páru klíč-hodnota.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na web Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec**. 

    Pomocí tlačítka ![Tlačítko pro kopírování](./media/create-cassandra-python/copy.png) na pravé straně obrazovky zkopírujte horní hodnotu KONTAKTNÍ BOD.

    ![Zobrazení a zkopírování přístupového uživatelského jména, hesla a kontaktního bodu na webu Azure Portal v okně připojovacího řetězce](./media/create-cassandra-python/keys.png)

2. Otevřete soubor *config.py* . 

3. Vložte hodnotu KONTAKTNÍ BOD z portálu místo `<FILLME>` na řádku 10.

    Řádek 10 by teď měl vypadat nějak takto: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Zkopírujte z portálu hodnotu UŽIVATELSKÉ JMÉNO a vložte ji místo `<FILLME>` na řádku 6.

    Řádek 6 by teď měl vypadat nějak takto: 

    `'username': 'cosmos-db-quickstart',`
    
5. Zkopírujte z portálu hodnotu HESLO a vložte ji místo `<FILLME>` na řádku 8.

    Řádek 8 by teď měl vypadat nějak takto:

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Uložte soubor *config.py* .
    
## <a name="use-the-x509-certificate"></a>Použití certifikátu X509

1. Stáhněte si certifikát Baltimore CyberTrust Root místně z [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Přejmenujte soubor pomocí přípony souboru *. cer*.

   Certifikát má sériové číslo `02:00:00:b9` a otisk SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Otevřete *pyquickstart.py* a změňte `path\to\cert` tak, aby odkazovalo na nový certifikát.

3. Uložte *pyquickstart.py*.

## <a name="run-the-python-app"></a>Spuštění aplikace v Pythonu

1. Pomocí příkazu cd v terminálu Git přejděte do složky `azure-cosmos-db-cassandra-python-getting-started`. 

2. Spusťte následující příkazy a nainstalujte požadované moduly:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Spuštěním následujícího příkazu spusťte aplikaci v Pythonu:

    ```
    python pyquickstart.py
    ```

3. Na příkazovém řádku zkontrolujte očekávané výsledky.

    Stisknutím kombinace kláves CTRL + C ukončete provádění programu a zavřete okno konzoly. 

    ![Zobrazení a ověření výstupu](./media/create-cassandra-python/output.png)
    
4. Na portálu Azure Portal otevřete **Data Explorer**, abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi. 

    ![Zobrazení dat v Průzkumníku dat](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB pomocí rozhraní API Cassandra a spustit aplikaci v Pythonu Cassandra, která vytvoří databázi Cassandra a kontejner. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)

