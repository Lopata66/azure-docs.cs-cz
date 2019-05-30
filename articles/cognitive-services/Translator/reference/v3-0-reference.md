---
title: Odkaz na Translator Text API verze 3.0
titlesuffix: Azure Cognitive Services
description: Referenční dokumentace pro verze Translator Text API 3.0.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 973d38413fa39fec1c50b5e9770b6114fa2c4c3d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387518"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>Co je nového?

Verze 3 rozhraní Translator Text API poskytuje moderní webové API založené na JSON. Zvyšuje použitelnost a výkon, když sloučení stávajících funkcí do menšího počtu operací a nabízí nové funkce.

 * Přepis pro převod textu v jednom jazyce z jeden skript na jiný skript.
 * Překlad do několika jazyků v jedné žádosti.
 * Rozpoznávání jazyka, překlad a přepis v jedné žádosti.
 * Slovník, který se vyhledávací termín najít back překlady a příklady znázorňující termínů používaných v kontextu alternativní překlady.
 * Další výsledky vyhledávání jazyka.

## <a name="base-urls"></a>Základní adresy URL

Microsoft Translator je provádět z více umístění datového centra. Aktuálně jsou umístěny v 6 [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Jižní Americe:** Západní USA 2 a střed USA – západ 
* **Asie a Tichomoří:** Jihovýchodní Asie a Korea – jih
* **Evropa:** Severní Evropa a západní Evropa

Požadavky na rozhraní Microsoft Translator Text API jsou ve většině případů zpracovat datacenter, která je nejblíž k původu žádosti. V případě datacenter selhání může být žádost směrována mimo oblast.

Vynutit požadavek na ošetřit konkrétní datové centrum, změňte globální koncový bod rozhraní API požadavek na požadovanou místní koncový bod:

|Popis|Oblast|Základní adresa URL|
|:--|:--|:--|
|Azure|Globální|  api.cognitive.microsofttranslator.com|
|Azure|Severní Amerika|   api-nam.cognitive.microsofttranslator.com|
|Azure|Evropa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asie a Tichomoří|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Přihlaste se k rozhraní Translator Text API odběru nebo [více služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) Microsoft Cognitive Services a použít k ověření klíče (k dispozici na webu Azure Portal) vašeho předplatného. 

Existují tři hlavičky, které můžete použít k ověření vašeho předplatného. Tato tabulka poskytuje, popisuje, jak každý používat:

|Hlavičky|Popis|
|:----|:----|
|OCP-Apim-Subscription-Key|*Použít s předplatným služeb Cognitive Services, Pokud předáváte tajný klíč*.<br/>Hodnota je Azure tajný klíč pro vaše předplatné Translator Text API.|
|Autorizace|*Použít s předplatným služeb Cognitive Services, Pokud předáváte ověřovací token.*<br/>Hodnota je nosný token: `Bearer <token>`.|
|OCP-Apim předplatné – oblasti|*Pokud předáváte více služeb tajný klíč, pomocí předplatného víc služeb Cognitive Services.*<br/>Hodnota je oblasti pro víc služeb předplatného. Tato hodnota je volitelná, kdy se nepoužívá víc služeb předplatného.|

###  <a name="secret-key"></a>Tajný klíč
První možností je provést ověření pomocí `Ocp-Apim-Subscription-Key` záhlaví. Stačí, když přidáte `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` záhlaví na váš požadavek.

### <a name="authorization-token"></a>Autorizační token
Alternativně můžete exchange váš tajný klíč pro přístupový token. Tento token je součástí každého požadavku, jako `Authorization` záhlaví. Ujistěte se, pokud chcete získat autorizační token, `POST` žádost na následující adresu URL:

| Prostředí     | Adresa URL pro ověřování služby                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Tady je příklad žádosti k získání tokenu Zadaný tajný klíč:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Úspěšná žádost vrátí kódovaného přístupový token jako prostý text v textu odpovědi. Službu Translator jako nosný token v povolení předána platný token.

```
Authorization: Bearer <Base64-access_token>
```

Ověřovací token je platný 10 minut. Token, který by měl být znovu použít při vytváření více volání rozhraní Translator API. Ale pokud váš program provede požadavky rozhraní Translator API po delší dobu, potom aplikace musí požádat o nový přístupový token v pravidelných intervalech (například každých 8 minut).

### <a name="multi-service-subscription"></a>Víc služeb předplatného

Poslední ověřování možností je použít víc služeb Cognitive Service předplatného. To umožňuje používat jeden tajný klíč k ověření požadavků pro víc služeb. 

Pokud používáte víc služeb tajného klíče, musí obsahovat dvě záhlaví ověřování při zpracování požadavku. První předá tajný klíč, druhý určuje oblastí, přidružených k vašemu předplatnému. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Oblast je povinná pro víc služeb předplatného rozhraní Text API. Oblast výběru je jen jedna oblast, můžete použít pro překlad textu při použití klíče víc služeb předplatného a musí být stejné oblasti, které jste vybrali při registraci předplatného víc služeb na webu Azure portal.

Jsou dostupné oblasti `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, a `westus2`.

Pokud předáte tajný klíč v řetězci dotazu s parametrem `Subscription-Key`, pak je nutné zadat oblast s parametrem dotazu `Subscription-Region`.

Pokud používáte nosný token, je nutné získat token z koncového bodu oblasti: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Chyby

Standardní chybové odpovědi je objekt JSON s dvojice název/hodnota s názvem `error`. Hodnota je také objekt JSON s vlastnostmi:

  * `code`: Kód chyby definované na serveru.

  * `message`: Řetězec, který poskytuje reprezentaci čitelné chyby.

Zákazník s bezplatnou zkušební verzi předplatného by například zobrazí následující chyba, po vyčerpání volného kvóty:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kód chyby je, chybu zařadit 6místným číselným číslo kombinování stavový kód HTTP 3 číslice následované číslem 3 číslice na další. Běžné kódy chyb jsou:

| Kód | Popis |
|:----|:-----|
| 400000| Jeden ze vstupů požadavku není platný.|
| 400001| Parametr "scope" je neplatná.|
| 400002| Parametr "kategorie" je neplatný.|
| 400003| Specifikátor jazyk je chybějící nebo neplatný.|
| 400004| Cílový specifikátor skriptu ("do skriptu") je chybějící nebo neplatný.|
| 400005| Vstupní text je chybějící nebo neplatný.|
| 400006| Kombinace jazyka a skriptu není platná.|
| 400018| Specifikátorem zdroje skriptu ("ze skriptu") je chybějící nebo neplatný.|
| 400019| Jeden zadaný jazyk není podporován.|
| 400020| Jeden z prvků v poli vstupní text není platný.|
| 400021| Parametr verze rozhraní API je chybějící nebo neplatný.|
| 400023| Jedna z dvojice zadaný jazyk není platný.|
| 400035| Zdrojový jazyk ("z" pole) není platný.|
| 400036| Cílový jazyk ("do" pole) je chybějící nebo neplatný.|
| 400042| Jednu z možností zadané ("Options" pole) není platný.|
| 400043| ID trasování klienta (ClientTraceId pole nebo záhlaví X-ClientTranceId) je chybějící nebo neplatný.|
| 400050| Vstupní text je příliš dlouhý. Zobrazení [omezení počtu požadavků](../request-limits.md).|
| 400064| Parametr "překladu" je chybějící nebo neplatný.|
| 400070| Počet skriptů target (ToScript parametr) neodpovídá počtu cíl jazyky (parametr).|
| 400071| Hodnota není platná pro TextType.|
| 400072| Vstupní textové pole má příliš mnoho prvků.|
| 400073| Parametr skriptu není platná.|
| 400074| Text žádosti nemá platný formát JSON.|
| 400075| Kombinace jazyka párování a kategorii není platné.|
| 400077| Byla překročena maximální požadavek velikost. Zobrazení [omezení počtu požadavků](../request-limits.md).|
| 400079| Vlastní systém požadovaný pro překlad mezi z a do jazyka neexistuje.|
| 401000| Požadavek není autorizovaný, protože jsou přihlašovací údaje chybí nebo je neplatný.|
| 401015| "Zadané přihlašovací údaje jsou pro rozhraní API pro rozpoznávání řeči. Tato žádost vyžaduje přihlašovací údaje pro rozhraní Text API. Použijte prosím předplatné Translator Text API."|
| 403000| Operace není povolena.|
| 403001| Operace není povolena, protože předplatné překročila její kvótu.|
| 405000| Metoda žádosti se nepodporuje pro požadovaný prostředek.|
| 408001| Je připravováno překladový systém požadovaný. Zkuste to prosím znovu za pár minut.|
| 408002| Žádosti vypršel časový limit čekání na příchozí datový proud. Klient nevrátil požadavku v rámci doby čekání byla připravena na serveru. Klient opakovat žádost o bez úprav kdykoli později.|
| 415000| Hlavička Content-Type je chybějící nebo neplatný.|
| 429000, 429001, 429002| Server odmítl požadavek, protože klient překročil omezení požadavků.|
| 500000| Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to datum/čas chyby, požádejte o identifikátor odpovědi hlavičku X-RequestId a identifikátor klienta z hlavičky X-ClientTraceId.|
| 503000| Služba je dočasně nedostupná. Zkuste to prosím znovu. Pokud chyba přetrvává, nahlaste to datum/čas chyby, požádejte o identifikátor odpovědi hlavičku X-RequestId a identifikátor klienta z hlavičky X-ClientTraceId.|

