---
title: Kopírování dat z a do Oracle pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z podporovaných úložišť zdrojů do databáze Oracle nebo z Oracle na podporovaná úložiště jímky pomocí Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: fd5843ecfd6585fb9c95168c6e55049fe0e27abb
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830305"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopírování dat z a do Oracle pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuální verze](connector-oracle.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do databáze Oracle. Sestaví se na [Přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Oracle se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z databáze Oracle můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do databáze Oracle. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Oracle podporuje:

- Následující verze databáze Oracle:
    - Oracle 18c R1 (18,1) a vyšší
    - Oracle 12c R1 (12,1) a vyšší
    - Oracle 11g R1 (11,1) a vyšší
    - Oracle 10g R1 (10,1) a vyšší
    - Oracle 9i R2 (9,2) a vyšší
    - Oracle 8i R3 (8.1.7) a vyšší
    - Služba Oracle Database Cloud Exadata
- Kopírování dat pomocí ověřování Basic nebo OID.
- Paralelní kopírování ze zdroje Oracle. Podrobnosti najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Proxy server Oracle se nepodporuje.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Modul runtime integrace poskytuje integrovaný ovladač Oracle. Proto nemusíte při kopírování dat z a do Oracle ručně instalovat ovladač.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Oracle.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Propojená služba Oracle podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na hodnotu **Oracle**. | Ano |
| connectionString | Určuje informace potřebné pro připojení k instanci Oracle Database. <br/>Můžete také vložit heslo do Azure Key Vault a načíst `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje uložené v Azure Key Vault](store-credentials-in-key-vault.md) . <br><br>**Podporovaný typ připojení**: k identifikaci databáze můžete použít název **Oracle SID** nebo **Oracle** :<br>– Pokud používáte SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Pokud používáte název služby: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>V případě pokročilých možností nativního připojení Oracle se můžete rozhodnout přidat položku v [souboru Tnsnames. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) soubor na serveru Oracle a v propojené službě ADF Oracle vyberte použít typ připojení název služby Oracle a nakonfigurujte odpovídající název služby. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud se zobrazí chyba, "ORA-01025: parametr UPI je mimo rozsah", a vaše verze Oracle je 8i, přidejte `WireProtocolMode=1` do připojovacího řetězce. Pak to zkuste znovu.

Další vlastnosti připojení můžete nastavit v připojovacím řetězci pro váš případ:

| Vlastnost | Popis | Povolené hodnoty |
|:--- |:--- |:--- |
| ArraySize |Počet bajtů, které může konektor načíst v jedné síťové výměně. Například `ArraySize=‭10485760‬`.<br/><br/>Větší hodnoty zvyšují propustnost tím, že se snižuje počet načítajících dat napříč sítí. Menší hodnoty zvyšují dobu odezvy, protože existuje méně zpoždění čekání na server, který odesílá data. | Celé číslo od 1 do 4294967296 (4 GB). Výchozí hodnota je `60000`. Hodnota 1 nedefinuje počet bajtů, ale označuje přidělení místa pro přesně jeden řádek dat. |

Pokud chcete povolit šifrování u připojení Oracle, máte dvě možnosti:

-   Pokud chcete použít **šifrování 3DES (Triple-DES) a standard AES (Advanced Encryption Standard) (AES)** , na straně serveru Oracle přejít na server Oracle Advanced Security (OAS) a nakonfigurovat nastavení šifrování. Podrobnosti najdete v [dokumentaci k systému Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Konektor pro vývoj aplikací Oracle (ADF) automaticky vyjednává metodu šifrování, aby používala tu, kterou nakonfigurujete v OAS při navazování připojení k Oracle.

-   Použití **protokolu SSL**:

    1.  Získejte informace o certifikátu SSL. Získejte informace o certifikátu SSL s kódováním kódování DER (DER) a uložte výstup (-----zahájit certifikát... Ukončit certifikát-----) jako textový soubor.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Příklad:** Extrahujte informace o certifikátu z DERcert. cer a pak výstup uložte do souboru CERT. txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Sestavte `keystore` nebo `truststore`. Následující příkaz vytvoří soubor `truststore` s heslem nebo bez něj, ve formátu PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Příklad:** Vytvořte soubor PKCS12 `truststore` s názvem MyTrustStoreFile s heslem.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore` soubor umístěte do místního počítače IR. Soubor umístěte například na C:\MyTrustStoreFile.
    4.  V Azure Data Factory nakonfigurujte připojovací řetězec Oracle pomocí `EncryptionMethod=1` a odpovídající `TrustStore`/`TrustStorePassword`hodnota. Například, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Příklad:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: uložení hesla v Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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
## <a name="dataset-properties"></a>Vlastnosti datové sady

V této části najdete seznam vlastností podporovaných datovou sadou Oracle. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z a do Oracle, nastavte vlastnost typ datové sady na `OracleTable`. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na `OracleTable`. | Ano |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table`. | Ne pro zdroj, Ano pro jímku |

**Příklad:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností podporovaných zdrojem a jímkou Oracle. Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle as source

>[!TIP]
>Pokud chcete data z Oracle načítat efektivně pomocí dělení dat, přečtěte si další informace z [paralelní kopie od Oracle](#parallel-copy-from-oracle).

Chcete-li kopírovat data z Oracle, nastavte typ zdroje v aktivitě kopírování na `OracleSource`. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na `OracleSource`. | Ano |
| oracleReaderQuery | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"`.<br>Pokud povolíte rozdělené zatížení, musíte v dotazu připojit všechny odpovídající předdefinované parametry oddílu. Příklady najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z Oracle. <br>Povolené hodnoty jsou: **none** (default), **PhysicalPartitionsOfTable** a **DynamicRange**.<br>Když je povolená možnost oddílu (to znamená, že ne `None`), je stupeň paralelismu na souběžně načtené data z databáze Oracle ovládán nastavením [`parallelCopies`](copy-activity-performance.md#parallel-copy) v aktivitě kopírování. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud není možnost oddílu `None`. | Ne |
| partitionNames | Seznam fyzických oddílů, které je třeba zkopírovat. <br>Použijte, pokud je možnost oddílu `PhysicalPartitionsOfTable`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfTabularPartitionName` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition. <br>Použijte, pokud je možnost oddílu `DynamicRange`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionColumnName` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je možnost oddílu `DynamicRange`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionUpbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je možnost oddílu `DynamicRange`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionLowbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Oracle](#parallel-copy-from-oracle) . | Ne |

**Příklad: kopírování dat pomocí základního dotazu bez oddílu**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle jako jímka

Chcete-li kopírovat data do Oracle, nastavte typ jímky v aktivitě kopírování na `OracleSink`. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na `OracleSink`. | Ano |
| writeBatchSize | Když velikost vyrovnávací paměti dosáhne `writeBatchSize`, vloží data do tabulky SQL.<br/>Povolené hodnoty jsou celé číslo (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout | Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou TimeSpan. Příklad je 00:30:00 (30 minut). | Ne |
| preCopyScript | Zadejte dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do Oracle při každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Paralelní kopírování z Oracle

Konektor Data Factory Oracle nabízí integrované vytváření oddílů dat pro kopírování dat z Oracle paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-oracle/connector-oracle-partition-options.png)

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy na zdroj Oracle a načte data podle oddílů. Paralelní míra je řízena nastavením [`parallelCopies`](copy-activity-performance.md#parallel-copy) u aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z databáze Oracle.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat zvlášť při načítání velkého množství dat z databáze Oracle. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (zadejte pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Nastavení Navržené                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.          | **Možnost oddílu**: fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. |
| Úplné načtení z velké tabulky bez fyzických oddílů spolu se sloupcem typu Integer pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec primárního klíče. |
| Pomocí vlastního dotazu s fyzickými oddíly načtěte velké množství dat. | **Možnost oddílu**: fyzické oddíly tabulky.<br>**Dotaz**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Název oddílu**: Zadejte názvy oddílů, ze kterých se mají kopírovat data. Pokud není zadaný, Data Factory automaticky detekuje fyzické oddíly v tabulce, kterou jste zadali v datové sadě Oracle.<br><br>Během provádění Data Factory nahradí `?AdfTabularPartitionName` skutečným názvem oddílu a odesílá je do Oracle. |
| Načtěte velké množství dat pomocí vlastního dotazu bez fyzických oddílů a zároveň se sloupcem typu Integer pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní hranice oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílů, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` skutečný název sloupce a rozsahy hodnot pro každý oddíl a pošle je do Oracle. <br>Pokud je například sloupec oddílu "ID" nastaven s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

**Příklad: dotazování pomocí fyzického oddílu**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapování datových typů pro Oracle

Při kopírování dat z a do Oracle platí následující mapování. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Oracle | Data Factory dočasné datový typ |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(podporuje se jenom v Oracle 10g a vyšších verzích) |
| CHAR |Řetězec |
| CLOB |Řetězec |
| DATE (Datum) |Datum a čas |
| FLOAT |Decimal, String (Pokud přesnost > 28) |
| INTEGER |Decimal, String (Pokud přesnost > 28) |
| DLOUHOU |Řetězec |
| LONG RAW |Byte[] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| NUMBER |Decimal, String (Pokud přesnost > 28) |
| NVARCHAR2 |Řetězec |
| ZÍSKÁNÍ |Byte[] |
| ROWID |Řetězec |
| TIMESTAMP |Datum a čas |
| TIMESTAMP WITH LOCAL TIME ZONE |Řetězec |
| TIMESTAMP WITH TIME ZONE |Řetězec |
| UNSIGNED INTEGER |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datový typ INTERVAL v roce do měsíce a druhý den v sekundách se nepodporuje.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
