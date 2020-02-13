---
title: Vývoj platformy Microsoft Identity Platform – Azure
description: Přečtěte si o platformě Microsoft Identity Platform a vývoji služby identity Azure Active Directory (Azure AD) a vývojářské platformy.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 3c18440e87cf50e370b21b5f0ca33b42e0069fc1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161422"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Rozvoj platformy Microsoft Identity Platform

Platforma identit Microsoftu vznikla z platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům sestavovat aplikace, které přihlásí uživatele, získat tokeny pro volání rozhraní API, například Microsoft Graph nebo rozhraní API, které vývojáři sestavili. Skládá se ze služby ověřování, open source knihoven, registrace aplikací a konfigurace (prostřednictvím portálu pro vývojáře a rozhraní API aplikace), úplné dokumentace pro vývojáře, ukázek pro rychlý Start, ukázek kódu, kurzů a návody a Další obsah pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

Až do té doby se většina vývojářů pracovala s platformou Azure AD v 1.0, aby ověřovala pracovní a školní účty (zřízené službou Azure AD) tím, že požaduje tokeny z koncového bodu Azure AD v 1.0 pomocí knihovny ADAL (Azure AD Authentication Library), Azure Portal pro registrace a konfigurace aplikací a Graph API služby Azure AD pro konfiguraci programových aplikací.

Díky jednotné platformě Microsoft identity (v 2.0) můžete napsat kód jednou a ověřit jakoukoli identitu Microsoftu do své aplikace. Pro několik platforem se pro použití s koncovými body platformy identity doporučuje plně podporovaná knihovna MSAL (Open Source Microsoft Authentication Library). MSAL se snadno používá, nabízí uživatelům Skvělé prostředí jednotného přihlašování (SSO) pro vaše uživatele, pomáhá dosahovat vysoké spolehlivosti a výkonu a vyvíjí se pomocí SDL (Microsoft Secure Development Lifecycle). Při volání rozhraní API můžete svou aplikaci nakonfigurovat tak, aby využívala výhod přírůstkového souhlasu, která umožňuje zpozdit žádost o souhlas s více invazivními rozsahy, dokud použití aplikace neopravňuje k tomu za běhu.  MSAL také podporuje Azure Active Directory B2C, takže vaši zákazníci používají své preferované sociální, podnikové nebo místní identity účtu k získání přístupu k aplikacím a rozhraním API pomocí jednotného přihlašování.

S platformou Microsoft Identity můžete rozšířit svůj přístup k těmto typům uživatelů:

- Pracovní a školní účty (účty zřízené Azure AD)
- Osobní účty (například Outlook.com nebo Hotmail.com)
- Vaši zákazníci, kteří přinášejí vlastní e-mail nebo sociální identitu (například LinkedIn, Facebook, Google) prostřednictvím MSAL a Azure AD B2C

Azure Portal můžete použít k registraci a konfiguraci aplikace a k použití rozhraní Microsoft Graph API pro konfiguraci programových aplikací.

Aktualizujte svou aplikaci vlastním tempem. Aplikace vytvořené pomocí knihoven ADAL budou nadále podporovány. Podporují se i smíšené portfolia aplikací, které se skládají z aplikací vytvořených pomocí ADAL a aplikací vytvořených pomocí knihoven MSAL. To znamená, že aplikace používající nejnovější ADAL a nejnovější MSAL budou v rámci portfolia poskytovat jednotné přihlašování, které poskytuje mezipaměť sdíleného tokenu mezi těmito knihovnami. Aplikace aktualizované z ADAL na MSAL budou při upgradu udržovat stav přihlášení uživatele.

## <a name="microsoft-identity-platform-experience"></a>Používání platformy Microsoft Identity Platform

Na následujícím schématu vidíte používání identit Microsoftu na vysoké úrovni včetně použití registrace aplikací, sad SDK, koncových bodů a podporovaných identit.

![Platforma Microsoft Identity Platform dnes](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Prostředí pro registraci aplikací

Prostředí Azure Portal **[Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)** je prostředí s jedním portálem pro správu všech aplikací, které jste integrací s platformou Microsoft identity. Pokud jste používali portál pro registraci aplikací, začněte místo toho používat prostředí pro registraci aplikací Azure Portal.

Pro integraci s Azure AD B2C (při ověřování sociálních nebo místních identit) budete muset aplikaci zaregistrovat v tenantovi Azure AD B2C. Toto prostředí je také součástí Azure Portal.

**Rozhraní API aplikace v Microsoft Graph** je aktuálně ve verzi Preview. Pomocí tohoto rozhraní API můžete programově nakonfigurovat aplikace integrované s Microsoft Identity platformou pro ověřování jakékoli identity Microsoftu. Nicméně dokud toto rozhraní API nedosáhne obecné dostupnosti, měli byste použít rozhraní Azure AD Graph 1,6 API a manifest aplikace.

### <a name="msal-libraries"></a>Knihovny MSAL

Pomocí knihovny MSAL můžete sestavovat aplikace, které ověřují všechny identity Microsoftu. Knihovny MSAL v rozhraní .NET a JavaScriptu jsou všeobecně dostupné. Knihovny MSAL pro iOS a Android jsou ve verzi Preview a jsou vhodné pro použití v produkčním prostředí. Poskytujeme stejnou podporu na úrovni výroby pro knihovny MSAL ve verzi Preview, a to pro verze MSAL a ADAL, které jsou všeobecně dostupné.

Pomocí knihoven MSAL můžete také integrovat aplikaci s Azure AD B2C.

Jsou všeobecně dostupné knihovny na straně serveru pro vytváření webových aplikací a webových rozhraní API: [ASP.NET](https://docs.microsoft.com/aspnet/overview) a [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Koncový bod platformy Microsoft identity

Koncový bod Microsoft Identity Platform (v 2.0) je teď OIDC certifikovaný. Funguje s knihovnami Microsoft Authentication Library (MSAL) nebo jinými knihovnami kompatibilními s normami. V souladu s oborovým standardem implementuje lidské čitelné rozsahy.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o verzích v1.0 a v2.0.

* [Microsoft Identity Platform (v 2.0) – přehled](v2-overview.md)
* [Přehled Azure Active Directory pro vývojáře (v 1.0)](../azuread-dev/v1-overview.md)
