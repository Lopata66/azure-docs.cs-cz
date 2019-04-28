---
title: Kopírování dat z Teradata pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o konektoru Teradata služby Data Factory, která umožňuje kopírování dat z databáze Teradata do úložišť dat podporovaných službou Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: e9fd818990c8a985a77c2e7eeea19bf63c440e4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347645"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopírování dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuální verze](connector-teradata.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze Teradata. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze Teradata do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 12 a vyšší**.
- Kopírování dat pomocí **základní** nebo **Windows** ověřování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor Teradata, budete muset:

- Nastavte modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Nainstalujte [.NET Data Provider pro Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) verzi 14 nebo vyšší na počítači prostředí Integration Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Teradata propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Teradata** | Ano |
| server | Název serveru Teradata. | Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi Teradata.<br/>Povolené hodnoty jsou: **Základní**, a **Windows**. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k databázi Teradata. | Ano |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností podporovaných datovou sadu Teradata.

Ke zkopírování dat z Teradata, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Teradata.

### <a name="teradata-as-source"></a>Teradata jako zdroj

Ke zkopírování dat z Teradata, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Datový typ mapování pro Teradata

Při kopírování dat z Teradata, se používají následující mapování z Teradata datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat Teradata | Data factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Objekt blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| char |String |
| Clob |String |
| Datum |DateTime |
| Decimal |Decimal |
| Double |Double |
| Obrázek |String |
| Integer |Int32 |
| Interval den |TimeSpan |
| Interval den na hodinu |TimeSpan |
| Den intervalu minuty. |TimeSpan |
| Den intervalu sekundy. |TimeSpan |
| Interval Hour |TimeSpan |
| Interval hodinu, minutu |TimeSpan |
| Hodina intervalu sekundy. |TimeSpan |
| Interval minutu |TimeSpan |
| Interval minuty, sekundy. |TimeSpan |
| Interval měsíc |String |
| Interval druhý |TimeSpan |
| Interval rok |String |
| Interval rok měsíc |String |
| Číslo |Double |
| Period(Date) |String |
| Period(Time) |String |
| Období (čas s časovým pásmem) |String |
| Period(Timestamp) |String |
| Období (časové razítko s časovým pásmem) |String |
| SmallInt |Int16 |
| Čas |TimeSpan |
| Čas s časovým pásmem |String |
| Timestamp |DateTime |
| Časové razítko s časovým pásmem |Datetimeoffset |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |String |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
