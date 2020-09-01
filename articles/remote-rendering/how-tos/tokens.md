---
title: Získání přístupových tokenů služeb
description: Popisuje, jak vytvořit tokeny pro přístup k rozhraním REST API ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076153"
---
# <a name="get-service-access-tokens"></a>Získání přístupových tokenů služeb

Přístup k rozhraním REST API ARR se uděluje jenom autorizovaným uživatelům. Chcete-li prokázat autorizaci, je nutné odeslat *přístupový token* společně s požadavky REST. Tyto tokeny se vydávají pomocí *služby tokenů zabezpečení* (STS) v Exchangi pro klíč účtu. Tokeny mají **dobu životnosti 24 hodin** , takže je lze vydat uživatelům, aniž by jim poskytli úplný přístup ke službě.

Tento článek popisuje, jak tento přístupový token vytvořit.

## <a name="prerequisites"></a>Požadavky

[Vytvořte si účet ARR](create-an-account.md), pokud ho ještě nemáte.

## <a name="token-service-rest-api"></a>REST API služby tokenu

Pro vytvoření přístupových tokenů poskytuje *služba tokenů zabezpečení* jednu REST API. Adresa URL služby ARR STS je https: \/ /STS.mixedreality.Azure.com.

### <a name="get-token-request"></a>Žádost o získání tokenu

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /Accounts/**accountId**/token | GET |

| Záhlaví | Hodnota |
|--------|:------|
| Autorizace | "Bearer **accountId**:**accountKey**" |

Položku *accountId* a *accountKey* nahraďte odpovídajícími daty.

### <a name="get-token-response"></a>Odpověď Get token

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | AccessToken: řetězec | Success |

| Záhlaví | Účel |
|--------|:------|
| MS-CV | Tato hodnota se dá použít k trasování volání v rámci služby. |

## <a name="getting-a-token-using-powershell"></a>Získání tokenu pomocí PowerShellu

Následující kód PowerShellu ukazuje, jak odeslat nezbytnou žádost REST do služby STS. Pak vypíše token do příkazového řádku PowerShellu.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skript pouze vytiskne token do výstupu, ze kterého můžete zkopírovat & jej vložit. Pro skutečný projekt byste měli tento proces automatizovat.

## <a name="next-steps"></a>Další kroky

* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
* [Rozhraní API front-endu Azure](../how-tos/frontend-apis.md)
* [REST API správy relací](../how-tos/session-rest-api.md)
