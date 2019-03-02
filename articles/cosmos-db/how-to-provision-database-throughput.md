---
title: Zřízení propustnosti databáze ve službě Azure Cosmos DB
description: Zjistěte, jak zřídit propustnost na úrovni databáze ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8c2fe8d42ded5a072a2211b102736326554bc4f4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239882"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Zřízení propustnosti pro databázi ve službě Azure Cosmos DB

Tento článek vysvětluje, jak zřídit propustnost pro kontejner databázi ve službě Azure Cosmos DB. Propustnost můžete zřídit pro jeden [kontejner](how-to-provision-container-throughput.md) nebo pro databázi a sdílet ji mezi kontejnery v rámci databáze. Informace o tom, kdy použít propustnosti kontejneru úrovni a úrovni databáze, najdete v článku [případy použití pro zřizování propustnosti na kontejnerech a databází](set-throughput.md) článku. Databáze úrovně propustnosti můžete zřídit pomocí webu Azure portal nebo sady SDK služby Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Propustnost zřízení pomocí webu Azure portal

### <a id="portal-sql"></a>Rozhraní SQL (Core) API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvoření nového účtu služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte **novou databázi**. Zadejte následující podrobnosti:

   * Zadejte ID databáze. 
   * Vyberte **zřídit propustnost**.
   * Zadejte propustnost (třeba 1 000 ru).
   * Vyberte **OK**.

![Dialogové okno snímek obrazovky s novou databázi](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Zřizování propustnosti s použitím sady .NET SDK

> [!Note]
> Propustnost pro všechna rozhraní API můžete zřídit pomocí rozhraní SQL API. Volitelně můžete v následujícím příkladu pro Apache Cassandra API také.

### <a id="dotnet-all"></a>Všechna rozhraní API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Rozhraní API Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Další postup

Zobrazit další informace o zřizování propustnosti ve službě Azure Cosmos DB v následujících článcích:

* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
