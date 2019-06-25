---
title: Kopírování dat do nebo z rozhraní API služby Azure Cosmos DB pro MongoDB pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště dat do nebo z rozhraní API služby Azure Cosmos DB pro MongoDB do jímky podporované úložišť pomocí služby Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61259711"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopírování dat do nebo z rozhraní API služby Azure Cosmos DB pro MongoDB pomocí Azure Data Factory

Tento článek popisuje, jak používat aktivitu kopírování, která ve službě Azure Data Factory pro kopírování dat z a do Azure Cosmos DB přes rozhraní API pro MongoDB. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

>[!NOTE]
>Tento konektor podporuje pouze kopírování dat do a z Azure Cosmos DB přes rozhraní API pro MongoDB. Rozhraní SQL API najdete v tématu [konektoru SQL API služby Cosmos DB](connector-azure-cosmos-db.md). Jiné typy rozhraní API nejsou nyní podporovány.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírování dat z rozhraní API služby Azure Cosmos DB pro MongoDB do jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do rozhraní API služby Azure Cosmos DB pro MongoDB. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Rozhraní API Azure Cosmos DB můžete použít konektor MongoDB:

- Kopírování dat z a do [rozhraní API služby Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapsat do služby Azure Cosmos DB jako **vložit** nebo **upsert**.
- Import a export dokumentů JSON jako-je, nebo zkopírovat data z nebo do tabulkové datové sady. Mezi příklady patří SQL database a soubor CSV. Zkopírujte dokumenty s kódováním – je do nebo ze souborů JSON nebo do a z jiné kolekce Azure Cosmos DB, najdete v části Import nebo export dokumentů JSON.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro rozhraní API služby Azure Cosmos DB pro MongoDB.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro rozhraní API Azure Cosmos DB pro MongoDB propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **CosmosDbMongoDbApi**. | Ano |
| connectionString |Zadejte připojovací řetězec pro Azure Cosmos DB pro rozhraní API pro MongoDB. Najdete ho na webu Azure Portal -> okna vaší služby Cosmos DB -> primární nebo sekundární připojovací řetězec s vzor `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| database | Název databáze, které chcete získat přístup. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace v Azure nebo místního prostředí integration runtime. Pokud není tato vlastnost určena, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). Pro rozhraní API služby Azure Cosmos DB pro MongoDB datové sady jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **CosmosDbMongoDbApiCollection**. |Ano |
| collectionName |Název kolekce Azure Cosmos DB. |Ano |

**Příklad**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností, které podporují rozhraní API Azure Cosmos DB pro MongoDB zdroje a jímky.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **CosmosDbMongoDbApiSource**. |Ano |
| filter | Určuje filtr výběru pomocí operátorů pro dotazování. Který vrátí všechny dokumenty v kolekci, tento parametr vynecháte nebo předat prázdný dokument ({}). | Ne |
| cursorMethods.project | Určuje pole, která vrátí v dokumentech pro projekci. Tento parametr vynechte, pokud se pokud chcete vrátit všechna pole v odpovídajících dokumentů. | Ne |
| cursorMethods.sort | Určuje pořadí, ve které dotaz vrátí odpovídajících dokumentů. Odkazovat na [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Ne |
| cursorMethods.limit | Určuje maximální počet dokumentů, který server vrátí. Odkazovat na [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Ne | 
| cursorMethods.skip | Určuje počet dokumentů, chcete-li přeskočit a od které začíná MongoDB pro vracení výsledků. Odkazovat na [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Ne |
| batchSize | Určuje počet dokumenty, které se vrátí v každé dávce odpovědi z MongoDB instance. Ve většině případů Změna velikosti dávky nebude mít vliv na uživatele nebo aplikace. Cosmos DB omezení jednotlivých dávek nemůže být delší než 40MB velikosti, který je součtem batchSize počet dokumentů velikost, takže zmenšete tuto hodnotu, pokud probíhá velké velikosti vašeho dokumentu. | Ne<br/>(výchozí hodnota je **100**) |

>[!TIP]
>ADF podporují využití návratových hodnot BSON dokumentu v **přísném režimu**. Zajistěte, aby že vašemu dotazu filtru je ve striktním režimu místo režimu prostředí. Další popis lze nalézt v [MongoDB ruční](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB jako jímka

Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na **CosmosDbMongoDbApiSink**. |Ano |
| writeBehavior |Popisuje, jak zapsat data do služby Azure Cosmos DB. Povolené hodnoty: **vložit** a **upsert**.<br/><br/>Chování **upsert** nahrazuje dokumentu, pokud dokument se stejným ID už existuje; v opačném případě vložit dokument.<br /><br />**Poznámka:** Data Factory automaticky vygeneruje ID dokumentu, pokud ID není zadán, buď v původním dokumentu nebo mapování sloupců. To znamená, že musíte zajistit, aby, pro **upsert** fungovat podle očekávání, váš dokument nemá identifikátor. |Ne<br />(výchozí hodnota je **vložit**) |
| writeBatchSize | **WriteBatchSize** vlastnost určuje velikost dokumenty pro zápis v každé dávce. Můžete zkusit zvýšit hodnotu **writeBatchSize** ke zlepšení výkonu a snížení hodnoty, pokud probíhá velké velikosti vašeho dokumentu. |Ne<br />(výchozí hodnota je **10 000**) |
| writeBatchTimeout | Doba čekání pro dávku vložte na dokončení před vypršením časového limitu operace. Povolená hodnota je časový interval. | Ne<br/>(výchozí hodnota je **00:30:00** – 30 minut) |

**Příklad**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>K importu dokumentů JSON jako-se odkazovat na [Import nebo export dokumentů JSON](#import-or-export-json-documents) části; chcete-li zkopírovat z tabulkového upravená data, přečtěte si [mapování schématu](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Import a export dokumentů JSON

Tento konektor služby Azure Cosmos DB můžete snadno:

* Importujte dokumenty JSON z různých zdrojů do služby Azure Cosmos DB, včetně z úložiště objektů Blob v Azure, Azure Data Lake Store a další souborové úložiště, které podporuje Azure Data Factory.
* Exportujte dokumentů JSON z kolekce Azure Cosmos DB do různých úložišť založená na souborech.
* Zkopírujte dokumenty mezi dvě kolekce Azure Cosmos DB jako-je.

K dosažení těchto kopírování dogmaticky na schématu, přeskočte "struktura" (také nazývané *schématu*) části datovou sadu a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>mapování schématu

Kopírování dat z rozhraní API služby Azure Cosmos DB pro MongoDB do tabulky jímky nebo vrátit zpět, odkazuje [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

Konkrétně pro zápis do služby Cosmos DB, zajistit, aby že naplnit Cosmos DB s ID objektu vpravo od zdroje dat, třeba máte sloupec "id" v tabulce databáze SQL a chcete použít hodnotu, která jako ID dokumentu v MongoDB pro vložení/upsertovat , budete muset nastavit mapování správné schéma podle definice přísném režimu MongoDB (`_id.$oid`) následujícím způsobem:

![Mapování ID Sink MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po kopírování jímky generován provádění aktivity pod BSON ObjectId:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
