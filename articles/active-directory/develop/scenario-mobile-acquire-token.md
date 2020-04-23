---
title: Získání tokenu pro volání webového rozhraní API (mobilní aplikace) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API. (Získat token pro aplikaci.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 2be074c457eaadd1fb6467cbcfdd45a2e7745613
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098896"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Získání tokenu pro mobilní aplikaci, která volá webová rozhraní API

Předtím, než aplikace může volat chráněná webová rozhraní API, potřebuje přístupový token. Tento článek vás provede procesem získání tokenu pomocí knihovny Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definování oboru

Když vyžádáte token, budete muset definovat obor. Obor určuje, k jakým datům může aplikace přistupovat.

Nejjednodušší způsob, jak definovat rozsah, je zkombinovat požadované webové rozhraní API `App ID URI` s oborem. `.default` Tato definice instruuje platformu Microsoft identity, kterou vaše aplikace vyžaduje pro všechny obory, které jsou nastavené na portálu.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Získat tokeny

### <a name="acquire-tokens-via-msal"></a>Získat tokeny prostřednictvím MSAL

MSAL umožňuje aplikacím získat bez tichého a interaktivního získávání tokenů. Když `AcquireTokenSilent()` zavoláte `AcquireTokenInteractive()`nebo, vrátí MSAL přístupový token pro požadované obory. Správným vzorem je provedení tichého požadavku a následného návratu k interaktivnímu požadavku.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

První pokus o získání tokenu v tichém režimu:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Pokud MSAL vrátí `MSALErrorInteractionRequired`, zkuste získat tokeny interaktivně:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL pro iOS a macOS podporuje různé modifikátory k interaktivnímu nebo tichému získání tokenu:
* [Společné parametry pro získání tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametry pro získání interaktivního tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametry pro získání tichého tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Následující příklad ukazuje minimální kód pro interaktivní získání tokenu. V příkladu se používá Microsoft Graph ke čtení profilu uživatele.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Povinné parametry v MSAL.NET

`AcquireTokenInteractive`má pouze jeden povinný parametr: `scopes`. `scopes` Parametr vytvoří výčet řetězců, které definují obory, pro které je vyžadován token. Pokud je token pro Microsoft Graph, můžete najít požadované obory v referenčních informacích k rozhraní API každého Microsoft Graph API. V odkazu přejdete do části Permissions (oprávnění).

Pokud například chcete [Zobrazit seznam kontaktů uživatele](https://docs.microsoft.com/graph/api/user-list-contacts), použijte obor "User. Read", "Contacts. Read". Další informace najdete v tématu informace o [Microsoft Graph oprávnění](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

V Androidu můžete při vytváření aplikace pomocí nástroje `PublicClientApplicationBuilder`zadat nadřazenou aktivitu. Pokud v daném čase nezadáte nadřazenou aktivitu, můžete ji později zadat pomocí `.WithParentActivityOrWindow` jako v následující části. Pokud zadáte nadřazenou aktivitu, pak se token vrátí do této nadřazené aktivity po interakci. Pokud ho nezadáte, vyvolá `.ExecuteAsync()` volání výjimku.

#### <a name="specific-optional-parameters-in-msalnet"></a>Konkrétní volitelné parametry v MSAL.NET

Následující části vysvětlují volitelné parametry v MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` Parametr řídí interakce s uživatelem zadáním výzvy.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Třída definuje následující konstanty:

- `SelectAccount`vynutí službu tokenu zabezpečení (STS) k zobrazení dialogového okna pro výběr účtu. Dialogové okno obsahuje účty, pro které má uživatel relaci. Tuto možnost můžete použít, pokud chcete, aby uživatel mohl zvolit mezi různými identitami. Tato možnost Drives MSAL se `prompt=select_account` pošle poskytovateli identity.

    `SelectAccount` Konstanta je výchozí a efektivně nabízí nejlepší možné prostředí na základě dostupných informací. Dostupné informace můžou zahrnovat účet, přítomnost relace pro uživatele a tak dále. Tuto výchozí hodnotu neměňte, pokud to nemáte dobrý důvod.
- `Consent`umožňuje uživateli vyzvat k vyjádření souhlasu i v případě, že byl souhlas udělen dříve. V takovém případě MSAL odesílá `prompt=consent` poskytovateli identity.

    Můžete chtít použít `Consent` konstantu v aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se při každém použití aplikace zobrazilo dialogové okno souhlasu.
- `ForceLogin`umožňuje službě zobrazit uživateli výzvu k zadání přihlašovacích údajů i v případě, že není nutná výzva.

    Tato možnost může být užitečná v případě, že získání tokenu se nezdařilo a chcete uživateli umožnit, aby se znovu přihlásili. V takovém případě MSAL odesílá `prompt=login` poskytovateli identity. Tuto možnost můžete chtít použít v aplikacích zaměřených na zabezpečení, kde zásady správného řízení organizace vyžadují, aby se uživatel přihlásil pokaždé, když přistupují k určitým částem aplikace.
- `Never`je určen pouze pro .NET 4,5 a prostředí Windows Runtime (WinRT). Tato konstanta nevyzve uživatele, ale pokusí se použít soubor cookie, který je uložený v skrytém vloženém webovém zobrazení. Další informace najdete v tématu [použití webových prohlížečů s MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Pokud tato možnost není úspěšná, `AcquireTokenInteractive` vyvolá výjimku, která vás upozorní, že je potřeba interakce uživatelského rozhraní. Pak je nutné použít jiný `Prompt` parametr.
- `NoPrompt`neodešle výzvu poskytovateli identity.

    Tato možnost je užitečná jenom pro zásady úprav profilu v Azure Active Directory B2C. Další informace najdete v tématu [B2C – konkrétní](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Použijte `WithExtraScopeToConsent` modifikátor v rozšířeném scénáři, kde chcete, aby uživatel poskytoval svůj souhlas s několika prostředky. Tento modifikátor můžete použít, když nechcete používat přírůstkový souhlas, který se obvykle používá s MSAL.NET nebo Microsoft Identity Platform 2,0. Další informace najdete v tématu o tom, že [Uživatel souhlasí s několika prostředky](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Zde je příklad kódu:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Další nepovinné parametry

Další informace o dalších volitelných parametrech `AcquireTokenInteractive`pro najdete v [referenční dokumentaci pro AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Získat tokeny prostřednictvím protokolu

Nedoporučujeme přímo používat protokol k získání tokenů. Pokud tak učiníte, aplikace nebude podporovat některé scénáře, které zahrnují jednotné přihlašování (SSO), správu zařízení a podmíněný přístup.

Když použijete protokol k získání tokenů pro mobilní aplikace, udělejte dva požadavky:

* Získání autorizačního kódu.
* Výměna kódu pro token

#### <a name="get-an-authorization-code"></a>Získání autorizačního kódu

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Získání přístupu a aktualizace tokenu

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-mobile-call-api.md)
