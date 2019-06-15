---
title: Vyřešte chybu "Se tam nelze dostat z tohoto" – Azure Active Directory | Dokumentace Microsoftu
description: Řešení potíží s potenciální důvody, které se vám chybová zpráva "Se tam nelze dostat z tohoto".
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a317680a39d4594aacdf84ccdf963bb84bfbf07b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473774"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>Možné důvody chybová zpráva "Se tam nelze dostat z tohoto"
Při přístupu k interním webu aplikacím nebo službám vaší organizace, může se zobrazit chybová zpráva s upozorněním, **se tam nelze dostat z tohoto**. Tato zpráva znamená, že vaše organizace má zavést zásady zařízení brání v přístupu k prostředkům vaší organizace. Zatímco může skončit by bylo nutné kontaktovat Helpdesk chcete tento problém vyřešit, tady je pár věcí, které můžete nejdřív vyzkoušet.

## <a name="make-sure-youre-using-a-supported-browser"></a>Ujistěte se, že používáte podporovaný prohlížeč
Pokud se zobrazí **se tam nelze dostat z tohoto** zpráva s oznámením, že se pokoušíte přistupovat k serverům vaší organizace z nepodporované prohlížeče, zkontrolujte webového prohlížeče spouštíte.

![Chybová zpráva týkající se podpora prohlížeče](media/user-help-device-remediation/browser-version.png)

Chcete-li tento problém vyřešit, musíte nainstalovat a spustit podporovaného prohlížeče podle vašeho operačního systému. Pokud používáte Windows 10, zahrnují podporované prohlížeče Microsoft Edge, Internet Exploreru a Google Chrome. Pokud používáte jiný operační systém, můžete zkontrolovat na úplný seznam [podporovaných prohlížečích](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Ujistěte se, že používáte podporovaný operační systém
Ujistěte se, že používáte podporovanou verzi operačního systému, včetně:

- **Klient Windows.** Windows 7 nebo novější.

- **Windows Server.** Windows Server 2008 R2 nebo novější.

- **macOS.** macOS X nebo novější

- **Zařízení s androidem a iOS.** Nejnovější verzi Androidu a iOS mobilních operačních systémů

Chcete-li tento problém vyřešit, musíte nainstalovat a spustit podporovaný operační systém.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Ujistěte se, že je zařízení připojené k síti
Pokud se zobrazí **se tam nelze dostat z tohoto** zpráva s oznámením, že vaše zařízení je mimo dodržování předpisů se zásadami vaší organizace přístup, ujistěte se, že vaše zařízení připojili k síti vaší organizace.

![Chybová zpráva týkající se, jestli jste v síti](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Chcete-li zkontrolovat, jestli je zařízení připojené k síti
1. Přihlaste se k Windows pomocí svého pracovního nebo školního účtu. Například, alain@contoso.com.

2. Připojení k síti vaší organizace pomocí virtuální privátní sítě (VPN) nebo technologie DirectAccess.

3. Jakmile budete hotovi, stiskněte **Windows logo klíče + L** k uzamčení zařízení.

4. Odemkněte zařízení pomocí svého pracovního nebo školního účtu a zkuste přístup k problematické aplikaci nebo službu znovu.

    Pokud se zobrazí **se tam nelze dostat z tohoto** chybovou zprávu znovu, vyberte **podrobnosti** propojení a obraťte se na Helpdesk s podrobnostmi.

### <a name="to-join-your-device-to-your-network"></a>Připojení zařízení k síti
Pokud zařízení není připojené k síti vaší organizace, můžete provést jeden ze dvou kroků:

- **Připojení pracovních zařízení.** Připojte se k zařízení s Windows 10 vlastněných společností práce k síti vaší organizace, budete mít přístup k prostředkům potenciálně s omezeným přístupem. Další informace a podrobné pokyny najdete v tématu [připojení pracovních zařízení k síti vaší organizace](user-help-join-device-on-network.md).

- **Zaregistrujte své osobní zařízení pro práci.** Registrovat své osobní zařízení, obvykle telefon nebo tablet, v síti vaší organizace. Po registraci vašeho zařízení má přístup k prostředkům vaší organizace s omezeným přístupem. Další informace a podrobné pokyny najdete v tématu [registraci osobních zařízení v síti vaší organizace](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Další postup
- [Co je na portálu MyApps?](active-directory-saas-access-panel-introduction.md)

- [Přihlášení pomocí telefonu, ne pomocí hesla](user-help-auth-app-sign-in.md)
