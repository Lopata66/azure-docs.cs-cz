---
title: Metriky Azure Storage ve službě Azure Monitor | Dokumentace Microsoftu
description: Další informace o nové metriky ze služby Azure Monitor nabízí.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: a5ebd50b3a5fe3b611bae28db98979eee40f9490
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899022"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metriky Azure Storage na platformě Azure Monitor

S metrikami ve službě Azure Storage můžete analyzovat trendy využití, sledování požadavků a diagnostikovat problémy s vaším účtem úložiště.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Monitor odesláním dat metriky na platformě Azure Monitor se integruje se služby Azure Storage.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Dostanete z [webu Azure portal](https://portal.azure.com), rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu jako jsou Event Hubs. Další informace najdete v tématu [metrik Azure monitoru](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Ve výchozím nastavení jsou povolené metriky a můžou k poslední data 93 dnů. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../../azure-monitor/platform/diagnostic-logs-overview.md) ve službě Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Přístup metrik na webu Azure Portal

Metriky můžete sledovat v čase na webu Azure Portal. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![snímek obrazovky s přístupem k metrik na webu Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Metrika podporuje dimenze můžete filtrovat metriky s hodnotou požadované dimenzí. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu na konkrétní operace tak, že vyberete hodnoty **název rozhraní API** dimenze.

![snímek obrazovky s přístupem k metriky s dimenzí na webu Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Metriky přístup pomocí rozhraní REST API

Platforma Azure Monitor poskytuje [rozhraní REST API](/rest/api/monitor/) číst definice metriky a hodnoty. Tato část ukazuje, jak číst metriky pro úložiště. ID prostředku se používá v všechna rozhraní REST API. Další informace přečtěte si vysvětlení ID prostředku pro služby ve službě Storage.

Následující příklad ukazuje, jak používat [ArmClient](https://github.com/projectkudu/ARMClient) příkazového řádku pro zjednodušení testování pomocí rozhraní REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Seznam účtů úrovně definice metriky pomocí rozhraní REST API

Následující příklad ukazuje, jak zobrazit seznam definice metriky na úrovni účtu:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Pokud chcete vypsat definice metriky pro objekt blob, tabulka, soubor nebo fronty, je nutné zadat jiné ID prostředků pro každou službu s rozhraním API.

Odpověď obsahuje definice metriky ve formátu JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Čtení hodnoty metrik úroveň účtu pomocí rozhraní REST API

Následující příklad ukazuje, jak načíst data metriky na úrovni účtu:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Ve výše příklad, pokud si chcete přečíst hodnoty metrik pro objekt blob, tabulka, soubor nebo fronty, musíte zadat ID různých prostředků pro každou službu s rozhraním API.

Následující odpověď obsahuje hodnoty metrik ve formátu JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Přístup metriky pomocí sady .NET SDK

Platforma Azure Monitor poskytuje [sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) číst definice metriky a hodnoty. [Ukázkový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak používat sadu SDK s různými parametry. Je třeba použít `0.18.0-preview` nebo novější verzi pro metrik úložiště. ID prostředku se používá v sadě .NET SDK. Další informace přečtěte si vysvětlení ID prostředku pro služby ve službě Storage.

Následující příklad ukazuje, jak čtení metrik storage pomocí .NET SDK služby Azure Monitor.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Seznam účtů úrovně definice metriky pomocí sady .NET SDK

Následující příklad ukazuje, jak zobrazit seznam definice metriky na úrovni účtu:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Pokud chcete vypsat definice metriky pro objekt blob, tabulka, soubor nebo fronty, je nutné zadat jiné ID prostředků pro každou službu s rozhraním API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Hodnoty metrik pro čtení pomocí sady .NET SDK

Následující příklad znázorňuje způsob čtení `UsedCapacity` data na úrovni účtu:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Ve výše příklad, pokud si chcete přečíst hodnoty metrik pro objekt blob, tabulka, soubor nebo fronty, musíte zadat ID různých prostředků pro každou službu s rozhraním API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Čtení hodnoty vícerozměrné metriky pomocí sady .NET SDK

U vícerozměrných metrik budete muset definovat meta filtr dat, pokud si chcete přečíst data metriky na konkrétní hodnotu dimenze.

Následující příklad ukazuje, jak načíst data metriky na metriky, podpora více dimenzí:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Vysvětlení ID prostředku pro služby ve službě Azure Storage

ID prostředku je jedinečný identifikátor prostředku v Azure. Při použití rozhraní REST API pro monitorování Azure ke čtení definic metrik nebo hodnoty, je nutné použít ID prostředku pro prostředek, na kterém chcete pracovat. ID šablony prostředků následující formát:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Storage poskytuje metriky na úrovni účtu úložiště a úroveň služeb s Azure Monitor. Například můžete načíst metriky pro pouze úložiště objektů Blob. Každá úroveň má svou vlastní ID prostředku, který slouží k načtení metriky pro právě tuto úroveň.

### <a name="resource-id-for-a-storage-account"></a>ID prostředku účtu úložiště

Následuje ukázka formátu pro určení ID prostředku účtu úložiště.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID prostředku pro služby storage

Následuje ukázka formátu pro určení ID prostředku pro každou ze služeb úložiště.

* ID prostředku služby objektů BLOB `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID prostředku služby Table `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID prostředku služby Queue `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID prostředku služby souborů `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID prostředku ve službě Azure Monitor, rozhraní REST API

Následuje vzor použitý při volání REST API služby Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Kapacitní metriky
Hodnoty metrik kapacity se posílají do Azure monitoru každou hodinu. Hodnoty se aktualizují každý den. Agregační interval definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporovaný agregační interval pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje následující metriky kapacity ve službě Azure Monitor.

### <a name="account-level"></a>Úroveň účtu

| Název metriky | Popis |
| ------------------- | ----------------- |
| UsedCapacity | Velikost úložiště využitá účtem úložiště. Pro účty úložiště úrovně standard je součtem kapacita použitá objektů blob, tabulky, souboru a fronty. Pro účty služby premium storage a účty úložiště Blob je stejný jako BlobCapacity. <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| BlobCapacity | Celkové využití úložiště objektů Blob v účtu úložiště. <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 <br/> Dimenze: BlobType ([definice](#metrics-dimensions)) |
| BlobCount    | Počet objektů blob uložených v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 <br/> Dimenze: BlobType ([definice](#metrics-dimensions)) |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| IndexCapacity     | Velikost úložiště využitá službou ADLS Gen2 hierarchické indexu <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="table-storage"></a>Úložiště Table

| Název metriky | Popis |
| ------------------- | ----------------- |
| TableCapacity | Velikost tabulky úložiště používá účet úložiště. <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| TableCount   | Počet tabulek v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| TableEntityCount | Počet entit tabulek v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost fronty úložiště používá účet úložiště. <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| QueueCount   | Počet front v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Počet zpráv fronty nevypršela v účtu úložiště. <br/><br/>Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="file-storage"></a>File Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| FileCapacity | Velikost služby File storage používá účet úložiště. <br/><br/> Jednotka: B <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| FileCount   | Počet souborů v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| FileShareCount | Počet sdílených složek v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

## <a name="transaction-metrics"></a>Metriku transakcí

Ze služby Azure Storage do Azure monitoru jsou emitovány metriku transakcí na každý požadavek na účet úložiště. V případě žádná aktivita v účtu úložiště nebudou žádná data na metriku transakcí v období. Všechny transakce metriky jsou k dispozici na úrovni účtu a služby (úložiště objektů Blob, Table storage, Azure Files a Queue storage). Agregační interval definuje časový interval, který se zobrazí hodnoty metrik. Podporovaný agregační zrna pro všechny metriky transakce jsou PT1H a PT1M.

Azure Storage poskytuje následující metriku transakcí ve službě Azure Monitor.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Příslušné dimenze: Hodnota ResponseType, GeoType, ApiName a ověřování ([definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotka: B <br/> Typ agregace: Celkem <br/> Příslušné dimenze: GeoType ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos | Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotka: B <br/> Typ agregace: Celkem <br/> Příslušné dimenze: GeoType ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotka: Milisekund <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. <br/><br/> Jednotka: Milisekund <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota pro celkový počet fakturovatelných požadavků vydělí počtem příslušných požadavků, včetně požadavků došlo k neočekávané chybě. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. <br/><br/> Jednotka: Procento <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99.99 |

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Storage podporuje následující dimenze pro metriky ve službě Azure Monitor.

| Název dimenze | Popis |
| ------------------- | ----------------- |
| BlobType | Typ objektu blob pro pouze metriky objektů Blob. Podporované hodnoty jsou **BlockBlob** a **PageBlob**. Doplňovací objekt Blob je součástí BlockBlob. |
| Hodnota ResponseType | Typ odpovědi transakce. Dostupné hodnoty zahrnují: <br/><br/> <li>ServerOtherError: Všechny ostatní chyby na straně serveru kromě zde popsaných </li> <li> ServerBusyError: Ověřená žádost, která vrátila stavový kód HTTP 503. </li> <li> ServerTimeoutError: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Časový limit vypršel kvůli chybě serveru. </li> <li> AuthorizationError: Ověřená žádost, která selhala kvůli neoprávněnému přístupu k datům nebo chybě autorizace. </li> <li> NetworkError: Ověřená žádost, která selhala kvůli chybě sítě. K tomu nejčastěji dochází, když klient předčasně ukončí spojení před vypršením časového limitu. </li> <li>    ClientThrottlingError: Chyba omezování využití sítě na straně klienta. </li> <li> ClientTimeoutError: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Pokud je časový limit sítě klienta nebo časový limit žádosti nastavený na hodnotu nižší, než služba úložiště očekávala, jde o očekávané vypršení časového limitu. V opačném případě bude ohlášeno jako ServerTimeoutError. </li> <li> ClientOtherError: Všechny ostatní chyby na straně klienta kromě zde popsaných. </li> <li> Úspěch: Úspěšná žádost|
| GeoType | Transakce z primární nebo sekundární clusteru. Dostupné hodnoty zahrnují primární a sekundární. To platí pro oprávnění ke čtení geograficky redundantní Storage(RA-GRS) při čtení objektů ze sekundární tenanta. |
| ApiName | Název operace. Příklad: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Všechny názvy operace, najdete v části [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| Authentication | Typ ověřování používaný v transakcích. Dostupné hodnoty zahrnují: <br/> <li>AccountKey: Transakce se ověřuje pomocí klíče účtu úložiště.</li> <li>SAS: Transakce se ověřuje pomocí signatur sdíleného přístupu.</li> <li>OAuth: Transakce se ověřuje pomocí přístupových tokenů OAuth.</li> <li>Anonymní: Transakce je požadováno anonymně. Neměl by zahrnovat předběžných požadavků.</li> <li>AnonymousPreflight: Transakce je předběžný požadavek.</li> |

Pro podpůrné dimenze metriky je třeba zadat hodnotu dimenze zobrazíte odpovídající hodnoty metriky. Například, pokud se podíváte na **transakce** hodnotu pro odpovědi – úspěch, je potřeba vyfiltrovat **hodnotu ResponseType** dimenze s **úspěch**. Nebo pokud se podíváte na **BlobCount** hodnotu pro objekt Blob bloku, budete potřebovat k filtrování **BlobType** dimenze s **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Provozní kontinuity služby starší verze metrik

Starší verze metriky jsou k dispozici paralelně s metrikami Azure monitoru spravované. Podpora udržuje stejné, až skončí služby starší verze metrik Azure Storage.

## <a name="faq"></a>Nejčastější dotazy

**Podporuje nové metriky klasický účet úložiště?**

Ne, nové metriky v účtech úložiště Azure Resource Manageru jedinou podpory Azure Monitor. Pokud chcete použít nastavení u účtů úložiště, které potřebujete migrovat na účet úložiště Azure Resource Manageru. Zobrazit [migrovat na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Azure Storage podporuje metriky pro Managed Disks a nespravovaných disků?**

Ne, Azure Compute podporuje metriky na discích. Zobrazit [článku](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) další podrobnosti.

**Jak namapovat a klasické metriky o nové metriky migrovat?**

Můžete najít podrobné mapování mezi klasické metriky a nové metriky v [migrace metrik Azure Storage](./storage-metrics-migration.md).

## <a name="next-steps"></a>Další postup

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
