---
title: Služba Azure branou – zabezpečení vrstvy aplikace | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit, jak služba branou Azure umožňuje k ochraně a zabezpečení vašich back-EndY aplikací
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193913"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpečení vrstvy aplikace s branou
Služba Azure branou Service poskytuje možnost ochrany webové aplikace na trhu při ochraně webových aplikací před síťovými útoky a ohrožení zabezpečení běžnými jako jsou Injektáž SQL nebo různé lokality skriptování mezi weby (XSS). Povolené pro front endů http (s), přední dveře aplikace vrstva zabezpečení je globálně distribuovaná a vždy, útoky se zlými úmysly zastavení na Azure network hraniční, daleko od vaší back-EndY. Zvýšení zabezpečení a optimalizace výkonu branou nabízí rychlé a zabezpečené webové prostředí pro koncové uživatele.

## <a name="application-protection"></a>Ochrana aplikace
Přední dveře Ochrana aplikace je nakonfigurovaná na každé edge prostředí po celém světě podle aplikací a automaticky blokuje bez – provoz http (s) dosažení vaše webové aplikace. Naše distribuovaná architektura s více tenanty povolí globální ochranu ve velkém měřítku bez omezení výkonu. Úlohy http (s) a službu ochrany přední dveře webové aplikace poskytuje bohaté pravidla modul pro vlastní pravidla, předem nakonfigurované sady pravidel před běžnými útoky, a podrobné protokolování pro všechny požadavky, který odpovídá pravidlu. Povolit, včetně flexibilní akce bloku nebo protokol jsou podporovány pouze.

## <a name="custom-access-control-rules"></a>Pravidla pro řízení vlastní přístupu
- **IP povolit seznamu a seznam blokovaných položek:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě seznamu klientských IP adres. Jsou podporovány IP v4 a IP v6
- **Řízení geografické přístupu na základě:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě kódu země, IP adresa klienta byl vytvořen
- **Parametry protokolu HTTP filtrování:** Můžete nakonfigurovat pravidla vlastního přístupu na základě porovnání parametry požadavku HTTP (s), včetně záhlaví, adresy URL a řetězce dotazu

## <a name="azure-managed-rules"></a>Pravidla spravované v Azure
- Předem nakonfigurované sady pravidel proti známých chyb zabezpečení horní OWASP je standardně povolená. Ve verzi preview zahrnuje sadu pravidel sqli a xss žádostí o kontrolu. Přidá další pravidla. Můžete začít s jedinou akcí protokolu ověření předkonfigurovaných pravidel práce podle očekávání pro vaše aplikace 

## <a name="rate-limiting"></a>Omezení četnosti
- Pravidlo řízení míry je omezit neobvyklé vysoký provoz z jakékoli IP adresy klienta.  Můžete nastavit prahové hodnoty počtu webových požadavků umožněných IP adresa klienta po dobu jedné minuty.

## <a name="centralized-protection-policy"></a>Zásady centrální ochrany
- Můžete definovat několik pravidel ochrany a přidejte je do zásad v pořadí podle priority. Vlastní pravidla mají vyšší prioritu než spravovaná sady pravidel tak, aby výjimky. Jedna zásada je přidružená k webové aplikaci.  Zkontrolujte zásady konzistentní zabezpečení ve všech oblastech, stejné zásady ochrany aplikací web se replikují do všech hraničních serverech ve všech umístěních

## <a name="configuration"></a>Konfigurace
- Ve verzi preview můžete použít rozhraní REST API, Powershellu nebo rozhraní příkazového řádku k vytvoření a nasazení zásady a přední dveře pravidla ochrany aplikací. Přístup k portálu bude podporovat, než je služba obecně dostupná. 


## <a name="monitoring"></a>Monitorování
Přední dveře umožňuje monitorovat webové aplikace před útoky pomocí metrik v reálném čase, které jsou integrované s Azure Monitor pro sledování výstrah a snadnou identifikaci trendů.

## <a name="pricing"></a>Ceny
Přední dveře zabezpečení vrstvy aplikace je zdarma ve verzi preview.


## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
