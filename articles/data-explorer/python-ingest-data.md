---
title: Ingestování dat pomocí knihovny Python Průzkumník dat Azure
description: V tomto článku se dozvíte, jak se přijmout data (načíst) do Průzkumníku dat Azure pomocí Pythonu.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: da23ec91891776e9a459b04c5718147427843991
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496925"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Ingestování dat pomocí knihovny Python Průzkumník dat Azure

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Průzkumník dat Azure nabízí dvě klientské knihovny pro Python: [knihovnu ingestů](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) a [knihovnu dat](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Tyto knihovny umožňují snadno ingestovat (načíst) data do clusteru a dotazovat se na data z vašeho kódu. V tomto článku vytvoříte první tabulky a mapování dat v clusteru. Pak vytvoříte frontu ingestace do clusteru a ověříte výsledky.

Tento článek je také k dispozici [poznámkového bloku Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* [Cluster a databáze](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) nainstalovaný na počítači pro vývoj

## <a name="install-the-data-and-ingest-libraries"></a>Instalace knihovny dat a knihovny ingestů

Nainstalujte *azure-kusto-data* a *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Přidání příkazů a konstant pro import

Importovat třídy z dat azure kusto.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

K ověření aplikace používá Azure Data Explorer ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

V tomto případě je ID tenanta `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Před spuštěním tohoto kódu nastavte hodnoty pro AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI a KUSTO_DATABASE.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Teď vytvořte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít [certifikát aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klíč aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20), a [AAD uživatele a heslo](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

Cílovou tabulku a mapování vytvoříte v pozdějším kroku.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Nastavení informací o zdrojovém souboru

Importujte další třídy a nastavte konstanty pro soubor zdroje dat. Tento příklad používá ukázkový soubor hostovaný v Azure Blob Storage. Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Vytvoření tabulky ve vašem clusteru

Vytvořte tabulku, která odpovídá schématu dat v souboru StormEvents.csv. Když tento kód se spustí, vrátí se zpráva podobná následující: *Pro přihlášení, použijte webový prohlížeč a otevřete stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu. Následující bloky kódu, které provedou připojení, vyžadují, abyste se znovu přihlásili.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definování mapování ingestace

Namapujte příchozí data CSV na názvy sloupců a datové typy použité při vytváření tabulky. To se mapuje pole zdroje dat na sloupce cílové tabulky

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Přidání zprávy do fronty pro ingestaci

Přidejte zprávu do fronty k získání dat z úložiště objektů blob a tato data ingestujte do Průzkumníka dat Azure.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference = DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # FILE_SIZE is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="query-data-that-was-ingested-into-the-table"></a>Dotazování dat, který se ingestuje do tabulky

Počkejte pět až deset minut, než ingestace ve frontě naplánuje ingestování a načte data do Průzkumníka dat Azure. Pak spuštěním následujícího kódu získejte počet záznamů v tabulce StormEvents.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Spuštění dotazů pro řešení potíží

Přihlaste se k [https://dataexplorer.azure.com](https://dataexplorer.azure.com) a připojte se k vašemu clusteru. Spuštěním následujícího příkazu ve vaší databázi zjistíte, jestli za poslední čtyři hodiny došlo k chybám ingestování. Přes spuštěním nahraďte název databáze.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Spuštěním následujícího příkazu zobrazíte stav všech operací ingestace za poslední čtyři hodiny. Přes spuštěním nahraďte název databáze.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít postupujte podle našich článků, zachovat prostředky, které jste vytvořili. Pokud ne, spuštěním následujícího příkazu v databázi tabulku StormEvents vyčistěte.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Další postup

* [Dotazování dat pomocí Pythonu](python-query-data.md)
