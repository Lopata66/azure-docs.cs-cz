---
title: Porozumět zabezpečení služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – řízení přístupu ke službě IoT Hub pro aplikace pro zařízení a back endové aplikace. Obsahuje informace o tokeny zabezpečení a podpora pro certifikáty X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: bb402a5a059fb6f2836bddbd951220271ca77ba3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400592"
---
# <a name="control-access-to-iot-hub"></a>Řízení přístupu k IoT Hubu

Tento článek popisuje možnosti pro zabezpečení služby IoT hub. IoT Hub používá *oprávnění* udělit přístup ke koncovému bodu každý IoT hub. Oprávnění omezují přístup do služby IoT hub, v závislosti na funkcích.

Tento článek představuje:

* Jiná oprávnění, abyste udělili na zařízení nebo do back endové aplikace pro přístup k službě IoT hub.
* V procesu ověřování a tokenů použije k ověření oprávnění.
* Jak určit obor přihlašovací údaje k omezení přístupu ke konkrétním prostředkům.
* Podpora služby IoT Hub pro certifikáty X.509.
* Zařízení vlastní ověřovací mechanismy, které používají existující registrů identity zařízení nebo schémat ověřování.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musí mít příslušná oprávnění pro přístup k jakémukoli koncové body IoT Hubu. Zařízení musí obsahovat třeba token obsahující zabezpečovací přihlašovací údaje spolu s každou zprávu, kterou odesílá do služby IoT Hub.

## <a name="access-control-and-permissions"></a>Řízení přístupu a oprávnění

Můžete udělit [oprávnění](#iot-hub-permissions) následujícími způsoby:

* **IoT hub úrovně sdílené zásady přístupu**. Zásady sdíleného přístupu udělit libovolnou kombinaci [oprávnění](#iot-hub-permissions). Můžete definovat zásady v [webu Azure portal](https://portal.azure.com), prostřednictvím kódu programu pomocí [IoT Hub prostředek REST API](/rest/api/iothub/iothubresource), nebo pomocí [az iot hub zásad](/cli/azure/iot/hub/policy?view=azure-cli-latest) rozhraní příkazového řádku. Nově vytvořený IoT hub má následující výchozí zásady:
  
  | Zásada sdíleného přístupu | Oprávnění |
  | -------------------- | ----------- |
  | iothubowner | Oprávnění All |
  | service | **ServiceConnect** oprávnění |
  | zařízení | **DeviceConnect** oprávnění |
  | registryRead | **RegistryRead** oprávnění |
  | registryReadWrite | **RegistryRead** a **RegistryWrite** oprávnění |

* **Zabezpečovací přihlašovací údaje zařízení**. Každé centrum IoT obsahuje [registr identit](iot-hub-devguide-identity-registry.md) pro každé zařízení v registru identit můžete nakonfigurovat přihlašovací údaje zabezpečení, které udělují **DeviceConnect** oprávnění omezená na odpovídající zařízení Koncové body.

Například v typického řešení IoT:

* Používá součásti správy zařízení *registryReadWrite* zásad.
* Používá součásti procesoru událostí *služby* zásad.
* Používá součásti zařízení za běhu obchodní logiky *služby* zásad.
* Jednotlivá zařízení připojit pomocí přihlašovací údaje uložené v registru identit služby IoT hub.

> [!NOTE]
> Zobrazit [oprávnění](#iot-hub-permissions) podrobné informace.

## <a name="authentication"></a>Authentication

Azure IoT Hub uděluje přístup ke koncovým bodům pomocí ověření tokenu proti zásady sdíleného přístupu a přihlašovací údaje zabezpečení registru identit.

Zabezpečovací přihlašovací údaje, jako jsou například symetrického klíče, se nikdy neodesílá přenosu.

> [!NOTE]
> Poskytovatel prostředků Azure IoT Hub zabezpečuje svým předplatným Azure, jako jsou všichni poskytovatelé v [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

Další informace o tom, jak vytvořit a použít tokeny zabezpečení najdete v tématu [tokeny zabezpečení IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Podrobnosti protokolu

Každý podporovaný protokol, jako je protokol MQTT, AMQP a protokolu HTTPS, přenáší tokeny různými způsoby.

Pokud používáte protokol MQTT, paket připojit má ID zařízení jako ClientId, `{iothubhostname}/{deviceId}` do pole uživatelské jméno a SAS token do pole heslo. `{iothubhostname}` by měl být úplný záznam CName služby IoT hub (například contoso.azure-devices.net).

Při použití [AMQP](https://www.amqp.org/), IoT Hub podporuje [SASL prostý](https://tools.ietf.org/html/rfc4616) a [AMQP deklarace identity – zabezpečení na základě-](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Pokud používáte AMQP deklarace identity na základě – zabezpečením, standardní Určuje, jak přenést tyto tokeny.

Pro rozhraní SASL prostý **uživatelské jméno** může být:

* `{policyName}@sas.root.{iothubName}` Pokud se používá tokeny úrovni centra IoT.
* `{deviceId}@sas.{iothubname}` Pokud používáte zařízení s rozsahem tokeny.

V obou případech se pole pro heslo obsahuje token, jak je popsáno v [tokeny zabezpečení IoT Hub](iot-hub-devguide-security.md#security-tokens).

HTTPS implementuje zahrnutím platný token v ověřování **autorizace** hlavičky žádosti.

#### <a name="example"></a>Příklad:

Uživatelské jméno (ID zařízení rozlišuje velká a malá písmena): `iothubname.azure-devices.net/DeviceId`

Heslo (můžete vygenerovat token SAS se [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) nástroje, rozšíření příkazu rozhraní příkazového řádku [az iot hub generovat--token sas](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), nebo [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Sad SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generovat tokeny při připojování ke službě. V některých případech se sadami Azure IoT SDK nepodporují všechny protokoly nebo všechny metody ověřování.

### <a name="special-considerations-for-sasl-plain"></a>Zvláštní upozornění pro prostý SASL

Pokud používáte rozhraní SASL prostý AMQP, klient připojení do služby IoT hub můžete použít jeden token pro každé připojení TCP. Když vyprší platnost tokenu, připojení TCP odpojení od služby a aktivuje opětovné připojení. Toto chování, když není problematické pro back endové aplikace, je poškození pro aplikace pro zařízení z následujících důvodů:

* Brány se obvykle připojíte jménem velký počet zařízení. Při použití rozhraní SASL prostý, mají vytvořit různé připojení TCP pro každé zařízení připojení do služby IoT hub. Tento scénář výrazně zvyšuje spotřebu energie a síťové prostředky a zvyšuje latenci každé připojení zařízení.

* Zařízení s omezenými zdroji jsou nepříznivě ovlivněny zvýšené využití prostředků chcete znovu připojit po jednotlivých vypršení platnosti tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Určení oboru přihlašovací údaje na úrovni centra IoT

Zásady zabezpečení na úrovni centra IoT můžete omezit rozsah tak, že vytvoříte tokeny s omezeným identifikátor URI prostředku. Koncový bod pro odesílání zpráv typu zařízení cloud ze zařízení je třeba **/devices/ {deviceId} / zpráv/události**. Můžete taky zásady sdíleného přístupu na úrovni centra IoT s **DeviceConnect** oprávnění k podpisu tokenu je jehož resourceURI **/devices/ {deviceId}**. Tento postup vytvoří token, který lze použít k odesílání zpráv zařízení pouze **deviceId**.

Tento mechanismus je podobný [zásad vydavatele služby Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)a umožňuje implementovat vlastní metody ověřování.

## <a name="security-tokens"></a>Tokeny zabezpečení

IoT Hub používá k ověření zařízení a služeb se odesílání klíče na lince tokeny zabezpečení. Kromě toho mají omezenou dobu platnosti a rozsahu tokenů zabezpečení. [Sady SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generovat tokeny nevyžaduje žádnou zvláštní konfiguraci. Některé scénáře vyžadují vytvoření a použití tokenů zabezpečení přímo. Mezi tyto scénáře patří:

* Přímé použití povrchy MQTT, AMQP nebo HTTPS.

* Implementace vzoru token služby, jak je vysvětleno v [ověřování zařízení vlastní](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub také umožňuje zařízení k ověření pomocí služby IoT Hub pomocí [certifikáty X.509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktura tokenu zabezpečení

Použití tokenů zabezpečení pro udělení časově přístup k zařízení a služeb na určitých funkcí ve službě IoT Hub. Získat autorizaci k připojení ke službě IoT Hub, musíte odeslat zařízení a služby tokenů zabezpečení, které jsou podepsané pomocí sdíleného přístupového nebo symetrický klíč. Tyto klíče jsou uloženy s identitou zařízení v registru identit.

Token podepsán sdíleného přístupového klíče uděluje přístup ke všem funkcím, které jsou přidružené k oprávnění zásad sdíleného přístupu. Token podepsán pomocí identity zařízení symetrického klíče pouze uděluje **DeviceConnect** oprávnění pro identitu přidružená zařízení.

Token zabezpečení má následující formát:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Tady jsou očekávané hodnoty:

| Value | Popis |
| --- | --- |
| {podpis} |Řetězec podpisu HMAC SHA256 formuláře: `{URL-encoded-resourceURI} + "\n" + expiry`. **Důležité**: Klíč je dekódovat z formátu base64 a použít jako klíč pro provádění výpočtů HMAC SHA256. |
| {resourceURI} |Předponu identifikátoru URI (podle segmentů) koncových bodů, které lze přistupovat pomocí tohoto tokenu, počínaje název hostitele služby IoT hub (žádný protokol). Například `myHub.azure-devices.net/devices/device1`. |
| {expiry} |Řetězce UTF8 pro počet sekund od 00:00:00 UTC epocha na 1. ledna 1970. |
| {URL-encoded-resourceURI} |Nižší malá a velká kódování URL z identifikátoru URI prostředku malými písmeny |
| {policyName} |Název zásad sdíleného přístupu, na který odkazuje tento token. Uveden v případě token, který odkazuje na přihlašovací údaje registru zařízení. |

**Poznámka: na předponě**: Předponu identifikátoru URI se počítá podle segmentu a ne podle znaku. Například `/a/b` je předpona pro `/a/b/c` , ale nikoli pro `/a/bc`.

Následující fragment kódu Node.js ukazuje funkci s názvem **generateSasToken** , který počítá token z vstupy `resourceUri, signingKey, policyName, expiresInMins`. Následující části podrobně popisují, jak inicializovat různé vstupy pro případy použití v odlišných tokenu.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Porovnání je ekvivalentní kód Python a vygenerovat token zabezpečení:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

Funkce v jazyce C# k vygenerování tokenu zabezpečení jsou:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Protože doba platnosti tokenu ověření ve službě IoT Hub počítačích, musí být minimální posun hodin na počítač, který generuje token.

### <a name="use-sas-tokens-in-a-device-app"></a>Použití tokenů SAS v zařízení aplikace

Existují dva způsoby, jak získat **DeviceConnect** oprávnění službou IoT Hub s tokeny zabezpečení: použití [zařízení symetrického klíče z registru identit](#use-a-symmetric-key-in-the-identity-registry), nebo použijte [sdíleným přístupovým klíčem](#use-a-shared-access-policy).

Mějte na paměti, že všechny funkce, které jsou přístupné ze zařízení je zveřejněna rozhraním návrhu na koncové body s předponou `/devices/{deviceId}`.

> [!IMPORTANT]
> Jediný způsob, jak IoT Hub provádí ověřování konkrétního zařízení používá symetrický klíč identity zařízení. V případech, pokud zásady sdíleného přístupu slouží pro přístup k funkcím zařízení, řešení nutné vzít v úvahu vydávání tokenu zabezpečení jako důvěryhodné dílčí komponenty.

Koncové body s přístupem k zařízení nejsou (bez ohledu na protokol):

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Odesílání zpráv typu zařízení cloud. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Příjem zpráv typu cloud zařízení. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Použít symetrického klíče v registru identit

Při použití symetrický klíč identity zařízení k vygenerování tokenu, tak název zásad (`skn`) element tokenu je vynechán.

Například token vytvořili pro přístup ke všem funkcím zařízení by měl mít následující parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* podpisový klíč: žádné symetrický klíč pro `{device id}` identitu,
* žádný název zásad
* kdykoli vypršení platnosti.

Příkladem použití předchozí funkci Node.js, může být:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro ZAŘÍZENÍ1, by byl:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Je možné vytvořit token SAS s [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) nástroje, rozšíření příkazu rozhraní příkazového řádku [az iot hub generovat--token sas](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), nebo [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Použijte zásady sdíleného přístupu

Při vytváření tokenu z zásady sdíleného přístupu nastaven `skn` pole Název zásady. Tyto zásady, musí udělit **DeviceConnect** oprávnění.

Jsou dva základní scénáře pro použití zásady sdíleného přístupu pro přístup k funkcím zařízení:

* [cloudové brány protokolů](iot-hub-devguide-endpoints.md),
* [token služby](iot-hub-devguide-security.md#custom-device-and-module-authentication) používaný k implementaci vlastní ověřovací schémata.

Protože zásady sdíleného přístupu může potenciálně udělit přístup k připojení jako jakékoli zařízení, je potřeba použít správný identifikátor URI prostředku. při vytváření tokenů zabezpečení. Toto nastavení je obzvláště důležité pro token služby, které mají k určení rozsahu token k určitému zařízení pomocí identifikátoru URI prostředku. Tento bod není použití navigace pro brány protokolů jako jsou jsou již jehož prostřednictvím je umožněn přenos pro všechna zařízení.

Jako příklad tokenu služby pomocí předem vytvořené sdílené zásady přístupu volá **zařízení** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* podpisový klíč: jeden z klíčů `device` zásad,
* Název zásady: `device`,
* kdykoli vypršení platnosti.

Příkladem použití předchozí funkci Node.js, může být:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který uděluje přístup ke všem funkcím pro ZAŘÍZENÍ1, by byl:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brána protokolu pro všechna zařízení, jednoduše nastavení identifikátoru URI prostředku použít stejný token do `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Použití tokenů zabezpečení ze služby komponent

Součásti služby můžou generovat jenom tokeny zabezpečení pomocí udělení příslušných oprávnění, jak bylo popsáno dříve zásady sdíleného přístupu.

Zde jsou funkce služby, které jsou zveřejněné na koncové body:

| Koncový bod | Funkce |
| --- | --- |
| `{iot hub host name}/devices` |Vytvořit, aktualizovat, načítat a odstraňovat identit zařízení. |
| `{iot hub host name}/messages/events` |Příjem zpráv typu zařízení cloud. |
| `{iot hub host name}/servicebound/feedback` |Přijímat zpětnou vazbu pro zprávy typu cloud zařízení. |
| `{iot hub host name}/devicebound` |Odesílat zprávy typu cloud zařízení. |

Jako příklad služby generování předem vytvořené využitím sdílené zásady přístupu volá **registryRead** by vytvořit token s následujícími parametry:

* identifikátor URI prostředku: `{IoT hub name}.azure-devices.net/devices`,
* podpisový klíč: jeden z klíčů `registryRead` zásad,
* Název zásady: `registryRead`,
* kdykoli vypršení platnosti.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Výsledek, který může poskytnout přístup ke čtení všech identit zařízení, bude:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Podporované certifikáty X.509

K ověření zařízení pomocí služby IoT Hub tak, že nahrajete kryptografický otisk certifikátu nebo certifikační autorita (CA) do služby Azure IoT Hub můžete použít některý z certifikátů X.509. Ověřování pomocí kryptografické otisky certifikátu pouze ověří, zda uvedený kryptografický otisk shoduje má nakonfigurovaný kryptografický otisk. Ověřování pomocí certifikační autority ověří řetěz certifikátů. 

Podporované certifikáty patří:

* **Existující certifikát X.509**. Zařízení už můžete mít certifikát X.509, který je s ním spojená. Zařízení můžete použít tento certifikát k ověření pomocí služby IoT Hub. Funguje s kryptografickým otiskem nebo ověření certifikační Autority. 

* **Certifikát X.509 podepsaný certifikační Autoritou**. K identifikaci zařízení a ověřování pomocí služby IoT Hub, můžete použít certifikát X.509, který vygeneruje a podepsaný certifikační autoritou (CA). Funguje s kryptografickým otiskem nebo ověření certifikační Autority.

* **A místním vygeneruje a X-509 certifikát podepsaný držitelem**. Výrobce zařízení nebo interní nástroje pro nasazení můžete vygenerovat tyto certifikáty a uložení odpovídající privátní klíč (a certifikátu) na zařízení. Můžete použít nástroje jako [OpenSSL](https://www.openssl.org/) a [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) nástroj pro tento účel. Funguje pouze s kryptografickým otiskem ověřování. 

Zařízení může použít certifikát X.509 nebo token zabezpečení pro ověřování, ale ne obojí.

Další informace o ověřování pomocí certifikační autority najdete v tématu [zařízení ověřování pomocí certifikátů X.509 certifikační Autority](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Zaregistrovat certifikát X.509 pro zařízení

[Sady SDK služby Azure IoT pro jazyk C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service) (verze 1.0.8+) podporuje registraci zařízení, které používá certifikát X.509 pro ověřování. Další rozhraní API, například import a export zařízení také podporují certifikáty X.509.

Příkaz rozšíření rozhraní příkazového řádku můžete použít také [az iot hub-identity zařízení](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) konfigurace certifikátů X.509 pro zařízení.

### <a name="c-support"></a>C\# podpory

**RegistryManager** třída poskytuje programový způsob, jak zaregistrovat zařízení. Zejména v případě **AddDeviceAsync** a **UpdateDeviceAsync** metody umožňují, abyste se zaregistrovali a aktualizujte zařízení v registru identit služby IoT Hub. Tyto dvě metody přijímají **zařízení** instance jako vstup. **Zařízení** třída zahrnuje **ověřování** vlastnost, která vám umožní určit primární a sekundární X.509 kryptografické otisky certifikátu. Kryptografický otisk představuje hodnotu hash SHA256 (uložené pomocí binární kódování DER) X.509 certifikátu. Máte možnost určit primární kryptografický otisk nebo sekundární kryptografický otisk nebo obojí. Primární a sekundární kryptografické otisky jsou podporovány pro zpracování scénáře změny klíčů certifikátu.

Tady je ukázka C\# fragment kódu pro registraci zařízení pomocí kryptografický otisk certifikátu X.509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Použít certifikát X.509 během operací za běhu

[Zařízení Azure IoT SDK pro .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device) (verze 1.0.11+) podporuje používání certifikátů X.509.

### <a name="c-support"></a>C\# podpory

Třída **DeviceAuthenticationWithX509Certificate** podporuje vytváření **DeviceClient** instance pomocí certifikátu X.509. Certifikát X.509 musí být ve formátu PFX (také nazývané PKCS #12), který obsahuje privátní klíč.

Tady je ukázka fragmentu kódu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Vlastní zařízení a modul ověřování

Můžete použít Centrum IoT [registr identit](iot-hub-devguide-identity-registry.md) nakonfigurujte zařízení/modulu zabezpečovacích přihlašovacích údajů a přístup k ovládacímu prvku pomocí [tokeny](iot-hub-devguide-security.md#security-tokens). Pokud řešení IoT už má vlastní identitu registru a/nebo ověřování schématu, zvažte vytvoření *token služby* integrovat Tato infrastruktura služby IoT Hub. Tímto způsobem můžete použít další funkce IoT ve vašem řešení.

Token služby je vlastní cloudovou službu. Pomocí služby IoT Hub *sdílené zásady přístupu* s **DeviceConnect** nebo **ModuleConnect** oprávnění k vytvoření *rozsahem zařízení* nebo *rozsahem modulu* tokeny. Tyto tokeny umožňují zařízení a modul pro připojení ke službě IoT hub.

![Kroky pro model služby tokenů](./media/iot-hub-devguide-security/tokenservice.png)

Tady jsou hlavní kroky vzor služby tokenů:

1. Vytvoření služby IoT Hub sdílené zásady přístupu s **DeviceConnect** nebo **ModuleConnect** oprávnění pro službu IoT hub. Můžete vytvořit tyto zásady v [webu Azure portal](https://portal.azure.com) nebo prostřednictvím kódu programu. Služba tokenu používají tuto zásadu k podepisování tokenů, který vytvoří.

2. Když se zařízení a modul musí pro přístup k službě IoT hub, vyžaduje podepsaný token z tokenu služby. Zařízení můžete ověřit se schématem ověřování registru nebo vaší vlastní identitu k určení identity zařízení a modul, který používá služba tokenu k vytvoření tohoto tokenu.

3. Služba tokenu vrátí token. Token, který je vytvořen pomocí `/devices/{deviceId}` nebo `/devices/{deviceId}/module/{moduleId}` jako `resourceURI`, s `deviceId` jako zařízení ověřovaného nebo `moduleId` jako modul ověřuje. Služba tokenu používá zásady sdíleného přístupu k vytvoření tokenu.

4. Zařízení a modul použije token přímo prostřednictvím služby IoT hub.

> [!NOTE]
> Můžete použít třídu .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) nebo třídy jazyka Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) k vytvoření tokenu v tokenu služby.

Token služby můžete podle potřeby nastavit vypršení platnosti tokenu. Když vyprší platnost tokenu, služby IoT hub přeruší připojení k zařízení nebo modulů. Potom zařízení a modul musí požádat o nový token od služby tokenů. Čas vypršení platnosti krátký zvyšuje zatížení zařízení nebo modulů a služba tokenů.

Pro zařízení a modul pro připojení k centru, je nutné ho přidat do registru identit služby IoT Hub, i když se používá token a ne klíč pro připojení. Proto můžete nadále používat řízení přístupu podle zařízení/za – moduly povolením nebo zakázáním identita zařízení a modul [registr identit](iot-hub-devguide-identity-registry.md). Tento přístup snižuje rizika při použití s dobou dlouhé vypršení platnosti tokenů.

### <a name="comparison-with-a-custom-gateway"></a>Porovnání s vlastní bránu

Služba tokenů vzor je doporučeným způsobem, jak implementovat vlastní identitu schéma registru/ověřování pomocí služby IoT Hub. Tento model se doporučuje, protože nadále zpracovat většinu provoz řešení služby IoT Hub. Nicméně pokud vlastním ověřovacím systémem tak vzájemně propojeny s protokolem, můžete požadovat, aby *vlastní bránu* zpracovat veškerý provoz. Příkladem takové situaci používá [zabezpečení TLS (Transport Layer) a předsdílené klíče (PSKs)](https://tools.ietf.org/html/rfc4279). Další informace najdete v tématu [brána protokolu](iot-hub-protocol-gateway.md) článku.

## <a name="reference-topics"></a>Referenční témata:

V následujících tématech vám poskytnout další informace o řízení přístupu ke službě IoT hub.

## <a name="iot-hub-permissions"></a>Oprávnění služby IoT Hub

V následující tabulce jsou uvedeny oprávnění, která slouží k řízení přístupu ke službě IoT hub.

| Oprávnění | Poznámky |
| --- | --- |
| **RegistryRead** |Uděluje přístup pro čtení k registru identit. Další informace najdete v tématu [registr identit](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění je použít cloudové back endové služby. |
| **RegistryReadWrite** |Uděluje přístup čtení a zápis do registru identit. Další informace najdete v tématu [registr identit](iot-hub-devguide-identity-registry.md). <br/>Toto oprávnění je použít cloudové back endové služby. |
| **ServiceConnect** |Uděluje přístup ke cloudovým komunikace a monitorování koncových bodů služby přístupem. <br/>Uděluje oprávnění pro příjem zpráv typu zařízení cloud, odesílat zprávy typu cloud zařízení a načtení odpovídajícího doručení potvrzení. <br/>Uděluje oprávnění k načtení potvrzení o doručení pro soubor nahraje. <br/>Uděluje oprávnění k přístupu dvojčat aktualizovat značky a požadované vlastnosti, načtení ohlášených vlastností a spouštět dotazy. <br/>Toto oprávnění je použít cloudové back endové služby. |
| **DeviceConnect** |Uděluje přístup ke koncovým bodům přístupem k zařízení. <br/>Uděluje oprávnění k odesílání zpráv typu zařízení cloud a příjem zpráv typu cloud zařízení. <br/>Uděluje oprávnění k provedení nahrávání souborů ze zařízení. <br/>Uděluje oprávnění přijímat oznámení vlastnost požadovaného dvojče zařízení a aktualizovat dvojče zařízení ohlášené vlastnosti. <br/>Uděluje oprávnění k provedení soubor nahraje. <br/>Toto oprávnění se používají zařízení. |

## <a name="additional-reference-material"></a>Další referenční materiál

Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý IoT hub zpřístupní pro operace za běhu a správy.

* [Omezování a kvótách](iot-hub-devguide-quotas-throttling.md) popisuje kvóty a omezování chování, které se vztahují ke službě IoT Hub.

* [Azure IoT zařízení a služby sady SDK](iot-hub-devguide-sdks.md) uvádí různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.

* [Dotazovací jazyk služby IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, slouží k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokolu MQTT.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak řídit přístup k službě IoT Hub, vás může zajímat v následujících tématech příručky pro vývojáře IoT Hub:

* [Použití dvojčat zařízení k synchronizaci stavu a konfigurace](iot-hub-devguide-device-twins.md)
* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)
* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud chcete vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujících kurzech služby IoT Hub:

* [Začínáme s Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Postup odesílání zpráv typu cloud zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Postupy zpracování zpráv typu zařízení cloud služby IoT Hub](tutorial-routing.md)
