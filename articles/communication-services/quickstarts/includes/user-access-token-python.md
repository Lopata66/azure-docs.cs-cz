---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 472129be5baa865365b49894b705d84c23e9cd04
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506309"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 nebo novější.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Pomocí textového editoru vytvořte soubor s názvem **Issue-Access-Tokens.py** v kořenovém adresáři projektu a přidejte strukturu pro program, včetně základního zpracování výjimek. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte klientskou knihovnu pro správu služby Azure Communication Services pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-communication-administration
```

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `CommunicationIdentityClient` s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Přidejte tento kód do `try` bloku:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-an-identity"></a>Vytvoření identity

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `create_user` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Uložte si získanou identitu s mapováním na uživatele vaší aplikace. Například uložením do databáze aplikačního serveru. Identita se vyžaduje později pro vydávání přístupových tokenů.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>Vystavení přístupových tokenů

Použijte `issue_token` metodu pro vydání přístupového tokenu pro již existující identitu komunikačních služeb. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md). Nová instance parametru `communicationUser` se dá sestavit na základě řetězcové reprezentace identity komunikační služby Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Přístupové tokeny jsou krátkodobé přihlašovací údaje, které je potřeba znovu vydat. V takovém případě může dojít k přerušení činnosti uživatelů vaší aplikace. `expires_on`Vlastnost Response označuje dobu života přístupového tokenu.

## <a name="refresh-access-tokens"></a>Obnovení přístupových tokenů

K aktualizaci přístupového tokenu použijte `CommunicationUser` objekt, který se má znovu vystavit:

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Odvolat přístupové tokeny

V některých případech je možné explicitně odvolat přístupové tokeny. Například když uživatel aplikace změní heslo, které používá k ověření vaší služby. Metoda `revoke_tokens` zruší platnost všech aktivních přístupových tokenů, které byly vystaveny identitě.

```python  
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Odstranění identity

Odstranění identity odvolá všechny aktivní přístupové tokeny a zabrání vám v vystavování přístupových tokenů pro danou identitu. Zároveň se tím odebere veškerý trvalý obsah přidružený k identitě.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Spuštění kódu

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *Issue-Access-token.py* a `python` Spusťte aplikaci spuštěním následujícího příkazu.

```console
python ./issue-access-token.py
```
