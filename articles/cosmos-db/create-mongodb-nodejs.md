---
title: Připojení aplikace Node. js MongoDB k Azure Cosmos DB
description: Tento rychlý Start ukazuje, jak připojit existující aplikaci MongoDB napsanou v Node. js k Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.openlocfilehash: 0ff15c29fb7d71919b54cbcd73ead30a8673e0f7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543090"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Rychlý start: Migrace stávající webové aplikace Node. js v MongoDB na Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, klíčů a hodnot a grafů, z nichž všechno přináší výhody funkcí globální distribuce a horizontálního škálování v jádru Cosmos DB. 

Tento rychlý Start ukazuje použití stávající aplikace MongoDB napsané v Node. js a její připojení k databázi Cosmos, která podporuje klienta MongoDB. Jinými slovy je transparentní pro aplikaci, že jsou data uložena v databázi Cosmos.

Až budete hotovi, budete mít střední hodnotu aplikace (MongoDB, Express, úhlová a Node. js) běžící na [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Aplikace MEAN.js spuštěná v rámci služby Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Abyste mohli spustit příkazy `npm` a `git`, musíte mít kromě rozhraní příkazového řádku Azure místně nainstalovaný [Node.js](https://nodejs.org/) a [Git](https://www.git-scm.com/downloads).

Měli byste mít praktickou znalost Node.js. Tento rychlý start není určen jako pomůcka pro všeobecný vývoj aplikací v Node.js.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Toto ukázkové úložiště obsahuje výchozí aplikaci [MEAN.js](https://meanjs.org/).

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd mean
npm install
npm start
```
Aplikace se pokusí připojit ke zdroji MongoDB, selže a ukončí se, když se na výstupu vrátí chyba [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud používáte nainstalované rozhraní příkazového řádku Azure, přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login) a postupujte podle pokynů na obrazovce. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Přidání modulu služby Azure Cosmos DB

Pokud používáte nainstalované rozhraní příkazového řádku Azure, zkontrolujte pomocí příkazu `az`, zda je komponenta `cosmosdb` už nainstalovaná. Pokud se komponenta `cosmosdb` v seznamu základních příkazů nachází, pokračujte k dalšímu příkazu. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

Pokud se komponenta `cosmosdb` v seznamu základních příkazů nenachází, přeinstalujte [rozhraní příkazového řádku Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte pomocí příkazu [az group create](/cli/azure/group#az-group-create) [skupinu prostředků](../azure-resource-manager/resource-group-overview.md). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například webové aplikace, databáze a účty úložiště. 

Následující příklad vytvoří skupinu prostředků pro oblast Západní Evropa. Pro skupinu prostředků vyberte jedinečný název.

Pokud používáte Azure Cloud Shell, vyberte **vyzkoušet**, postupujte podle pokynů na obrazovce pro přihlášení a pak zkopírujte příkaz do příkazového řádku.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Pomocí příkazu [AZ cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) vytvořte účet Cosmos.

V následujícím příkazu nahraďte `<cosmosdb-name>` zástupný symbol vlastním jedinečným názvem účtu Cosmos. Tento jedinečný název se použije jako součást Cosmos DBho koncového bodu (`https://<cosmosdb-name>.documents.azure.com/`), takže název musí být jedinečný ve všech účtech Cosmos v Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umožňuje klientská připojení MongoDB.

Po vytvoření účtu služby Azure Cosmos DB zobrazí rozhraní příkazového řádku Azure podobné informace jako v následujícím příkladu. 

> [!NOTE]
> Tento příklad jako formát výstupu Azure CLI používá výchozí JSON. Pokud chcete použít jiný formát výstupu, přečtěte si téma [Formáty výstupu pro příkazy Azure CLI](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Připojení aplikace Node.js k databázi

V tomto kroku připojíte ukázkovou aplikaci střední. js k databázi Cosmos, kterou jste právě vytvořili. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurace připojovacího řetězce v aplikaci Node.js

V úložišti MEAN.js otevřete `config/env/local-development.js`.

Obsah tohoto souboru nahraďte následujícím kódem. Nezapomeňte také nahradit tyto dva `<cosmosdb-name>` zástupné symboly názvem vašeho účtu Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Načtení klíče

Aby bylo možné se připojit k databázi Cosmos, potřebujete klíč databáze. Pro načtení primárního klíče použijte příkaz [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Výstupní informace rozhraní příkazového řádku Azure jsou podobné jako v následujícím příkladu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Zkopírujte hodnotu `primaryMasterKey`. Vložte ji místo `<primary_master_key>` v souboru `local-development.js`.

Uložte provedené změny.

### <a name="run-the-application-again"></a>Spusťte aplikaci znovu.

Spusťte `npm start` znovu. 

```bash
npm start
```

Zpráva konzoly by vás teď měla informovat o tom, že vývojové prostředí je připravené a spuštěné. 

V prohlížeči `http://localhost:3000` přejdete na. V horní nabídce vyberte možnost **zaregistrovat** se a zkuste vytvořit dva fiktivní uživatele. 

Ukázková aplikace MEAN.js ukládá data uživatelů v databázi. Pokud budete úspěšní a aplikace MEAN.js automaticky zaregistruje vytvořeného uživatele, bude připojení Azure Cosmos DB fungovat. 

![Aplikace MEAN.js se úspěšně připojí k databázi MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Zobrazení dat v Průzkumníku dat

Data uložená v databázi Cosmos jsou k dispozici pro zobrazení a dotazování v Azure Portal.

Pokud chcete zobrazovat uživatelská data vytvořená v předchozím kroku, zadávat na ně dotazy a pracovat s nimi, přihlaste se k portálu [Azure Portal](https://portal.azure.com) ve webovém prohlížeči.

Do vyhledávacího pole nahoře zadejte **Azure Cosmos DB**. Když se otevře okno účtu Cosmos, vyberte svůj účet Cosmos. V levém navigačním panelu vyberte **Průzkumník dat**. Rozbalte kolekci v podokně Kolekce. Pak můžete zobrazovat dokumenty v kolekci, dotazovat se na data a dokonce vytvářet a spouštět uložené procedury, triggery a funkce UDF. 

![Průzkumník dat na portálu Azure Portal](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Nasaďte aplikaci Node.js do platformy Azure

V tomto kroku nasadíte aplikaci Node. js na Cosmos DB.

Pravděpodobně jste si všimli, že dříve změněný soubor konfigurace je určený pro vývojové prostředí (`/config/env/local-development.js`). Pokud nasadíte aplikaci do služby App Service, bude se ve výchozím nastavení spouštět v produkčním prostředí. Teď tedy musíte provést stejné změny v příslušném souboru konfigurace.

V úložišti MEAN.js otevřete `config/env/production.js`.

V objektu `db` nahraďte hodnotu `uri` tak, jak je uvedeno v následujícím příkladu. Nezapomeňte nahradit zástupné symboly jako v předcházejícím případě.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Možnost `ssl=true` je důležitá, protože [Cosmos DB vyžaduje protokol SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

V terminálu potvrďte do systému Git všechny změny. Můžete zkopírovat oba příkazy a spouštět je společně.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Cosmos, vytvořit kolekci a spustit konzolovou aplikaci. Nyní můžete importovat další data do databáze Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
