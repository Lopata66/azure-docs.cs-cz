---
title: Připojení pomocí Node. js – Azure Database for MySQL
description: V tomto rychlém startu najdete několik vzorových kódů Node.js, které můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/02/2019
ms.openlocfilehash: 6773b5d4635089d0977dfa0699549ad22e00aead
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770726"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití Node. js k připojení a dotazování dat v Azure Database for MySQL
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí [Node.js](https://nodejs.org/) z platforem Windows, Ubuntu Linux a Mac. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Toto téma předpokládá, že máte zkušenosti s vývojem pomocí Node.js a teprve začínáte pracovat se službou Azure Database for MySQL.

## <a name="prerequisites"></a>Předpoklady
Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Budete také muset:
- Nainstalovat modul runtime [Node.js](https://nodejs.org).
- Nainstalujte balíček [MySQL](https://www.npmjs.com/package/mysql) pro připojení k MySQL z aplikace Node. js. 

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalace Node.js a konektoru MySQL
V závislosti na vaší platformě nainstalujte Node.js podle pokynů v příslušné části. Pomocí npm nainstalujte balíček MySQL a jeho závislosti do složky projektu.

### <a name="windows"></a>**Windows**
1. Přejděte na [stránku pro stažení Node.js](https://nodejs.org/en/download/) a vyberte požadovanou možnost Instalační služby systému Windows.
2. Vytvořte místní složku projektu, například `nodejsmysql`. 
3. Otevřete příkazový řádek a poté změňte adresář na složku projektu, například `cd c:\nodejsmysql\`
4. Spusťte nástroj NPM a nainstalujte knihovnu MySQL do složky projektu.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Ověřte instalaci kontrolou výstupního textu `npm list`. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. Spuštěním následujících příkazů nainstalujte **Node.js** a **npm** – správce balíčků pro Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Spuštěním následujících příkazů vytvořte složku projektu `mysqlnodejs` a nainstalujte do ní balíček MySQL.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Ověřte instalaci tak, že zkontrolujete výstupní text seznamu npm. Číslo verze se může lišit v závislosti na vydávání nových oprav.

### <a name="mac-os"></a>**Mac OS**
1. Zadejte následující příkazy, abyste nainstalovali **brew**, snadno použitelného správce balíčků pro Mac OS X a **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Spuštěním následujících příkazů vytvořte složku projektu `mysqlnodejs` a nainstalujte do ní balíček MySQL.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Ověřte instalaci kontrolou výstupního textu `npm list`. Číslo verze se může lišit v závislosti na vydávání nových oprav.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo v Azure Portal vyberte **všechny prostředky**a potom vyhledejte server, který jste vytvořili (například **mydemoserver**).
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for MySQL](./media/connect-nodejs/server-name-azure-database-mysql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Spuštění kódu jazyka JavaScript v Node.js
1. Vložte kód jazyka JavaScript do textového souboru a ten uložte s příponou .js do složky projektu (například C:\nodejsmysql\createtable.js nebo /home/username/nodejsmysql/createtable.js).
2. Otevřete příkazový řádek nebo prostředí bash a pak změňte adresář na složku vašeho projektu `cd nodejsmysql`.
3. Chcete-li spustit aplikaci, zadejte příkaz Node následovaný názvem souboru, například `node createtable.js`.
4. Pokud v systému Windows není aplikace Node ve vaší proměnné prostředí PATH, možná bude nutné ke spuštění aplikace Node použít úplnou cestu, například `"C:\Program Files\nodejs\node.exe" createtable.js`.

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat
Pomocí následujícího kódu se připojte a nahrajte data s využitím příkazů **CREATE TABLE** a **INSERT INTO** jazyka SQL.

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Funkce [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Funkce [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. Pole results slouží k ukládání výsledků dotazu.

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **UPDATE** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) slouží k vytvoření rozhraní pro server MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) se používá k navázání připojení k serveru. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) se používá k provedení příkazu jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md)
