---
title: Sada přesměrovaly na b2clogin.com – Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o použití b2clogin.com do vaší přesměrovat adresy URL pro Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8188e36278bad9c93f709a5d7d9f831d1c19e6b4
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486843"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Sada přesměrovaly na b2clogin.com pro Azure Active Directory B2C

Když nastavíte zprostředkovatele identity pro registraci a přihlašování v aplikaci Azure Active Directory (Azure AD) B2C, budete muset zadat adresu URL přesměrování. V minulosti bylo použito login.microsoftonline.com, teď by měl být použití b2clogin.com.

Použití b2clogin.com poskytuje další výhody, jako například:

- Nezabírá místo v záhlaví souboru cookie používané služby Microsoftu.
- Vaší adresy URL už nebude obsahovat odkaz na Microsoft. Například, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

>[!NOTE]
> Můžete název klienta a identifikátor GUID klienta následujícím způsobem:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` (stále odkazuje na `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid` (v takovém případě není žádná reference Microsoftu vůbec)
>
> Však nelze použít _vlastní doménu_ pro Azure Active Directory B2C tenanta, třeba `https://your-tenant-name.b2clogin.com/your-custom-domain-name` by _není_ fungovat.

Vezměte v úvahu tato nastavení, které může být nutné změnit při použití b2clogin.com:

- Nastavení přesměrování adresy URL ve vašich aplikacích zprostředkovatele identity použití b2clogin.com. 
- Nastavte aplikaci Azure AD B2C k použití b2clogin.com pro token koncových bodů a odkazy na tok uživatele. 
- Pokud používáte MSAL, je nutné nastavit **ValidateAuthority** vlastnost `false`.
- Ujistěte se, že změníte některá **povolené zdroje** , který jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Změna adresy URL pro přesměrování

Použití b2clogin.com, v nastavení pro vaši aplikaci zprostředkovatele identity, vyhledejte a změnit seznam důvěryhodných adres URL pro přesměrování zpět do Azure AD B2C.  V současné době máte pravděpodobně ji nastavit na přesměrovat zpět na některé login.microsoftonline.com lokality. 

Budete muset změnit adresu URL přesměrování tak, aby `your-tenant-name.b2clogin.com` autorizaci. Nezapomeňte nahradit `your-tenant-name` s názvem vaší Azure AD B2C tenanta a odeberte `/te` pokud existuje v adrese URL. Existují malé odchylky na tuto adresu URL pro každého zprostředkovatele identity proto zkontrolujte na odpovídající stránce zobrazíte přesnou adresu URL.

Informace o nastavení pro zprostředkovatele identity najdete v následujících článcích:

- [Účet Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Vlastní OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aktualizace aplikace

Aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com` na několika místech, jako jsou odkazy na tok uživatele a token koncových bodů.  Ujistěte se, že koncový bod autorizace, koncový bod tokenu a vystavitele byla aktualizována, aby používat `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Nastavte vlastnost ValidateAuthority

Pokud používáte MSAL, nastavte **ValidateAuthority** vlastnost `false`. Když **ValidateAuthority** je nastavena na `false`, b2clogin.com je povoleno přesměrování. 

Následující příklad ukazuje, jak může nastavit vlastnost:

V [MSAL pro .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

A v [MSAL pro jazyk Javascript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
