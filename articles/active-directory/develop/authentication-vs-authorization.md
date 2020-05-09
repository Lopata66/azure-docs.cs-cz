---
title: Ověřování oproti autorizaci | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se se základy ověřování a autorizace v platformě Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b905b8f3fe99b83bafdd61b1daa25549354c5275
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926778"
---
# <a name="authentication-vs-authorization"></a>Ověřování vs. autorizace

Tento článek definuje ověřování a autorizaci a stručně popisuje, jak můžete pomocí platformy Microsoft Identity ověřovat a autorizovat uživatele ve webových aplikacích, webových rozhraních API nebo aplikacích, které volají chráněná webová rozhraní API. Pokud se vám zobrazí termín, který neznáte, vyzkoušejte si náš [Glosář](developer-glossary.md) nebo naše [videa Microsoftu pro identitu platforem](identity-videos.md) , která se týkají základních konceptů.

## <a name="authentication"></a>Ověřování

**Ověřování** je proces prokazující, na koho jste se rozhodli. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN. Platforma Microsoft Identity Platform implementuje protokoly [OpenID Connect](https://openid.net/connect/) a [SAML 2,0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html) pro zpracování ověřování.

## <a name="authorization"></a>Autorizace

**Autorizace** je způsob, jakým se uděluje oprávnění ověřené strany k tomu, aby něco projednalo. Určuje, jaká data máte povolený přístup, a co s nimi můžete dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ. Platforma Microsoft Identity Platform implementuje protokol [OAuth 2,0](https://oauth.net/2/) pro zpracování autorizace.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Ověřování a autorizace pomocí platformy Microsoft Identity Platform

Místo vytváření aplikací, které každý z nich zachovávají vlastní uživatelské jméno a heslo, což má za následek vysokou administrativní zátěž, když potřebujete přidat nebo odebrat uživatele napříč více aplikacemi, můžou aplikace delegovat tuto odpovědnost na centralizovaného zprostředkovatele identity.

Azure Active Directory (Azure AD) je centralizovaný poskytovatel identity v cloudu. Delegování ověřování a autorizace do IT umožňuje scénáře, jako jsou zásady podmíněného přístupu, které vyžadují, aby uživatel byl v konkrétním umístění, použití služby Multi-Factor Authentication a aby se uživatel mohl přihlásit a pak se automaticky přihlásil ke všem webovým aplikacím, které sdílejí stejný centralizovaný adresář. Tato funkce se označuje jako **jednotné přihlašování (SSO)**.

Microsoft Identity Platform usnadňuje ověřování a autorizaci pro vývojáře aplikací tím, že poskytuje identitu jako službu a podporuje standardní protokoly jako OAuth 2,0, OpenID Connect a SAML 2,0 a také open source knihovny pro různé platformy, které vám pomůžou rychle začít vytvářet kódování. Umožňuje vývojářům sestavovat aplikace, které přihlásí všechny identity Microsoftu, získat tokeny pro volání [Microsoft Graph](https://developer.microsoft.com/graph/), jiná rozhraní API Microsoftu nebo rozhraní API, která vývojáři sestavili. Další informace najdete v tématu [vývoj platformy Microsoft Identity Platform](about-microsoft-identity-platform.md).

Následuje krátké porovnání různých protokolů používaných platformami Microsoft Identity Platform:

* **OAuth vs. OpenID Connect**: pro autorizaci se používá OAuth a OpenID Connect (OIDC) se používá k ověřování. OpenID Connect je postavená na OAuth 2,0, takže terminologie a tok jsou mezi nimi podobné. Můžete dokonce ověřit uživatele (pomocí OpenID Connect) a získat autorizaci pro přístup k chráněnému prostředku, který uživatel vlastní (pomocí OAuth 2,0) v jednom požadavku. Další informace najdete v tématu [protokoly OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md) a [protokol OpenID Connect](v2-protocols-oidc.md).
* **OAuth vs. SAML**: protokol OAuth se používá pro autorizaci a pro ověřování se používá SAML. Další informace o tom, jak se tyto dva protokoly dají použít společně k ověření uživatele (pomocí SAML), najdete v tématu [Microsoft Identity Platform a OAuth 2,0 Flow – tok kontrolního výrazu SAML](v2-saml-bearer-assertion.md) . získáte tak autorizaci pro přístup k chráněnému prostředku (pomocí OAuth 2,0).
* **OpenID Connect vs. SAML**: k ověření uživatele se používají OpenID Connect i SAML a používají se k povolení jednotného přihlašování. Ověřování SAML se běžně používá u zprostředkovatelů identity, jako je Active Directory Federation Services (AD FS) (ADFS) federované do Azure AD, a je proto často používané v podnikových aplikacích.

## <a name="next-steps"></a>Další kroky

Další témata týkající se ověřování a základů autorizace:

* V tématu [tokeny zabezpečení](security-tokens.md) se dozvíte, jak se v ověřování a autorizaci používají přístupové tokeny, aktualizují tokeny a tokeny ID.
* V článku [aplikační model](application-model.md) se dozvíte o procesu registrace aplikace, aby se mohla integrovat s platformou Microsoft identity.
* V tématu [Flow pro přihlášení k aplikaci](app-sign-in-flow.md) se dozvíte o procesu přihlašování webových, desktopových a mobilních aplikací na platformě Microsoft identity.

Další informace o protokolech, které Microsoft Identity Platform implementuje:

* Další informace o standardech OpenID Connect a OAuth 2,0 najdete v tématu [protokoly oauth 2,0 a OpenID Connect na platformě Microsoft Identity](active-directory-v2-protocols.md) .
* Další informace o tom, jak platforma Microsoft identity podporuje jednotné přihlašování, najdete v tématu [protokol SAML jednotného přihlašování](single-sign-on-saml-protocol.md) .
