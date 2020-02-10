---
title: Aktivita webu v Azure Data Factory
description: Naučte se, jak můžete použít aktivitu webu, jednu z aktivit toku ovládacích prvků, které podporuje Data Factory, k vyvolání koncového bodu REST z kanálu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: c700c9786f3bec4c79cae904a95deb5fd1c670b4
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110014"
---
# <a name="web-activity-in-azure-data-factory"></a>Aktivita webu v Azure Data Factory
Webová aktivita slouží k volání vlastního koncového bodu REST z kanálu služby Data Factory. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup.

> [!NOTE]
> Aktivita webu může volat pouze veřejně vystavené adresy URL. Pro adresy URL, které jsou hostované v privátní virtuální síti, se nepodporuje.

## <a name="syntax"></a>Syntaxe

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity webu | Řetězec | Ano
type | Musí být nastavená na **aktivitu webactivity**. | Řetězec | Ano
method | Metoda rozhraní REST API pro cílový koncový bod | Řetězec. <br/><br/>Podporované typy: "GET", "POST", "PUT" | Ano
url | Cílový koncový bod a cesta | Řetězec (nebo výraz s hodnotou resultType řetězce). Pokud tato aktivita neobdrží odpověď od koncového bodu, bude časový limit 1 minuty s chybou. | Ano
záhlaví | Hlavičky, které se odesílají do žádosti Například pro nastavení jazyka a typu na žádost: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ano, hlavička Content-Type je povinná. `"headers":{ "Content-Type":"application/json"}`
těles | Představuje datovou část, která je odeslána do koncového bodu.  | Řetězec (nebo výraz s hodnotou resultType řetězce). <br/><br/>Podívejte se na schéma datové části požadavku v části [schéma datové části požadavku](#request-payload-schema) . | Vyžadováno pro metody POST/PUT.
ověřování | Metoda ověřování používaná pro volání koncového bodu. Podporované typy jsou "Basic" nebo ClientCertificate ". Další informace najdete v části [ověřování](#authentication) . Pokud není vyžadováno ověření, vylučte tuto vlastnost. | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne
datasets | Seznam datových sad předaných do koncového bodu. | Pole odkazů na datovou sadu Může být prázdné pole. | Ano
linkedServices | Seznam propojených služeb předaných koncovému bodu | Pole odkazů na propojené služby Může být prázdné pole. | Ano

> [!NOTE]
> Koncové body REST, které vyvolává webová aktivita, musí vracet odpověď typu JSON. Pokud tato aktivita neobdrží odpověď od koncového bodu, bude časový limit 1 minuty s chybou.

V následující tabulce jsou uvedeny požadavky na obsah JSON:

| Typ hodnoty | Text požadavku | Text odpovědi |
|---|---|---|
|JSON – objekt | Podporuje se | Podporuje se |
|Pole JSON | Podporuje se <br/>(V současné době pole JSON nefungují v důsledku chyby. Probíhá oprava.) | Nepodporované |
| Hodnota JSON | Podporuje se | Nepodporované |
| Typ jiný než JSON | Nepodporované | Nepodporované |
||||

## <a name="authentication"></a>Ověřování

Níže jsou uvedené podporované typy ověřování v aktivitě webu.

### <a name="none"></a>Žádná

Pokud není vyžadováno ověřování, nezahrnujte vlastnost "ověřování".

### <a name="basic"></a>Basic

Zadejte uživatelské jméno a heslo, které chcete použít se základním ověřováním.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>certifikát klienta

Zadejte obsah souboru PFX a hesla zakódovaného ve formátu base64.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Spravovaná identita

Zadejte identifikátor URI prostředku, pro který bude přístupový token vyžádán pomocí spravované identity pro datovou továrnu. K volání rozhraní API pro správu prostředků Azure použijte `https://management.azure.com/`. Další informace o tom, jak spravované identity fungují, najdete na [stránce s přehledem spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Pokud je vaše Datová továrna nakonfigurovaná s úložištěm Git, musíte přihlašovací údaje uložit v Azure Key Vault pro použití základního nebo ověřování certifikátu klienta. Azure Data Factory neukládá hesla v Gitu.

## <a name="request-payload-schema"></a>Schéma datové části požadavku
Při použití metody POST/PUT představuje vlastnost text datovou část, která je odeslána do koncového bodu. Propojené služby a datové sady můžete předat jako součást datové části. Tady je schéma pro datovou část:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Příklad
V tomto příkladu webová aktivita v kanálu volá koncový bod REST. Předá do koncového bodu propojenou službu Azure SQL a datovou sadu Azure SQL. Koncový bod REST používá připojovací řetězec Azure SQL pro připojení k serveru SQL Azure a vrací název instance SQL serveru.

### <a name="pipeline-definition"></a>Definice kanálu

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Hodnoty parametrů kanálu

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Kód koncového bodu webové služby

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory:

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
