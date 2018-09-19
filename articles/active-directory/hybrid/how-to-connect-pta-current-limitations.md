---
title: 'Služby Azure AD Connect: Předávací ověřování - aktuální omezení | Dokumentace Microsoftu'
description: Tento článek popisuje aktuální omezení předávací ověřování služby Azure Active Directory (Azure AD)
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: acb3297b8d7af1a91563fb2f519e06bc49c65695
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314619"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure předávací ověřování služby Active Directory: Aktuální omezení

>[!IMPORTANT]
>Předávací ověřování Azure Active Directory (Azure AD) je bezplatná funkce a není nutné žádné placené edice Azure AD, aby ho použít. Předávací ověřování je k dispozici v celosvětové instance služby Azure AD a ne v pouze [cloudu Microsoft Azure Germany](http://www.microsoft.de/cloud-deutschland) nebo [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Podporované scénáře

Jsou podporovány následující scénáře:

- Přihlášení uživatelů do webové aplikace založené na prohlížeči.
- Přihlášení uživatelů do Outlookové klienty pomocí starší verze protokoly, například Exchange ActiveSync, EAS, SMTP, POP a IMAP.
- Přihlášení uživatele do starší klientské aplikace Office a aplikace Office, které podporují [moderní ověřování](https://aka.ms/modernauthga): verze Office 2010, 2013 a 2016.
- Přihlašování uživatelů na starší verzi protokolu aplikace, jako je PowerShell verze 1.0 a další.
- Azure AD připojí pro zařízení s Windows 10.
- Hesla aplikací pro ověřování službou Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře jsou _není_ podporovány:

- Zjišťování uživatelů s [úniku přihlašovacích údajů](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Azure AD Domain Services musí být na tenantovi povolena synchronizaci hodnot Hash hesel. Proto tenanty, které používají předávací ověřování _pouze_ nefungují pro scénáře, které je třeba Azure AD Domain Services.
- Předávací ověřování není integrován s [Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Jako alternativní řešení pro nepodporované scénáře _pouze_ (s výjimkou integrace Azure AD Connect Health), povolte na synchronizaci hodnot Hash hesel [volitelné funkce](how-to-connect-install-custom.md#optional-features) stránky v průvodci službou Azure AD Connect.

>[!NOTE]
Povolení synchronizace hodnot Hash hesel nabízí možnost převzetí služeb při selhání ověřování je-li přerušit vaši místní infrastrukturu. Toto převzetí služeb při selhání z předávací ověřování na synchronizaci hodnot Hash hesel nebude automatická. Budete potřebovat přepnout metodu přihlašování ručně pomocí služby Azure AD Connect. Pokud server se službou Azure AD Connect ocitne mimo provoz, bude vyžadovat pomoc od společnosti Microsoft Support vypnout předávací ověřování.

## <a name="next-steps"></a>Další postup
- [Rychlý start](how-to-connect-pta-quick-start.md): uvedení do provozu pomocí předávacího ověřování Azure AD.
- [Migrace ze služby AD FS na předávací ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) – podrobné pokyny k migraci ze služby AD FS (nebo jiné technologie federation) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení ve svém tenantovi k ochraně uživatelské účty.
- [Podrobné technické informace](how-to-connect-pta-how-it-works.md): pochopit, jak funguje funkce předávací ověřování.
- [Nejčastější dotazy k](how-to-connect-pta-faq.md): Najděte odpovědi na nejčastější dotazy týkající se funkcí předávací ověřování.
- [Řešení potíží s](tshoot-connect-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): získáte podrobné technické informace o funkci předávací ověřování.
- [Azure AD bezproblémového jednotného přihlašování k](how-to-connect-sso.md): Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fórum Azure Active Directory do souboru žádostí o nové funkce.
