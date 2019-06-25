---
title: Translator Text API transkripce – metoda
titlesuffix: Azure Cognitive Services
description: Použijte metodu transkripce Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 138a04cca1bbbaf7b59f628f491a5f13d73fb6f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387396"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterace

Převede text v jednom jazyce z jeden skript pro další skript.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané v řetězci dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>api-version</td>
    <td>*Povinný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Povinný parametr*.<br/>Určuje jazyk textu pro převod z jednoho skriptu do jiného. Je to možné jazyky jsou uvedené v `transliteration` oboru získala při dotazování na službu pro jeho [podporované jazyky](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Povinný parametr*.<br/>Určuje skript vstupního textu. Vyhledání [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru se najít vstupní skripty k dispozici pro vybraný jazyk.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Povinný parametr*.<br/>Určuje výstupní skript. Vyhledání [podporované jazyky](./v3-0-languages.md) pomocí `transliteration` oboru najít výstup nejsou k dispozici pro vybranou kombinaci jazyk skripty a vstupní skriptu.</td>
  </tr>
</table> 

Hlavičky žádosti patří:

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Ověřovací hlavičky</td>
    <td><em>Hlavička požadavku požadované</em>.<br/>Zobrazit <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostupné možnosti pro ověřování</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Hlavička požadavku požadované*.<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Hlavička požadavku požadované*.<br/>Délka textu požadavku.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Volitelné*.<br/>Klientem generovaná identifikátor GUID k jednoznačné identifikaci požadavku. Všimněte si, že můžete tuto hlavičku vynechat, pokud zahrnují ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Text požadavku

Text požadavku je pole JSON. Každý prvek pole je objekt JSON s řetězcovou vlastnost s názvem `Text`, která představuje řetězec k převedení.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Platí následující omezení:

* Pole může mít maximálně 10 prvků.
* Textová hodnota elementu pole nemůže být delší než 1 000 znaků včetně mezer.
* Celý text zahrnutý v požadavku nemůže být delší než 5 000 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON se jeden výsledek pro každý prvek vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `text`: Řetězec, který je výsledkem konverze na výstupní skript vstupního řetězce.
  
  * `script`: Řetězec určující skript, které ve výstupu.

Je například odpověď JSON:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Hlavičky odpovědi

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>X-RequestId</td>
    <td>Hodnota vygenerované službou k identifikaci požadavku. Používá se pro účely odstraňování potíží.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Stavové kódy odezvy

Tady jsou možné stavové kódy HTTP, které vrátí žádost o. 

<table width="100%">
  <th width="20%">Stavový kód</th>
  <th>Popis</th>
  <tr>
    <td>200</td>
    <td>Úspěch</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrů dotazu je chybí nebo není platný. Opravte parametry požadavku než to zkusíte znovu.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Požadavek nešlo ověřit. Zkontrolujte, zda jsou pověření zadaná a je platný.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Požadavek není autorizovaný. Najdete podrobnosti o chybové zprávě. To často určuje, že se využilo všechny bezplatné překlady zkušebního předplatného k dispozici.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Server odmítl požadavek, protože klient překročil omezení požadavků.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server je dočasně nedostupný. Zkuste požadavek. Pokud chyba přetrvává, nahlaste to s: datum a čas selhání žádost s identifikátorem v hlavičce odpovědi `X-RequestId`a identifikátor klienta v hlavičce požadavku `X-ClientTraceId`.</td>
  </tr>
</table> 

Pokud dojde k chybě, vrátí požadavek také chybová odpověď JSON. Kód chyby je, chybu zařadit 6místným číselným číslo kombinování stavový kód HTTP 3 číslice následované číslem 3 číslice na další. Běžné kódy chyb můžete najít na [stránky referenční dokumentace rozhraní Translator Text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak převést dva řetězce japonské Romanized japonštinu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

Datová část JSON pro požadavek v tomto příkladu:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Pokud používáte cURL v okně příkazového řádku, který nepodporuje znaky Unicode, využijte následující datové části JSON a uložte ho do souboru s názvem `request.txt`. Nezapomeňte uložit soubor s `UTF-8` kódování.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
