---
title: Migrace existujících dat do účtu table api v Azure Cosmos DB
description: Zjistěte, jak migrovat nebo importovat místní nebo cloudová data do účtu Azure Table API v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5c828644cb03d83df38265719cd8afabc24cf739
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "66242569"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrace dat do účtu rozhraní Table API služby Azure Cosmos DB

Tento kurz obsahuje pokyny k importu dat pro použití s [rozhraním API tabulky](table-introduction.md)DB Azure Cosmos . Pokud máte data uložená ve službě Azure Table Storage, můžete k importu dat do rozhraní Table API služby Azure Cosmos DB použít nástroj pro migraci dat nebo AzCopy. Pokud máte data uložená v účtu rozhraní Table API služby Azure Cosmos DB (Preview), musíte k migraci dat použít nástroj pro migraci dat. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Import dat pomocí nástroje pro migraci dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z rozhraní Table API (Preview) na rozhraní Table API 

## <a name="prerequisites"></a>Požadavky

* **Zvýšení propustnosti:** Doba trvání migrace dat závisí na velikosti propustnosti, kterou jste nastavili pro jednotlivé kontejnery nebo sadu kontejnerů. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti na webu Azure Portal najdete v tématu Úrovně výkonu a cenové úrovně ve službě Azure Cosmos DB.

* **Vytvoření prostředků služby Azure Cosmos DB:** Ještě před zahájením migrace dat vytvořte všechny tabulky na webu Azure Portal. Pokud provádíte migraci na účet služby Azure Cosmos DB, který má propustnost na úrovni databáze, nezapomeňte při vytváření tabulek Azure Cosmos DB zadat klíč oddílu.

## <a name="data-migration-tool"></a>Nástroj pro migraci dat

Nástroj příkazového řádku pro migraci dat Azure Cosmos DB (dt.exe) je možné použít k importu stávajících dat ve službě Azure Table Storage do účtu obecně dostupného rozhraní Table API nebo k migraci dat z účtu rozhraní Table API (Preview) do účtu obecně dostupného rozhraní Table API. Ostatní zdroje se v současné době nepodporují. Nástroj pro migraci dat s uživatelským rozhraním (dtui.exe) se v současné době nepodporuje pro účty rozhraní Table API. 

Pokud chcete provést migraci tabulkových dat, proveďte následující úlohy:

1. Stáhněte si nástroj pro migraci z [GitHubu](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Spusťte `dt.exe` s použitím argumentů příkazového řádku pro váš scénář. `dt.exe` přijímá příkaz v následujícím formátu:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Možnosti příkazu jsou:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>Nastavení zdroje na příkazovém řádku

Při definování služby Azure Table Storage nebo rozhraní Table API Preview jako zdroje migrace použijte následující možnosti zdroje.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Pokud chcete zdrojový připojovací řetězec načíst při importu z úložiště Azure Table, otevřete portál Azure portal a klikněte na**klíče přístupu k****účtu účtů** >  **úložiště** > a potom pomocí tlačítka copy zkopírujte **připojovací řetězec**.

![Snímek obrazovky s možnostmi zdroje HBase](./media/table-import/storage-table-access-key.png)

Pokud chcete zdrojový připojovací řetězec načíst při importu z účtu Azure Cosmos DB Table API (preview), otevřete portál Azure, klikněte na**připojovací řetězec** **účtu** >  **Azure Cosmos DB** > a pomocí tlačítka copy zkopírujte **připojovací řetězec**.

![Snímek obrazovky s možnostmi zdroje HBase](./media/table-import/cosmos-connection-string.png)

[Ukázkový příkaz pro Azure Table Storage](#azure-table-storage)

[Ukázkový příkaz pro rozhraní Table API služby Azure Cosmos DB (Preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Nastavení cíle na příkazovém řádku

Při definování rozhraní Table API služby Azure Storage jako cíle migrace použijte následující možnosti cíle.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Ukázkový příkaz: Zdrojem je Azure Table Storage

Tady je ukázka příkazového řádku znázorňující provedení importu ze služby Azure Table Storage do rozhraní Table API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Ukázkový příkaz: Zdrojem je rozhraní Table API služby Azure Cosmos DB (Preview)

Tady je ukázka příkazového řádku pro import z rozhraní Table API Preview do obecně dostupného rozhraní Table API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrace dat pomocí AzCopy

Druhou možností, jak migrovat data ze služby Azure Table Storage do rozhraní Table API služby Azure Cosmos DB, je použít nástroj příkazového řádku AzCopy. Pokud chcete použít AzCopy, je potřeba nejprve data exportovat, jak je popsáno v části [Export dat ze služby Table Storage](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage), a pak data importovat do služby Azure Cosmos DB, jak je popsáno v části [Rozhraní Table API služby Azure Cosmos DB](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Při provádění importu do služby Azure Cosmos DB použijte jako referenci následující ukázku. Všimněte si, že v hodnotě /Dest se používá cosmosdb, a ne core.

Ukázka příkazu pro import:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrace z rozhraní Table API (Preview) na rozhraní Table API

> [!WARNING]
> Pokud chcete okamžitě začít využívat výhod obecně dostupných tabulek, migrujte své stávající tabulky verze Preview, jak je popsáno v této části. Jinak pro stávající zákazníky verze Preview v nadcházejících týdnech provedeme automatickou migraci. Mějte však na paměti, že pro automaticky migrované databáze verze Preview budou platit určitá omezení, která se na nově vytvořené tabulky vztahovat nebudou.
> 

Rozhraní Table API je teď obecně dostupné. Mezi tabulkami verze Preview a obecně dostupnými tabulkami existují rozdíly jak v kódu, který běží v cloudu, tak v kódu, který běží v klientovi. Proto se nedoporučuje pokoušet se o kombinaci klienta sady SDK verze Preview a účtu obecně dostupného rozhraní Table API a naopak. Zákazníci rozhraní Table API Preview, kteří chtějí i nadále používat své stávající tabulky, ale v produkčním prostředí, musí provést migraci z verze Preview do obecně dostupného prostředí nebo počkat na automatickou migraci. Pokud počkáte na automatickou migraci, dostanete upozornění na omezení migrovaných tabulek. Po migraci budete ve svém stávajícím účtu moct vytvářet nové tabulky bez omezení (omezení se budou vztahovat pouze na migrované tabulky).

Migrace z rozhraní Table API (Preview) na obecně dostupné rozhraní Table API:

1. Vytvořte nový účet služby Azure Cosmos DB a nastavte jeho typ rozhraní API na Azure Table, jak je popsáno v části [Vytvoření účtu databáze](create-table-dotnet.md#create-a-database-account).

2. Upravte klienty tak, aby používali obecně dostupnou verzi sad [Table API SDK](table-sdk-dotnet.md).

3. Pomocí nástroje pro migraci dat migrujte klientská data z tabulek verze Preview do obecně dostupných tabulek. Pokyny pro použití nástroje pro migraci dat k tomuto účelu jsou popsané v části [Nástroj pro migraci dat](#data-migration-tool). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Import dat pomocí nástroje pro migraci dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z rozhraní Table API (Preview) na rozhraní Table API

Teď můžete pokračovat k dalšímu kurzu, kde zjistíte, jak dotazovat data pomocí rozhraní Table API služby Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak dotazovat data?](../cosmos-db/tutorial-query-table.md)
