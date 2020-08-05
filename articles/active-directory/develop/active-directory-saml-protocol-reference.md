---
title: Jak Microsoft Identity Platform používá protokol SAML
description: Tento článek poskytuje přehled profilů SAML jednotného přihlašování a jednotného přihlašování v Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552794"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Jak Microsoft Identity Platform používá protokol SAML

Platforma Microsoft Identity Platform používá protokol SAML 2,0 k tomu, aby aplikacím poskytovaly jednotné přihlašování svým uživatelům. Profily SAML [jednotného přihlašování](single-sign-on-saml-protocol.md) a [jednotné](single-sign-out-saml-protocol.md) PŘIhlašování v Azure AD vysvětlují, jak se ve službě zprostředkovatele identity používají kontrolní výrazy SAML, protokoly a vazby.

Protokol SAML vyžaduje pro výměnu informací o samy poskytovatele identity (Microsoft Identity Platform) a poskytovatele služeb (aplikace).

Když je aplikace zaregistrovaná ve službě Azure AD, vývojář aplikace registruje informace týkající se federace ve službě Azure AD. Tyto informace zahrnují **identifikátor URI přesměrování** a **identifikátor URI metadat** aplikace.

Platforma Microsoft Identity Platform používá **identifikátor URI metadat** cloudové služby k načtení podpisového klíče a identifikátoru URI pro odhlášení. Zákazník může aplikaci otevřít ve **službě Azure AD – > registraci aplikace** a pak v **Nastavení-> vlastnosti**může aktualizovat adresu URL pro odhlášení. Tímto způsobem může Microsoft Identity Platform odeslat odpověď na správnou adresu URL. 

Azure Active Directory zveřejňuje jednotné přihlašování (nezávislé na klientovi) a koncové body s jedním odhlášením pro konkrétního tenanta. Tyto adresy URL představují adresovatelná umístění – nejedná se o pouze identifikátory – takže můžete načíst metadata z tohoto koncového bodu.

* Koncový bod specifický pro tenanta je umístěný na adrese `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . *\<TenantDomainName>* Zástupný symbol představuje registrovaný název domény nebo identifikátor GUID TenantID tenanta Azure AD. Federační metadata klienta contoso.com jsou například v:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislý na klientovi je umístěný na adrese `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . V této adrese koncového bodu se **běžně** zobrazuje název domény klienta nebo ID.

Informace o dokumentech federačních metadat, které zveřejňuje Azure AD, najdete v tématu [federační metadata](../azuread-dev/azure-ad-federation-metadata.md).
