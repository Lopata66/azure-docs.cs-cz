---
title: Přesun aplikace klasické pracovní plochy volání webových rozhraní API do produkčního prostředí – Microsoft Identity Platform | Azure
description: Přečtěte si, jak přesunout desktopovou aplikaci, která volá webová rozhraní API do produkčního prostředí.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882875"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: přesunout do produkčního prostředí

V tomto článku se dozvíte, jak přesunout desktopovou aplikaci, která volá webová rozhraní API do produkčního prostředí.

## <a name="handle-errors-in-desktop-applications"></a>Zpracování chyb v aplikacích klasické pracovní plochy

V různých tocích jste se naučili, jak zpracovávat chyby pro tiché toky, jak je znázorněno ve fragmentech kódu. Také jste viděli, že existují případy, kdy je potřeba interakce, jako v rámci přírůstkového souhlasu a podmíněného přístupu.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Mít souhlas uživatele před několika prostředky

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro Microsoft Identity Platform, ale ne pro Azure Active Directory (Azure AD) B2C. Azure AD B2C podporuje jenom souhlas správce, ne pro vyjádření souhlasu s uživatelem.

Pomocí koncového bodu Microsoft Identity Platform (v 2.0) nemůžete získat token pro několik prostředků současně. `scopes`Parametr může obsahovat obory pouze pro jeden prostředek. Můžete zajistit, aby uživatel mohl předběžně odeslat několik prostředků pomocí `extraScopesToConsent` parametru.

Například můžete mít dva prostředky, které mají dva obory:

- `https://mytenant.onmicrosoft.com/customerapi`s rozsahy `customer.read` a`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`s rozsahy `vendor.read` a`vendor.write`

V tomto příkladu použijte `.WithAdditionalPromptToConsent` modifikátor, který má `extraScopesToConsent` parametr.

Například:

### <a name="in-msalnet"></a>V MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>V MSAL pro iOS a macOS

Cíl-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

SWIFT

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Toto volání vám získá přístupový token pro první webové rozhraní API.

Pokud potřebujete zavolat druhé webové rozhraní API, zavolejte `AcquireTokenSilent` rozhraní API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Osobní účet Microsoft vyžaduje při každém spuštění aplikace souhlas.

Pro uživatele osobního účtu Microsoft se znovu zobrazí výzva k zadání souhlasu každého nativního klienta (Desktop nebo mobilní aplikace) volání metody autorizovat je zamýšlené chování. Nativní identita klienta je ze své podstaty nezabezpečená, což je v rozporu s identitou důvěrné klientské aplikace. Důvěrné klientské aplikace vyměňují tajný kód s platformou Microsoft identity, aby prokázali jejich identitu. Platforma Microsoft identity se rozhodla snížit toto zabezpečení pro zákazníky pomocí výzvy k souhlasu uživatele při každém autorizaci aplikace.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
