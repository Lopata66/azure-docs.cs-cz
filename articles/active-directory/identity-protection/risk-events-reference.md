---
title: Azure Active Directory Identity Protection rizikem události – referenční informace | Dokumentace Microsoftu
description: Azure Active Directory Identity Protection rizikem události – referenční informace.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d009022d4ec03990d3ed0321ebcb13c72c674ed8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294204"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Azure Active Directory Identity Protection rizikem události – referenční informace

Většinu narušení zabezpečení se použijí při útočníci získají přístup k prostředí krádeží identity uživatele. Zjišťování ohrožení zabezpečení identit je žádný snadný úkol. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik ke zjištění podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každé zjištěné podezřelé akce je uložen v záznamu volané rizikové události.


## <a name="anonymous-ip-address"></a>Anonymní IP adresa

**Typ zjišťování:** V reálném čase  
**Starý název:** Přihlášení z anonymních IP adres


Tento typ rizikové události označuje přihlášení z anonymní IP adresy (například Tor prohlížeč, programu anonymizer VPN).
Tyto IP adresy jsou obvykle používány účastníky, kteří chtějí skrýt jejich přihlášení telemetrie (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivým činnostem.


## <a name="atypical-travel"></a>Neobvyklá cesta

**Typ zjišťování:** Offline  
**Starý název:** Nemožná cesta do netypických míst


Tento typ rizikové události identifikuje dvě přihlášení pocházející z geograficky vzdáleném umístění, kde aspoň jedno z umístění může být také netypických pro uživatele, daný za chování. Mezi několik dalších faktorů tohoto algoritmu strojového učení bere v úvahu čas mezi dvěma přihlášení a čas, kdy by trvalo pro uživatele k dopravě z první umístění do druhého, označující, že jiný uživatel používá stejný přihlašovací údaje.

Algoritmus ignoruje zřejmé "počet falešně pozitivních výsledků" přispívání do podmínky neuskutečnitelná cesta, například sítě VPN a umístění pravidelně používat další uživatelé v organizaci. Systém má období učení z nejdřívější z 14 dnů a 10 přihlášení, během kterých se naučí nového uživatele přihlásit chování.


## <a name="leaked-credentials"></a>Úniku přihlašovacích údajů

**Typ zjišťování:** Offline  
**Starý název:** Uživatelé s uniklými přihlašovacími údaji


Tento typ rizikové události označuje, že byl úniku platné přihlašovací údaje uživatele.
Když internetovým zločincům ohrozit platný hesla oprávněným uživatelům, pachatelů často sdílí své přihlašovací údaje. To se obvykle provádí zveřejněním je veřejně na tmavě web nebo vložit lokalit nebo obchodování nebo prodej přihlašovací údaje na černém trhu. Microsoft úniku přihlašovacích údajů služby získá uživatelské jméno / heslo dvojice monitorování veřejné a tmavé webů a práce s:

- Výzkumní pracovníci

- Vymáhání zákona

- Zabezpečení týmů v Microsoftu

- Jiných důvěryhodných zdrojů

Když služba získá přihlašovací údaje uživatele z dark web, vložit weby nebo výše uvedených zdrojů, jsou porovnávána s aktuální platná pověření uživatelů Azure AD se najít platný shody.


## <a name="malware-linked-ip-address"></a>IP adresy související s malwarem

**Typ zjišťování:** Offline  
**Starý název:** Přihlášení z nakažených zařízení


Tento typ rizikové události označuje přihlášení z IP adresy napadený malwarem, který je známý aktivně komunikovat se serverem robota. To se určují podle korelace IP adresy zařízení uživatele s IP adresami, které byly kontaktu s bot serveru, zatímco bot server byl aktivní.


## <a name="unfamiliar-sign-in-properties"></a>Neznámé vlastnosti přihlášení

**Typ zjišťování:** V reálném čase  
**Starý název:** Přihlášení z neznámých míst

Tento typ rizikové události bere v úvahu minulé historie přihlašování (IP, zeměpisné šířky / délky a čísla ASN) k vyhledání neobvyklou přihlašovací aktivitou. Systém uchovává informace o předchozí umístění použitá uživatelem a bere v úvahu tyto "známé" umístění. Riziková událost se aktivuje, dojde li přihlásit z umístění, které již není v seznamu známých umístění. Nově vytvořený uživatelé budou v "režimu learning" pro určitou dobu v neznámého přihlášení vlastnosti, které rizikové události se vypne při naše algoritmy přečtěte si chování uživatele. Studijní doba trvání režimu je dynamický a závisí na tom, jak dlouho trvá algoritmus shromáždit dostatek informací o vzory přihlašování uživatele. Minimální doba trvání je pět dní. Uživatele můžete přejít zpět do režimu učení po delší době nečinnosti. Systém také ignoruje přihlášení ze známé zařízení a umístění, které jsou geograficky umístěné blízko známé umístění. 

Můžeme také spustit toto zjišťování pro základní ověřování (nebo starších verzí protokolů). Protože tyto protokoly nejsou moderní vlastnosti, jako je ID klienta, je omezený telemetrických dat do snížil počet falešných poplachů. Doporučujeme, aby naše zákazníky, přesunout do moderního ověřování.

