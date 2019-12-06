---
title: 'Kurz: Azure Cosmos DB globální distribuční kurz pro rozhraní SQL API'
description: 'Kurz: Naučte se, jak nastavit globální distribuci Azure Cosmos DB pomocí rozhraní SQL API s využitím .NET, Java, Pythonu a různých dalších sad SDK.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 541fcdd966ec1e0443fa6211d894bab3ed965f93
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870322"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Kurz: nastavení globální distribuce Azure Cosmos DB pomocí rozhraní SQL API

V tomto článku si ukážeme, jak pomocí webu Azure Portal nastavit globální distribuci služby Azure Cosmos DB a pak se k ní připojit pomocí rozhraní SQL API.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [SQL API](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Připojení k preferované oblasti pomocí rozhraní SQL API

Aby mohly využívat [globální distribuci](distribute-data-globally.md), můžou mít klientské aplikace určený seřazený seznam preferovaných oblastí, které se mají použít k provádění operací s dokumenty. To je možné provést nastavením zásady připojení. V závislosti na konfiguraci účtu služby Azure Cosmos DB, aktuální regionální dostupnosti a zadaného seznamu preferencí zvolí sada SQL SDK optimální koncový bod, který bude provádět operace čtení a zápisu.

Tento seznam preferencí se zadává při inicializaci připojení pomocí sad SQL SDK. Sady SDK přijímají volitelný parametr PreferredLocations, což je seřazený seznam oblastí Azure.

Sada SDK bude automaticky odesílat všechny operace zápisu do aktuální oblasti pro zápis.

Všechny operace čtení se budou odesílat do první dostupné oblasti v seznamu PreferredLocations. Pokud požadavek selže, klient přejde k další oblasti v seznamu atd.

Sady SDK se budou pokoušet číst pouze z oblastí uvedených v seznamu PreferredLocations. Takže pokud je například účet databáze dostupný ve čtyřech oblastech, ale v seznamu PreferredLocations klienta jsou uvedené pouze dvě oblasti pro čtení (nikoli zápis), z oblastí, které nejsou uvedené v seznamu PreferredLocations, se číst nebude. V případě nedostupnosti oblastí pro čtení uvedených v seznamu PreferredLocations se čtení bude provádět z oblasti pro zápis.

Aplikace může ověřit aktuální koncový bod pro čtení a koncový bod pro zápis, které sada SDK zvolila, kontrolou dvou vlastností WriteEndpoint a ReadEndpoint, které jsou dostupné v sadě SDK verze 1.8 a novější.

Pokud vlastnost PreferredLocations není nastavená, všechny požadavky se budou obsluhovat z aktuální oblasti pro zápis.

## <a name="net-sdk"></a>.NET SDK
Sadu SDK můžete využívat bez jakýchkoli změn kódu. V tomto případě sada SDK automaticky směruje operace čtení i zápisu do aktuální oblasti pro zápis.

V sadě .NET SDK verze 1.8 a novější má parametr ConnectionPolicy pro konstruktor DocumentClient vlastnost Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Tato vlastnost je typu Kolekce `<string>` a měla by obsahovat seznam názvů oblastí. Řetězcové hodnoty jsou formátovány podle sloupce název oblasti na stránce [oblasti Azure][regions] , bez mezer před nebo za prvním a posledním znakem.

Aktuální koncové body pro čtení a zápis jsou k dispozici ve vlastnostech DocumentClient.WriteEndpoint a DocumentClient.ReadEndpoint.

> [!NOTE]
> Adresy URL koncových bodů by se neměly považovat za dlouhodobé konstanty. Služba je může kdykoli aktualizovat. Sada SDK tuto změnu zpracuje automaticky.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node. js/JavaScript

> [!NOTE]
> Adresy URL koncových bodů by se neměly považovat za dlouhodobé konstanty. Služba je může kdykoli aktualizovat. Sada SDK tuto změnu zpracuje automaticky.
>
>

Níže je příklad kódu pro Node. js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

Následující kód ukazuje, jak nastavit Upřednostňovaná umístění pomocí sady Python SDK:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>Sada Java v2 SDK

Následující kód ukazuje, jak nastavit Upřednostňovaná umístění pomocí sady Java SDK:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Po zpřístupnění účtu databáze ve více oblastech můžou klienti dotazovat její dostupnost provedením požadavku GET na následující identifikátor URI.

    https://{databaseaccount}.documents.azure.com/

Služba vrátí seznam oblastí a jejich odpovídajících identifikátorů URI koncových bodů služby Azure Cosmos DB pro repliky. V odpovědi bude uvedená aktuální oblast pro zápis. Klient si pak může vybrat vhodný koncový bod pro všechny další požadavky rozhraní REST API následujícím způsobem.

Příklad odpovědi

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Všechny požadavky PUT,POST a DELETE musí směřovat na uvedený identifikátor URI pro zápis.
* Všechny požadavky GET a další požadavky jen pro čtení (například dotazy) můžou směřovat do koncového bodu podle výběru klienta.

Požadavky na zápis do oblastí jen pro čtení nebudou úspěšné. Zobrazí se kód chyby HTTP 403 (zakázáno).

Pokud se po počáteční fázi zjišťování klienta oblast pro zápis změní, nebudou další zápisy do předchozí oblasti pro zápis úspěšné. Zobrazí se kód chyby HTTP 403 (zakázáno). Klient pak musí pomocí požadavku GET znovu získat seznam oblastí, aby získal aktualizovanou oblast pro zápis.

To je vše, tento kurz je u konce. Informace o správě konzistence vašeho globálně replikovaného účtu najdete v tématu [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md). Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní SQL API

Teď můžete přejít k dalšímu kurzu, ve kterém se naučíte vyvíjet místně s využitím místního emulátoru služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Místní vývoj s využitím emulátoru](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

