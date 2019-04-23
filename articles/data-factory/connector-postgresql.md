---
title: Kopírování dat z PostgreSQL pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z for postgresql – úložiště dat jímky podporované s využitím aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 8515b3f357d77ea4f3d98101f8dd058f13b69206
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405745"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopírování dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuální verze](connector-postgresql.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze PostgreSQL. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze PostgreSQL do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor PostgreSQL podporuje PostgreSQL **verze 7.4 a vyšší**.

## <a name="prerequisites"></a>Požadavky

Pokud vaše databáze PostgreSQL není veřejně přístupná, budete muset nastavit modul Integration Runtime. Další informace o prostředí v místním prostředí integration Runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku. Prostředí Integration Runtime poskytuje integrované PostgreSQL ovladačů počínaje od verze 3.7, proto není nutné ručně nainstalovat všechny ovladače.

Pro místní prostředí IR verze nižší než 3.7, je potřeba nainstalovat [Ngpsql zprostředkovatel dat pro PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) verze 2.0.12 až 3.1.9 na počítači prostředí Integration Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor PostgreSQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro PostgreSQL propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **PostgreSql** | Ano |
| připojovací řetězec | ODBC připojovací řetězec služby pro připojení ke službě Azure Database for PostgreSQL. <br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Heslo můžete také vložit do služby Azure Key Vault a o přijetí změn `password` konfigurace z připojovacího řetězce. Podívejte se na následující ukázky a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda ovladač používá k šifrování dat posílaných mezi ovladač a databázový server. Například `ValidateServerCertificate=<0/1/6>;`| 0 (žádné šifrování) **(výchozí)** / 1 (SSL) / 6 (RequestSSL) | Ne |
| ValidateServerCertificate (virtuální čipové karty) | Určuje, jestli ovladač ověřil certifikát, který je odesláno serverem databáze, když je povoleno šifrování protokolu SSL (metoda šifrování = 1). Například `ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** / 1 (povoleno) | Ne |

**Příklad:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: ukládání hesel ve službě Azure Key Vault**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pokud jste používali služba PostgreSQL propojené s následující datová část, je stále podporovány jako-se, když byly navrženy používat novou do budoucna.

**Předchozí datové části:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje PostgreSQL datové sady.

Ke zkopírování dat z PostgreSQL, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi PostgreSQL. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako zdroj

Ke zkopírování dat z PostgreSQL, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Ne (když je "tableName" v datové sadě zadán) |

> [!NOTE]
> Schéma a tabulku názvy jsou malá a velká písmena. Vložte je do `""` (dvojité uvozovky) v dotazu.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
