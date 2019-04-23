---
title: Protokoly ověřování pro Azure Active Directory | Dokumentace Microsoftu
description: Přehled ověřování protokolů podporovaných službou Azure Active Directory (AD)
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 937aa5d5874ff2915bfb51d289bfb1aac44a0530
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300525"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protokoly ověřování pro Azure Active Directory
Azure Active Directory (Azure AD) podporuje některé z nejčastěji používaných ověřovací a autorizační protokoly. Témata v této části popisují podporované protokoly a jejich provádění ve službě Azure AD. V tématech zahrnuté kontrolu podporované typy deklarací identity, úvod do používání federačních metadat, podrobné OAuth 2.0. a referenční dokumentaci protokol SAML 2.0 a část s řešením potíží.

## <a name="authentication-protocols-articles-and-reference"></a>Ověřovací protokoly, články a referenční informace
* [Důležité informace o podpisový klíč výměny ve službě Azure AD](active-directory-signing-key-rollover.md) – přečtěte si víc o Azure AD podepisování výměny podpisových klíčů, změny, můžete provádět automaticky aktualizovat klíč a diskusi k tom, jak aktualizovat většině běžných scénářů aplikace.
* [Podporované typy deklarací identity a Token](v1-id-and-access-tokens.md) – Další informace o deklarace identity do tokenů, které Azure AD vydá.
* [Federační Metadata](azure-ad-federation-metadata.md) – zjistěte, jak najít a interpretovat dokumentů metadat, která generuje Azure AD.
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md) – Další informace o provádění OAuth 2.0 ve službě Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) – Další informace o použití autorizační protokol OAuth 2.0 pro ověřování.
* [Volání mezi službami s přihlašovacími údaji klientů](v1-oauth2-client-creds-grant-flow.md) – zjistěte, jak použít tok udělování přihlašovacích údajů klienta OAuth 2.0 pro volání mezi službami.
* [Volání mezi službami Flow On-Behalf-Of](v1-oauth2-on-behalf-of-flow.md) – zjistěte, jak tok OAuth 2.0 On-Behalf-Of použít pro volání mezi službami.
* [Referenční informace o protokolu SAML](active-directory-saml-protocol-reference.md) – Další informace o profilech, jednotné přihlašování a jednotné odhlašování SAML služby Azure AD.

## <a name="see-also"></a>Viz také
[Příručka pro vývojáře Azure Active Directory](v1-overview.md)

[Ukázky kódu služby Active Directory](sample-v1-code.md)
