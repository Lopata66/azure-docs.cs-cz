---
title: 'Agent zřizování Azure AD Connect: Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny verze Azure AD Connect agenta zřizování a popisuje nové funkce a opravené problémy.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183240"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent zřizování Azure AD Connect: Historie verzí
V tomto článku jsou uvedené verze a funkce Azure Active Directory Connect zřizovacího agenta, který byl vydán. Tým Azure AD pravidelně aktualizuje agenta zřizování o nové funkce a funkce. Agent zřizování se aktualizuje automaticky, když se uvolní nová verze. 

Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stav verze

4\. prosince 2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Zahrnuje podporu [Azure AD Connectho zřizování cloudu](../cloud-provisioning/what-is-cloud-provisioning.md) pro synchronizaci dat uživatelů, kontaktů a skupin z místní služby Active Directory do Azure AD.


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stav verze

9\. září 2019: vydáno pro automatickou aktualizaci

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Možnost konfigurovat další trasování a protokolování pro problémy s agentem zřizování ladění
* Možnost načíst jenom ty atributy služby Azure AD, které jsou nakonfigurované v mapování, aby se zlepšil výkon synchronizace

### <a name="fixed-issues"></a>Oprava potíží

* Opravili jsme chybu, kterou agent přešel do nereagující na stav, pokud došlo k problémům s chybami připojení Azure AD.
* Opravili jsme chybu, která způsobila problémy při čtení binárních dat z Azure Active Directory
* Opravili jsme chybu, která agentovi selhala při obnovení vztahu důvěryhodnosti se službou Cloud Hybrid identity.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stav verze

23. ledna 2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přepracované zřizovacího agenta a architektury konektoru pro zajištění lepšího výkonu, stability a spolehlivosti 
* Zjednodušená konfigurace agenta zřizování pomocí Průvodce instalací na základě uživatelského rozhraní 
* Přidání podpory pro automatické aktualizace agentů

