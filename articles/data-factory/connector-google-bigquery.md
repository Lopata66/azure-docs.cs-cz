---
title: Kopírování dat z Google BigQuery pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Google BigQuery úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu datové továrny.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 70eb70d7c63d66601aebf8e05a2b14b582058296
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009247"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopírování dat z Google BigQuery pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Google BigQuery. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Google BigQuery se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou zdrojovou maticí](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Kopírování dat z Google BigQuery do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Data Factory poskytuje integrovaný ovladač umožňující připojení. Proto není nutné ručně nainstalovat ovladač pro tento konektor použít.

>[!NOTE]
>Tento konektor Google BigQuery je postavený na rozhraní API BigQuery. Mějte na paměti, že omezení BigQuery maximální rychlost přenosu příchozích požadavků a vynucuje příslušné kvóty na základě jednotlivých projektů, odkazují na [kvóty a omezení – rozhraní API požaduje](https://cloud.google.com/bigquery/quotas#api_requests). Zajistěte, aby že nespouštějí příliš mnoha souběžnými požadavky na účet.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Google BigQuery.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Google BigQuery propojenou službu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **GoogleBigQuery**. | Ano |
| project | ID projektu výchozí projekt BigQuery chcete dotazovat.  | Ano |
| additionalProjects | Čárkou oddělený seznam ID projektu veřejných BigQuery projekty se mají přístup.  | Ne |
| requestGoogleDriveScope | Určuje, zda žádat o přístup k disku Google. Povolení přístupu k disku Google. umožňuje podporu pro federované tabulky, které kombinují BigQuery data s daty z disku Google. Výchozí hodnota je **false**.  | Ne |
| authenticationType | Mechanismus ověřování OAuth 2.0 pro ověřování. ServiceAuthentication lze použít pouze na modul Integration Runtime. <br/>Povolené hodnoty jsou **UserAuthentication** a **ServiceAuthentication**. Naleznete v oddílech dál v této tabulce na další vlastnosti a ukázky JSON pro typy ověřování v uvedeném pořadí. | Ano |

### <a name="using-user-authentication"></a>Pomocí ověřování uživatelů

Nastavte vlastnost "authenticationType" **UserAuthentication**a zadejte následující požadované vlastnosti spolu s generické vlastnosti, které jsou popsané v předchozí části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| clientId | ID aplikace použili k vygenerování tokenu obnovení. | Ne |
| clientSecret | Tajný klíč aplikace použili k vygenerování tokenu obnovení. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| refreshToken | Obnovovací token získaný z Googlu pro autorizaci přístupu k BigQuery. Další informace o získání jednoho z [přístupových tokenů OAuth 2.0 získání](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) a [tento blog komunity](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

**Příklad:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Pomocí ověřování služby

Nastavte vlastnost "authenticationType" **ServiceAuthentication**a zadejte následující požadované vlastnosti spolu s generické vlastnosti, které jsou popsané v předchozí části. Tento typ ověřování lze použít pouze na modul Integration Runtime.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| email | ID účtu služby e-mailu, který se používá pro ServiceAuthentication. Je možné použít pouze na modul Integration Runtime.  | Ne |
| keyFilePath | Úplná cesta k souboru s klíčem .p12, který se používá k ověření e-mailovou adresu účtu služby. | Ne |
| trustedCertPath | Úplná cesta soubor .pem, který obsahuje certifikáty důvěryhodné certifikační Autority používaného k ověřování serveru, když se připojíte přes protokol SSL. Tuto vlastnost lze nastavit pouze při použití protokolu SSL na modul Integration Runtime. Výchozí hodnota je soubor cacerts.pem nainstalované s modulem runtime integrace.  | Ne |
| useSystemTrustStore | Určuje, jestli se má použít certifikát certifikační Autority ze systémového úložiště důvěryhodnosti nebo ze souboru zadaného .pem. Výchozí hodnota je **false**.  | Ne |

**Příklad:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu Google BigQuery.

Ke zkopírování dat z Google BigQuery, nastavte vlastnost typ datové sady na **GoogleBigQueryObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **GoogleBigQueryObject** | Ano |
| integrován | Název datové sady Google BigQuery |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `dataset` a. `table` | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaný typ zdroje Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ zdroje

Ke zkopírování dat z Google BigQuery, nastavte typ zdroje v aktivitě kopírování do **GoogleBigQuerySource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **GoogleBigQuerySource**. | Ano |
| query | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
