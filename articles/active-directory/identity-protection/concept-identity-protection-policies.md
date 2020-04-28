---
title: Zásady Azure AD Identity Protection
description: Identifikace tří zásad, které jsou povolené pomocí Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "72887413"
---
# <a name="identity-protection-policies"></a>Zásad ochrany identit

Azure Active Directory Identity Protection obsahuje tři výchozí zásady, které mohou správci vybrat k povolení. Tyto zásady zahrnují omezené přizpůsobení, ale platí pro většinu organizací. Všechny zásady umožňují vyloučit uživatele, jako jsou například účty pro [nouzový přístup nebo správce přestávek](../users-groups-roles/directory-emergency-access.md).

![Zásad ochrany identit](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Zásady registrace pro Azure MFA

Identity Protection může organizacím pomoci při zavádění Azure Multi-Factor Authentication (MFA) pomocí zásad podmíněného přístupu, které vyžadují registraci při přihlášení. Povolení těchto zásad je skvělým způsobem, jak zajistit, aby noví uživatelé ve vaší organizaci měli svůj první den zaregistrovaným pro MFA. Multi-Factor Authentication je jednou z metod pro automatické opravy rizikových událostí v rámci Identity Protection. Vlastní náprava umožňuje uživatelům provádět akce podle svých vlastních potřeb pro omezení objemu volání helpdesku.

Další informace o Azure Multi-Factor Authentication najdete v článku [jak to funguje: azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Zásady rizik přihlašování

Identity Protection analyzuje signály od každého přihlášení, v reálném čase i v režimu offline a vypočítá skóre rizika na základě pravděpodobnosti, že se přihlášení neuskutečnilo uživatelem. Správci mohou rozhodnout na základě tohoto signálu pro hodnocení rizik vynutilit požadavky organizace. Správci se můžou rozhodnout blokovat přístup, povolí přístup nebo povolí přístup, ale vyžadují službu Multi-Factor Authentication.

Pokud se zjistí riziko, můžou uživatelé provádět ověřování pomocí služby Multi-Factor Authentication a odstranit událost rizikového přihlašování a zabránit tak zbytečnému hluku správcům.

> [!NOTE] 
> Před aktivací zásad pro rizikové přihlašování musí být uživatelé předtím registrovaní pro Azure Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Vlastní zásady podmíněného přístupu

Správci se také můžou rozhodnout vytvořit vlastní zásady podmíněného přístupu, včetně rizika přihlášení jako podmínky přiřazení. Další informace o podmíněném přístupu najdete v článku [co je podmíněný přístup?](../conditional-access/overview.md)

![Vlastní zásady rizikového přihlášení pro podmíněný přístup](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Zásady rizik uživatelů

Identity Protection může vypočítat, co se považuje za běžné pro chování uživatele a použít ho k základnímu rozhodování o jejich riziku. Riziko uživatele je výpočet pravděpodobnosti ohrožení identity. Správci mohou rozhodnout na základě tohoto signálu pro hodnocení rizik vynutilit požadavky organizace. Správci se můžou rozhodnout blokovat přístup, povolí přístup nebo povolí přístup, ale vyžadují změnu hesla pomocí [samoobslužného resetování hesla služby Azure AD](../authentication/howto-sspr-deployment.md).

Pokud se zjistí riziko, můžou uživatelé provést Samoobslužné resetování hesla, aby se opravily samy a zavřeli událost rizika uživatele, aby nedocházelo k zbytečnému hluku správcům.

> [!NOTE] 
> Před aktivací zásad rizika uživatele musí být uživatelé předtím registrováni pro Samoobslužné resetování hesla.

## <a name="next-steps"></a>Další kroky

- [Povolit Samoobslužné resetování hesla služby Azure AD](../authentication/howto-sspr-deployment.md)

- [Povolení služby Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Povolit zásady registrace pro Azure Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Povolit přihlašování a zásady rizik uživatelů](howto-identity-protection-configure-risk-policies.md)
