---
title: Jak Azure AD používá protokol SAML | Dokumentace Microsoftu
description: Tento článek obsahuje přehled profily jednotného přihlašování a jednotné odhlašování SAML v Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35c231f043261b65baa40eff32535eeab014bee9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299919"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Jak Azure AD využívá protokol SAML

Azure Active Directory (Azure AD) pomocí SAML 2.0 protokol umožňují aplikacím poskytovat jednotné přihlašování svým uživatelům. [Single Sign-On](single-sign-on-saml-protocol.md) a [jedním odhlašování](single-sign-out-saml-protocol.md) SAML profilů služby Azure AD vysvětlují, jak používat kontrolní výrazy SAML, protokoly a vazby ve službě zprostředkovatele identity.

Protokol SAML vyžaduje zprostředkovatele identity (Azure AD) a poskytovatelem služeb (aplikace) k výměně informací o sami.

Když aplikace je zaregistrován ve službě Azure AD, vývojář aplikace registruje informací souvisejících s federační službou Azure AD. Tyto informace zahrnují **identifikátor URI pro přesměrování** a **metadat URI** aplikace.

Azure AD používá cloudovou službu **metadat URI** a získejte podpisový klíč a identifikátor URI odhlášení. Odběratele můžete otevřít aplikaci v **Azure AD -> Registrace aplikace** a potom v **Nastavení -> vlastnosti**, mohou aktualizovat odhlašovací adresa URL. Azure AD díky tomu můžete odeslat odpověď na správnou adresu URL. 

Azure Active Directory zpřístupňuje specifickým pro tenanta a běžné (nezávislé na tenanta) jednotné přihlašování a jednotné odhlašování koncové body. Tyto adresy URL představují adresovatelný umístění – nejsou jenom identifikátory – takže můžete přejít ke koncovému bodu ke čtení metadat.

* Koncový bod specifickým pro tenanta se nachází na `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  *\<Název_domény_tenanta >* zastupuje registrovaný název domény nebo identifikátor GUID ID Tenanta tenanta služby Azure AD. Je například federační metadata tenanta contoso.com, na: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislé na tenanta se nachází na `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Tuto adresu koncového bodu **běžné** se zobrazí místo název domény klienta nebo ID.

Informace o dokumentů metadat federace, které publikuje Azure AD najdete v tématu [federačních metadat](azure-ad-federation-metadata.md).
