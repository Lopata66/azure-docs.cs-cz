---
title: Kopírování dat z zdroje HTTP pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze zdroje HTTP-místních i cloudových úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: f25b0f2c7b5e3148bae778c4b50a3f0bd0c148da
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875947"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopírování dat z koncového bodu HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-http-connector.md)
> * [Aktuální verze](connector-http.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z koncového bodu HTTP. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

Rozdíly mezi tento konektor HTTP [REST konektor](connector-rest.md) a [webový tabulky konektor](connector-web-table.md) jsou:

- **Konektor REST** konkrétně podpora kopírování dat z rozhraní RESTful API; 
- **Konektor HTTP** je obecný k načtení dat z jakékoli koncového bodu HTTP, třeba ke stažení souboru. Než konektor REST bude k dispozici, může dojít k používání konektoru HTTP ke zkopírování dat z rozhraní RESTful API, které je podporované, ale méně funkční porovnání konektor REST.
- **Webový konektor tabulky** výpisy tabulky obsah webové stránce HTML.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z HTTP kódu do jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Můžete použít tento konektor HTTP pro:

- Načtení dat z koncového bodu HTTP/S s využitím HTTP **získat** nebo **příspěvek** metody.
- Načtení dat pomocí jedné z následujících ověření: **Anonymní**, **základní**, **Digest**, **Windows**, nebo **ClientCertificate**.
- Zkopírovat odpověď HTTP jako-je a analyzovat pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> K testování požadavku HTTP pro načtení dat, než nakonfigurujete konektor HTTP ve službě Data Factory, přečtěte si o specifikace rozhraní API pro záhlaví a text požadavky. Nástroje, jako je Postman nebo ve webovém prohlížeči můžete použít k ověření.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor HTTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro HTTP propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **HttpServer**. | Ano |
| url | Základní adresa URL webového serveru. | Ano |
| enableServerCertificateValidation | Určete, zda povolit ověření certifikátu serveru SSL při připojování k koncový bod HTTP. Pokud váš server HTTPS používá certifikát podepsaný svým držitelem, nastavte tuto vlastnost na **false**. | Ne<br /> (výchozí hodnota je **true**) |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou **anonymní**, **základní**, **Digest**, **Windows**, a **ClientCertificate**. <br><br> Najdete v částech uvedené pod touto tabulkou další vlastnosti a ukázky JSON pro typy ověřování. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace v Azure nebo v místním prostředí Integration Runtime. Pokud není zadán, tuto vlastnost používá výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="using-basic-digest-or-windows-authentication"></a>Používá ověřování Basic, Digest nebo Windows

Nastavte **authenticationType** vlastnost **základní**, **Digest**, nebo **Windows**. Kromě generické vlastnosti, které jsou popsány v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatelské jméno pro použití pro přístup ke koncovému bodu HTTP. | Ano |
| password | Heslo pro uživatele ( **uživatelské jméno** hodnota). Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>Pomocí ověřování ClientCertificate

Chcete-li použít ClientCertificate ověřování, nastavte **authenticationType** vlastnost **ClientCertificate**. Kromě generické vlastnosti, které jsou popsány v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| embeddedCertData | Data certifikátu s kódováním base64. | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| certThumbprint | Kryptografický otisk certifikátu, který je nainstalován v úložišti certifikátů v místním prostředí Integration Runtime v počítači. Platí, pouze když typ v místním prostředí Integration runtime je podle **connectVia** vlastnost. | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| password | Heslo, který je spojen s certifikátem. Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

Pokud používáte **certThumbprint** pro ověřování a certifikát, který je nainstalován v osobním úložišti místního počítače, udělit oprávnění ke čtení v místním prostředí Integration Runtime:

1. Otevřete konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in, zaměřuje **místního počítače**.
2. Rozbalte **certifikáty** > **osobní**a pak vyberte **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a pak vyberte **všechny úkoly** > **spravovat soukromé klíče**.
3. Na **zabezpečení** kartu, přidejte uživatelský účet, pod kterým běží hostitelská služba Integration Runtime (DIAHostService) s přístupem pro čtení k certifikátu.

**Příklad 1: Pomocí certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Pomocí embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

- Pro **Parquet a formátu odděleného textu**, odkazovat na [datovou sadu formátu Parquet a text s oddělovači](#parquet-and-delimited-text-format-dataset) oddílu.
- Pro ostatní formáty, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiné datové sady formátu](#other-format-dataset) oddílu.

### <a name="parquet-and-delimited-text-format-dataset"></a>Datová sada formátu parquet a text s oddělovači

Ke zkopírování dat z protokolu HTTP v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek na datové sadě založené na formátu a podporované nastavení. Následující vlastnosti jsou podporovány pro protokolu HTTP v rámci `location` nastavení v datové sadě založené na formátu:

| Vlastnost    | Popis                                                  | Požaduje se |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Vlastnost type v rámci `location` v datové sadě musí být nastaveno na **HttpServerLocation**. | Ano      |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data.       | Ne       |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je přibližně 500 KB. Velikost datové části, které chcete předat do vašeho koncového bodu webové je větší než 500 KB, vezměte v úvahu dávkování datové části menších částech.

> [!NOTE]
> **HttpFile** typ datové sady uvedené v další části formát Parquet/textu je stále podporovány jako-je pro aktivitu kopírování a vyhledávání z důvodu zpětné kompatibility. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Další datové sady formátu

Ke zkopírování dat z protokolu HTTP v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **HttpFile**. | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když tato vlastnost neurčí, použije se pouze adresu URL, která je zadána v definici propojené služby. | Ne |
| requestMethod | Metoda HTTP Povolené hodnoty jsou **získat** (výchozí) a **příspěvek**. | Ne |
| additionalHeaders | Další hlavičky požadavků HTTP. | Ne |
| Includesearchresults: true | Obsah žádosti protokolu HTTP. | Ne |
| formát | Pokud chcete načíst data z koncového bodu HTTP jako-je bez analýzy a zkopírujte data do úložiště založené na souborech, přeskočte **formátu** části v definici vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat obsah odpovědi HTTP při kopírování, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, and **ParquetFormat**. V části **formátu**, nastavte **typ** vlastnost na jednu z těchto hodnot. Další informace najdete v tématu [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format), [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formát Orc](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Podporované typy: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Podporované úrovně:  **Optimální** a **nejrychlejší**. |Ne |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je přibližně 500 KB. Velikost datové části, které chcete předat do vašeho koncového bodu webové je větší než 500 KB, vezměte v úvahu dávkování datové části menších částech.

**Příklad 1: Pomocí metody Get (výchozí)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Příklad 2: Pomocí metody Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností, které podporuje zdroje HTTP.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako zdroj

- Pro kopírování z **Parquet a formátu odděleného textu**, odkazovat na [Parquet a zdroj obsahující text oddělený znaky formátu](#parquet-and-delimited-text-format-source) oddílu.
- Pro kopírování z dalších formátech, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiný formát zdroj](#other-format-source) oddílu.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet a zdroj formátu odděleného textu

Ke zkopírování dat z protokolu HTTP v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek věnovaný tomu zdroj aktivity kopírování založená na formát a podporovaná nastavení. Následující vlastnosti jsou podporovány pro protokolu HTTP v rámci `storeSettings` nastavení zdroje kopírování založená na formát:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type v rámci `storeSettings` musí být nastaveno na **HttpReadSetting**. | Ano      |
| requestMethod            | Metoda HTTP <br>Povolené hodnoty jsou **získat** (výchozí) a **příspěvek**. | Ne       |
| addtionalHeaders         | Další hlavičky požadavků HTTP.                             | Ne       |
| Includesearchresults: true              | Obsah žádosti protokolu HTTP.                               | Ne       |
| RequestTimeout           | Časový limit ( **TimeSpan** hodnotu) pro požadavek HTTP získat odpověď. Tato hodnota je časový limit získat odpověď, nevypršel časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**. | Ne       |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti storage současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne       |

> [!NOTE]
> Pro formátu Parquet nebo odděleného textu **HttpSource** zdroj aktivity kopírování typů uvedených v následující části je stále podporovány jako-je z důvodu zpětné kompatibility. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSetting",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Jiný zdroj formátu

Ke zkopírování dat z protokolu HTTP v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **HttpSource**. | Ano |
| httpRequestTimeout | Časový limit ( **TimeSpan** hodnotu) pro požadavek HTTP získat odpověď. Tato hodnota je časový limit získat odpověď, nevypršel časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | Ne |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
