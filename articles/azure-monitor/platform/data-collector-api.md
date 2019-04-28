---
title: Rozhraní API kolekce dat HTTP Azure Monitor | Dokumentace Microsoftu
description: Azure Monitor HTTP rozhraní API kolekce dat slouží k přidání dat POST JSON do pracovního prostoru Log Analytics z libovolného klienta, která může volat rozhraní REST API. Tento článek popisuje, jak použít rozhraní API a obsahuje příklady toho, jak publikovat data pomocí různých programovacích jazycích.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: bwren
ms.openlocfilehash: 9fd65dc0a6d2a5756acd2de7cb46fbf7943a8758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931755"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Odeslat data protokolu pro monitorování Azure pomocí rozhraní API kolekce dat HTTP (public preview)
V tomto článku se dozvíte, jak používat rozhraní API kolekce dat HTTP k odeslání dat protokolů do Azure monitoru z klienta REST API.  Popisuje jak formátovat data shromážděná z vašich skriptů nebo aplikací, zahrnout do požadavku a jste tento požadavek na autorizaci pomocí Azure monitoru.  Příklady jsou k dispozici pro prostředí PowerShell, C# a Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Azure Monitor HTTP rozhraní API kolekce dat je ve verzi public preview.

## <a name="concepts"></a>Koncepty
Rozhraní API kolekce dat HTTP slouží k odesílání dat protokolu do pracovního prostoru Log Analytics ve službě Azure Monitor z libovolného klienta, která může volat rozhraní REST API.  To může být sady runbook ve službě Azure Automation, který shromažďuje správu dat z Azure nebo jiného cloudu nebo ji může být systém alternativní správy, který používá Azure monitoru ke konsolidaci a analyzuje data protokolů.

Všechna data v pracovním prostoru Log Analytics se ukládá jako záznam s konkrétní typ záznamu.  Formátování dat k odeslání do rozhraní API kolekce dat HTTP jako více záznamů ve formátu JSON.  Při odesílání dat vrátí jednotlivý záznam se vytvoří v úložišti pro každý záznam v datové části požadavku.


![Přehled kolekce dat HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Vytvořit žádost
Pokud chcete používat rozhraní API kolekce dat HTTP, můžete vytvořit požadavek POST, který obsahuje data k odeslání v zápisu JSON (JavaScript Object).  V následujících třech tabulkách jsou uvedeny atributy, které jsou požadovány pro každý požadavek. Popisujeme podrobněji dále v tomto článku každý atribut.

### <a name="request-uri"></a>Identifikátor URI žádosti
| Atribut | Vlastnost |
|:--- |:--- |
| Metoda |POST |
| Identifikátor URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ obsahu |application/json |

### <a name="request-uri-parameters"></a>Parametry identifikátoru URI žádosti
| Parametr | Popis |
|:--- |:--- |
| ID zákazníka |Jedinečný identifikátor pro pracovní prostor Log Analytics. |
| Prostředek |Název prostředku rozhraní API: / api/logs. |
| Verze rozhraní API |Verze rozhraní API pro použití s touto žádostí. V současné době je 2016-04-01. |

### <a name="request-headers"></a>Hlavičky požadavku
| Hlavička | Popis |
|:--- |:--- |
| Autorizace |Ověření podpisu. Později v tomto článku najdete informace o tom, jak vytvořit hlavičku HMAC SHA256. |
| Log-Type |Zadejte typ záznamu dat, která se právě odesílá. Omezení velikosti pro tento parametr je 100 znaků. |
| x-ms-date |Datum zpracování žádosti, ve formátu RFC 1123. |
| x-ms-AzureResourceId | ID prostředku Azure prostředku data měla být přidružená. Tím vyplníte [_ResourceId](log-standard-properties.md#_resourceid) vlastnost a umožňuje data, která mají být součástí [zaměřené na prostředek](manage-access.md#access-modes) dotazy. Pokud toto pole není zadán, data nebudou zahrnuty v dotazech zaměřené na prostředek. |
| čas vygenerované pole | Název pole v datech, která obsahuje časové razítko datová položka. Pokud určíte pole, pak jeho obsah se používají pro **TimeGenerated**. Pokud toto pole není zadán, výchozí hodnota pro **TimeGenerated** je čas, který se ingestuje zprávy. Obsah pole zprávy postupujte podle ISO 8601 formátu RRRR-MM-: ssZ. |

## <a name="authorization"></a>Autorizace
Jakákoli žádost Azure Monitor HTTP rozhraní API kolekce dat musí obsahovat hlavičku autorizace. Pro žádost o ověření, musíte podepsat žádost s primární nebo sekundární klíč pro pracovní prostor, který provádí požadavek. Předejte tento podpis jako součást požadavku.   

Tady je formát pro autorizační hlavičky:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*ID pracovního prostoru* je jedinečný identifikátor pro pracovní prostor Log Analytics. *Podpis* je [Hash-based Message Authentication kód metoda HMAC ()](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , který je vytvořen z požadavku a pak je vypočítán s použitím [algoritmus SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Potom můžete zakódovat je pomocí kódování Base64.

Použijte tento formát určený ke kódování **SharedKey** podpis řetězce:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Tady je příklad podpis řetězce:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Máte-li řetězec podpis, zakódovat je pomocí algoritmus HMAC SHA256 na řetězec kódování UTF-8 a potom kódování výsledek jako Base64. Použijte tento formát:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Ukázky v následujících částech obsahovat vzorový kód vám pomůže vytvořit autorizační hlavičky.

## <a name="request-body"></a>Text požadavku
Text zprávy musí být ve formátu JSON. Musí obsahovat jeden nebo více záznamů pomocí dvojice název a hodnotu vlastnosti v tomto formátu:

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Pomocí následujícího formátu může hromadně společně v jedné žádosti více záznamů. Stejný typ záznamu musí být všechny záznamy.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Typ záznamu a vlastnosti
Při odesílání dat prostřednictvím rozhraní Azure Monitor HTTP API kolekce dat, definovat vlastní typ záznamu. V současné době nelze zapisovat data do existující typy záznamů, které byly vytvořeny v jiných datových typů a řešení. Azure Monitor přečte příchozích dat a pak vytvoří vlastnosti, které odpovídají datové typy hodnot, které zadáte.

Musí zahrnovat každého požadavku rozhraní API kolekce dat **typ protokolu** záhlaví s názvem pro příslušný typ záznamu. Přípona **_CL** se automaticky připojí k názvu zadáte, aby se odlišil od ostatních typů protokolu jako vlastní protokol. Například, pokud zadáte název **MyNewRecordType**, Azure Monitor vytvoří záznam s typem **MyNewRecordType_CL**. To pomáhá zajistit, že neexistují žádné konflikty mezi názvy typů vytvořené uživatelem a poskytuje současný nebo budoucí řešení Microsoftu.

Azure Monitor k identifikaci typ dat vlastnosti, přidá příponu název vlastnosti. Pokud vlastnost obsahuje hodnotu null, vlastnost není zahrnutý v daném záznamu. Tato tabulka uvádí typ dat vlastnosti a odpovídající přípony:

| Typ dat vlastnosti | Přípona |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Datum a čas |_t |
| GUID |_g |

Datový typ, který používá Azure Monitor pro každou vlastnost závisí na tom, zda již existuje typ záznamu pro nový záznam.

* Pokud typ záznamu neexistuje, vytvoří Azure Monitor novou zásadu pomocí odvození typu JSON určit typ dat pro každou vlastnost u nového záznamu.
* Typ záznamu neexistuje, Azure Monitor se pokusí vytvořit nový záznam na základě existující vlastností. Pokud datový typ pro vlastnost v novém záznamu zadané informace neodpovídají a nelze jej převést na stávající typ, nebo pokud tento záznam obsahuje vlastnost, která neexistuje, vytvoří Azure Monitor novou vlastnost, která má odpovídající příponu.

Například by tato položka odeslání vytvořit záznam s tři vlastnosti **number_d**, **boolean_b**, a **string_s**:

![Ukázka záznamu 1](media/data-collector-api/record-01.png)

Pokud pak odešle tento další položce s všechny hodnoty ve formátu jako řetězce, nebude změna vlastnosti. Tyto hodnoty lze převést na existující datové typy:

![Ukázka záznamu 2](media/data-collector-api/record-02.png)

Ale pokud jste provedli poté toto další odeslání, Azure Monitor byste vytvořit nové vlastnosti **boolean_d** a **string_d**. Nelze převést tyto hodnoty:

![Ukázka záznamu 3](media/data-collector-api/record-03.png)

Pokud potom odeslali následující položku předtím, než byl vytvořen typ záznamu, monitorování Azure by u tři vlastnosti, vytvořit záznam **úspěch**, **boolean_s**, a **string_s**. Na tuto položku se všechny počáteční hodnoty naformátovaná jako řetězec:

![Ukázka záznamu 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Rezervované vlastnosti
Následující vlastnosti jsou vyhrazené a nesmí být použita v vlastní typ záznamu. Pokud vaše datová část obsahuje některé z těchto názvů vlastností, dojde k chybě.

- tenant

## <a name="data-limits"></a>Omezení dat
Existují některá omezení kolem data vystavená pro rozhraní API pro shromažďování dat monitorování Azure.

* Maximálně 30 MB na příspěvek k rozhraní API kolekce dat monitorování Azure. Toto je omezení velikosti pro jeden příspěvek. Pokud se data z jedné příspěvku, který překračuje 30 MB, měli rozdělit data do menších bloků velikosti dat a odešlete je současně.
* Maximální limit 32 KB pro hodnoty pole. Pokud hodnota pole je větší než 32 KB, data se zkrátí.
* Doporučený maximální počet polí pro daný typ je 50. To je praktické omezení použitelnosti a perspektivy vyhledávací prostředí.  
* Tabulky v pracovním prostoru Log Analytics podporuje jenom až 500 sloupce (označované jako pole v tomto článku). 
* Maximální počet znaků pro název sloupce je 500.

## <a name="return-codes"></a>Návratové kódy
Stavový kód HTTP 200 znamená, že žádost byla přijata ke zpracování. To znamená, že operace byla úspěšně dokončena.

Tato tabulka uvádí kompletní sadu stavové kódy, které může vrátit služby:

| Kód | Status | Kód chyby | Popis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Požadavek byl úspěšně přijat. |
| 400 |Nesprávná žádost |InactiveCustomer |Pracovní prostor byl uzavřen. |
| 400 |Nesprávná žádost |InvalidApiVersion |Služba nebyla rozpoznána verze rozhraní API, který jste zadali. |
| 400 |Nesprávná žádost |InvalidCustomerId |Zadané ID pracovního prostoru je neplatný. |
| 400 |Nesprávná žádost |InvalidDataFormat |Neplatný identifikátor JSON se odeslal. Text odpovědi může obsahovat další informace o tom, jak tuto chybu napravíme. |
| 400 |Nesprávná žádost |InvalidLogType |Zadaný typ protokolu omezením speciální znaky nebo číslice. |
| 400 |Nesprávná žádost |MissingApiVersion |Nebyla zadaná verze rozhraní API. |
| 400 |Nesprávná žádost |MissingContentType |Nezadal se typ obsahu. |
| 400 |Nesprávná žádost |MissingLogType |Typ protokolu požadovaná hodnota nebyla zadána. |
| 400 |Nesprávná žádost |UnsupportedContentType |Typ obsahu nebyl nastaven na **application/json**. |
| 403 |Zakázáno |InvalidAuthorization |Službu se nepovedlo ověřit žádost. Ověřte, že jsou platné ID a připojení klíče pracovního prostoru. |
| 404 |Nenalezené | | Zadaná adresa URL je nesprávná nebo požadavku je moc velká. |
| 429 |Příliš mnoho žádostí | | Služba dochází k velkému počtu data z vašeho účtu. Zkuste prosím požadavek později. |
| 500 |Vnitřní chyba serveru |UnspecifiedError |U této služby došlo k vnitřní chybě. Zkuste prosím požadavek. |
| 503 |Služba není dostupná |ServiceUnavailable |Služba je momentálně nedostupný a nepřijímá žádosti. Zkuste to prosím znovu, vaši žádost. |

## <a name="query-data"></a>Dotazování dat
Zadat dotaz na data odeslaná Azure monitoru HTTP rozhraní API kolekce dat, hledat záznamy s **typ** , který je roven **LogType** hodnotu, která jste zadali, s příponou **_CL**. Například, pokud jste použili **MyCustomLog**, pak by vrátí všechny záznamy s `MyCustomLog_CL`.

## <a name="sample-requests"></a>Požadavky na ukázky
V následujících částech najdete ukázky toho, jak odesílat data Azure monitoru HTTP rozhraní API kolekce dat pomocí různých programovacích jazycích.

Pro každý vzorek proveďte tyto kroky k nastavení proměnných pro autorizační hlavičky:

1. Na webu Azure Portal vyhledejte pracovního prostoru Log Analytics.
2. Vyberte **upřesňující nastavení** a potom **připojené zdroje**.
2. Napravo od **ID pracovního prostoru**, vyberte ikonu kopírování a vložte ID jako hodnotu **ID zákazníka** proměnné.
3. Napravo od **primární klíč**, vyberte ikonu kopírování a vložte ID jako hodnotu **sdíleného klíče** proměnné.

Alternativně můžete změnit proměnné pro typ protokolu a dat JSON.

### <a name="powershell-sample"></a>Ukázka PowerShellu
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Ukázka v jazyce C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Ukázka Python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Možnosti a důležité informace
Rozhraní API kolekce dat by měl zahrnovat většinu, které potřebujete ke shromažďování dat volného tvaru do protokolů Azure, existuje instance, kde je alternativou může být nutné překonávat některá omezení rozhraní API. Vaše možnosti jsou následujícím způsobem hlavní aspekty zahrnuté:

| Alternativní | Popis | Vyhovuje |
|---|---|---|
| [Vlastní události](https://docs.microsoft.com/en-us/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Nativní ingestování založené na sadě SDK ve službě Application Insights | Application Insights, obvykle instrumentována prostřednictvím sady SDK v rámci vaší aplikace, nabízí možnost odesílat vlastní data prostřednictvím vlastních událostí. | <ul><li> Data, která je generována v rámci vaší aplikace, ale není vyzvednou SDK prostřednictvím jednoho z výchozí datové typy (ie: požadavky, závislosti, výjimky, atd.).</li><li> Data, která jsou nejčastěji korelována dalších dat aplikací ve službě Application Insights </li></ul> |
| [Rozhraní API kolekce dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) v protokoly Azure monitoru | Rozhraní API kolekce dat v protokolech Azure Monitor je zcela otevřený způsob, jak ingestovat data. Zde nelze odesílat žádná data formátovaná v objektu JSON. Po odeslání se zpracuje a k dispozici v protokolech se korelují s další data v protokolech nebo proti jiné Application Insights data. <br/><br/> To je poměrně snadné nahrát data jako soubory do objektu blob Azure Blob z kde se tyto soubory zpracovat a nahrát do služby Log Analytics. Podrobnosti najdete na [to](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) článek ukázku implementace takových kanálu. | <ul><li> Data, která není nutně vygenerované v rámci aplikace instrumentovány v rámci Application Insights.</li><li> Příklady vyhledávání a skutečnosti, tabulky, referenční data, předem agregovaných statistik, atd. </li><li> Určená pro data, která bude křížovými odkazy na jiná data monitorování Azure (například Application Insights, jiné protokoly datové typy, Security Center, monitorování Azure pro kontejnery nebo virtuální počítače a tak dál). </li></ul> |
| [Průzkumník dat Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Průzkumník Azure dat (ADX) je datová platforma, která je základem Application Insights Analytics a monitorování protokolů Azure. Nyní obecně dostupná ("GA"), platformě data v nezpracované podobě poskytuje úplnou flexibilitu (ale vyžadující režii správy) v clusteru (RBAC, míra uchování, schéma, atd.). ADX poskytuje mnoho [možnosti ingestování](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) včetně [CSV, TSV a JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) soubory. | <ul><li> Data, která nebude korelují s jinými daty v rámci Application Insights nebo protokoly. </li><li> Data vyžadující advanced ingestování nebo zpracování funkce není ještě dnes k dispozici v protokolech monitorování Azure. </li></ul> |


## <a name="next-steps"></a>Další postup
- Použití [rozhraní API pro vyhledávání protokolu](../log-query/log-query-overview.md) k načtení dat z pracovního prostoru Log Analytics.

- Další informace o tom [vytvoření datového kanálu pomocí rozhraní API kolekce dat](create-pipeline-datacollector-api.md) pomocí pracovního postupu Logic Apps do Azure monitoru.
