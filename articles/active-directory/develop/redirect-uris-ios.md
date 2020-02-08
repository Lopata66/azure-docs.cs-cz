---
title: Použití identifikátorů URI pro přesměrování s MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi Microsoft Authentication Library for ObjectiveC (MSAL for iOS and macOS) a s knihovnou ověřování Azure AD pro ObjectiveC (ADAL. ObjC) a postup migrace mezi nimi.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085638"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Použití identifikátorů URI pro přesměrování s knihovnou Microsoft Authentication Library pro iOS a macOS

Když se uživatel ověří, Azure Active Directory (Azure AD) pošle token do aplikace pomocí identifikátoru URI přesměrování zaregistrovaného v aplikaci Azure AD.

Knihovna Microsoft Authentication Library (MSAL) vyžaduje, aby identifikátor URI přesměrování byl zaregistrován v aplikaci Azure AD v konkrétním formátu. MSAL používá výchozí identifikátor URI přesměrování, pokud ho nezadáte. Formát je `msauth.[Your_Bundle_Id]://auth`.

Výchozí formát identifikátoru URI pro přesměrování funguje pro většinu aplikací a scénářů, jako je například zprostředkované ověřování a systémové webové zobrazení. Pokud je to možné, použijte výchozí formát.

V případě pokročilých scénářů ale možná budete muset změnit identifikátor URI přesměrování, jak je popsáno níže.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scénáře, které vyžadují jiný identifikátor URI pro přesměrování

### <a name="cross-app-single-sign-on-sso"></a>Jednotné přihlašování mezi aplikacemi (SSO)

Aby mohla platforma Microsoft Identity sdílet tokeny napříč aplikacemi, musí mít každá aplikace stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který jste zadali při registraci aplikace na portálu (ne ID sady prostředků aplikace, které zaregistrujete na aplikaci pomocí Applu).

Identifikátory URI pro přesměrování musí být pro každou aplikaci pro iOS odlišné. To umožňuje službě Microsoft Identity jednoznačně identifikovat různé aplikace, které sdílejí ID aplikace. Každá aplikace může mít v Azure Portal zaregistrováno více identifikátorů URI pro přesměrování. Každá aplikace v sadě bude mít jiný identifikátor URI pro přesměrování. Příklad:

Při následující registraci aplikace v Azure Portal:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 používá přesměrování `msauth.com.contoso.app1://auth` app2 používá `msauth.com.contoso.app2://auth` APP3 používá `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrace z knihovny ADAL na MSAL

Při migraci kódu, který používal knihovnu ověřování Azure AD (ADAL) na MSAL, už možná máte pro vaši aplikaci nakonfigurovanou identifikátor URI pro přesměrování. Stejný identifikátor URI pro přesměrování můžete dál používat, pokud byla aplikace ADAL nakonfigurovaná tak, aby podporovala zprostředkované scénáře, a váš identifikátor URI přesměrování splňuje požadavky formátu URI MSAL přesměrování.

## <a name="msal-redirect-uri-format-requirements"></a>Požadavky formátu identifikátoru URI pro přesměrování MSAL

* Identifikátor URI pro přesměrování MSAL musí být ve tvaru `<scheme>://host`

    Kde `<scheme>` je jedinečný řetězec, který identifikuje vaši aplikaci. Primárně se vychází z identifikátoru sady prostředků vaší aplikace, aby se zaručila jedinečnost. Pokud je třeba ID sady `com.contoso.myapp`vaší aplikace, váš identifikátor URI přesměrování by měl být ve formátu: `msauth.com.contoso.myapp://auth`.

    Pokud migrujete z knihovny ADAL, váš identifikátor URI pro přesměrování bude pravděpodobně mít tento formát: `<scheme>://[Your_Bundle_Id]`, kde `scheme` je jedinečný řetězec. Tento formát bude fungovat i v případě, že použijete MSAL.

* `<scheme>` musí být v souboru info. plist vaší aplikace zaregistrované v části `CFBundleURLTypes > CFBundleURLSchemes`.  V tomto příkladu byl otevřen info. plist jako zdrojový kód:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL ověří, zda identifikátor URI přesměrování správně zaregistruje, a vrátí chybu, pokud není.
    
* Pokud chcete použít univerzální odkazy jako identifikátor URI přesměrování, `<scheme>` musí být `https` a nemusí být deklarované v `CFBundleURLSchemes`. Místo toho nakonfigurujte [pro vývojáře](https://developer.apple.com/ios/universal-links/) aplikaci a doménu na pokyny společnosti Apple a zavolejte metodu `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication`, když se vaše aplikace otevírá prostřednictvím univerzálního odkazu.

## <a name="use-a-custom-redirect-uri"></a>Použít vlastní identifikátor URI pro přesměrování

Chcete-li použít vlastní identifikátor URI pro přesměrování, předejte parametr `redirectUri`, aby `MSALPublicClientApplicationConfig` a předával tento objekt do `MSALPublicClientApplication` při inicializaci objektu. Pokud je identifikátor URI přesměrování neplatný, inicializátor vrátí `nil` a nastaví `redirectURIError`s dalšími informacemi.  Příklad:

Cíl-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Zpracovat událost otevření adresy URL

Vaše aplikace by měla volat MSAL, když přijme jakoukoli odpověď prostřednictvím schémat adres URL nebo univerzálních odkazů. Při otevření aplikace zavolejte metodu `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication`. Tady je příklad pro vlastní schémata:

Cíl-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
