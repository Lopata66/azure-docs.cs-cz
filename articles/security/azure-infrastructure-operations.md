---
title: Správa produkční sítě Azure – Microsoft Azure
description: Tento článek popisuje, jak Microsoft spravuje a funguje produkční sítě Azure k zabezpečení datových centrech Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: 515896851c5bf7b9c847416ebadedf1242e0eeb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428268"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Správa a provoz produkční sítě Azure    
Tento článek popisuje, jak Microsoft spravuje a funguje produkční sítě Azure k zabezpečení datových centrech Azure.

## <a name="monitor-log-and-report"></a>Monitorování, protokolů a vytváření sestav

Správa a provoz produkční sítě Azure je koordinovaný proces mezi provozní týmy Azure a Azure SQL Database. Týmy používají v prostředí nástroje několik systému a monitorování výkonu aplikací. A používají vhodné nástroje pro monitorování síťových zařízení, servery, služby a aplikace zpracovává.

K zajištění zabezpečeného spuštění služby spuštěné v prostředí Azure, provozní týmy implementovat několik úrovní monitorování, protokolování a vytváření sestav, včetně následujících akcí:

- Microsoft Monitoring Agent (MMA) primárně, shromáždí informace o protokolu monitorování a diagnostiky z mnoha místech, včetně kontroler prostředků infrastruktury (FC) a kořenovém operačním systému (OS) a zapíše jej do souborů protokolu. Agent nakonec odešle abstrahovanou část informací v úvahu předem nakonfigurované služby Azure storage. Kromě toho samostatné monitorování a diagnostiky služby přečte různé monitorování a diagnostiky dat protokolu a shrnuje informace. Služba monitorování a diagnostiky zapisuje informace do integrovaného protokolu. Azure používá vlastními silami sestavených Azure security monitoring, která je rozšířením monitorování systému Azure. Obsahuje součásti, které sledovat, analyzovat a reportovat zabezpečení relevantní události z různých bodů v platformě.

- Platforma Azure SQL Database Windows Fabric poskytuje správu, nasazování, vývoj a provozní dohledu služby pro službu Azure SQL Database. Platforma nabízí služby pro distribuované, několika kroky nasazení, monitorování stavu, automatických oprav a dodržování předpisů verze služby. Poskytuje následující služby:

   - Služby, funkce s vysokou věrností vývojové prostředí pro modelování (datacenter clustery je nákladná a omezené).
   - Nasazení jedním kliknutím a upgradu pracovních postupů pro spuštění služby a údržbu.
   - Stav vytváření sestav s pracovními postupy automatizované opravy umožňují automatické opravování chyb.
   - Monitorování v reálném čase, výstrahy a ladění zařízení napříč uzly distribuovaného systému.
   - Centralizované shromažďování provozních dat a metrik pro distribuované kořenovou způsobit analýzu a služby insight.
   - Nástroje pro nasazení, provozní Správa změn a monitorování.
   - Azure SQL Database Windows Fabric platformy a sledovacích skripty spouštět nepřetržitě a monitorování v reálném čase.

Pokud se objeví anomálie, procesu reakce na incidenty, za nímž následuje Azure tým incident aktivován. Pracovníci odpovídající podpory Azure se zobrazí oznámení, reagovat na incident. Sledování problémů a řešení jsou zdokumentované a spravují v centralizované systém lístků podpory. Metriky na dobu provozu systému jsou k dispozici v rámci smlouvy o utajení (NDA) a na vyžádání.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Podnikové sítě a službou Multi-Factor Authentication přístup do produkčního prostředí
Podnikové sítě uživatelské základny zahrnuje pracovníky podpory Azure. Podnikové sítě podporuje interní podnikové funkce a zahrnuje přístup k interní aplikace, které se používají pro Azure zákaznickou podporu. V podnikové síti je logicky a fyzicky oddělená od produkční sítě Azure. Pracovníci Azure přístup k podnikové síti pomocí Azure pracovní stanice a přenosné počítače. Všichni uživatelé musí mít účet Azure Active Directory (Azure AD), včetně uživatelského jména a hesla pro přístup k prostředkům podnikové sítě. Přístup k podnikové síti používá účty Azure AD, které jsou vydány pro všechny pracovníky Microsoftu, partnery a dodavateli a spravuje informace o technologii Microsoftu. Jedinečné uživatelské identifikátory rozlišit pracovníky na základě jejich stavu zaměstnání v Microsoftu.

Přístup k interní aplikace Azure je řízen prostřednictvím ověřování pomocí služby Active Directory Federation Services (AD FS). Služba AD FS je služba hostovaná společností Microsoft informačních technologií, která poskytuje ověřování uživatelů v podnikové síti prostřednictvím použití zabezpečeného deklarací identity tokenu a uživatele. Služba AD FS umožňuje interní aplikace Azure k ověřování uživatelů vůči doméně Microsoft podnikové služby active directory. Pro přístup k podnikové síti z prostředí podnikové sítě, musí uživatelé ověřovat pomocí služby Multi-Factor authentication.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
