---
title: Konfigurace aplikace Azure REST API – zámky
description: Referenční stránky pro práci s zámky klíč-hodnota pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424088"
---
# <a name="locks"></a>Zámky

verze API-Version: 1,0

Toto rozhraní API poskytuje sémantiku zámku/odemknutí pro prostředek klíč-hodnota. Podporuje následující operace:

- Umístit zámek
- Odebrat zámek

Je-li k dispozici, `label` musí se jednat o explicitní hodnotu popisku ( **nikoli** zástupný znak). U všech operací je to volitelný parametr. Je-li tento parametr vynechán, nepředpokládá žádný popisek.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Zamknout Key-Value

- **Požadováno:** ``{key}`` , ``{api-version}``  
- *Volitelné:*``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Pokud klíč-hodnota neexistuje, vrátí se následující odpověď:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Odemknout Key-Value

- **Požadováno:** ``{key}`` , ``{api-version}``  
- *Volitelné:*``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Pokud klíč-hodnota neexistuje, vrátí se následující odpověď:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Podmíněný zámek/odemknutí

Aby nedocházelo ke konfliktům časování, použijte `If-Match` nebo `If-None-Match` vyžádejte hlavičky. `etag`Argument je součástí reprezentace klíče. Pokud `If-Match` `If-None-Match` jsou nebo vynechány, operace bude nepodmíněný.

Následující požadavek použije operaci pouze v případě, že aktuální reprezentace klíč-hodnota odpovídá zadané hodnotě `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Následující požadavek použije operaci pouze v případě, že aktuální reprezentace klíč-hodnota existuje, ale neodpovídá zadané hodnotě `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
