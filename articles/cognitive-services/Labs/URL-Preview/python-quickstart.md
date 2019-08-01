---
title: 'Rychlý start: Náhled adresy URL projektu, Python'
titlesuffix: Azure Cognitive Services
description: Ukázkový skript, který vám pomůže rychle začít používat funkci Project URL Preview za použití Pythonu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 19a226fb580f3d4215b7c3f04f17c3f92505987e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697980"
---
# <a name="quickstart-url-preview-with-python"></a>Rychlý start: Náhled adresy URL v Pythonu

Následující příklady Pythonu vytvoří náhled adresy URL pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

V tomto příkladu se používá Python 3.6.

## <a name="code-scenario"></a>Scénář kódu 

Následující kód vytvoří náhled adresy URL.
Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte adresu URL dotazu k náhledu a přidejte parametr dotazu.  
3. Nastavte parametr dotazu.
4. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku *Ocp-Apim-Subscription-Key*.
5. Nastavte hlavičku *Ocp-Apim-Subscription-Key*. 
6. Vytvořte připojení a odešlete požadavek.
7. Zobrazte výsledky ve formátu JSON.

Následuje celý kód pro tuto ukázku:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro adresu URL uzlu](node-quickstart.md)
