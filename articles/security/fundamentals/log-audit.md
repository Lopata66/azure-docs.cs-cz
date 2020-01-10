---
title: Protokolování a auditování zabezpečení Azure | Microsoft Docs
description: Seznamte se s protokoly dostupnými v Azure a přehledy zabezpečení, které můžete získat.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750787"
---
# <a name="azure-security-logging-and-auditing"></a>Protokolování a auditování zabezpečení Azure

Azure nabízí řadu konfigurovatelných možností auditování zabezpečení a protokolování, které vám pomůžou identifikovat mezery v zásadách a mechanismech zabezpečení. Tento článek pojednává o generování, shromažďování a analýze protokolů zabezpečení ze služeb hostovaných v Azure.

> [!Note]
> Některá doporučení v tomto článku můžou mít za následek zvýšení využití dat, sítě nebo výpočetních prostředků a zvyšují náklady na licence nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure

Cloudové aplikace jsou komplexní s mnoha pohybujícími se částmi. Data protokolování můžou poskytovat přehled o vašich aplikacích a pomáhat vám:

- Řešení minulých problémů nebo prevence potenciálních
- Zlepšení výkonu a udržovatelnosti aplikace
- Automatizace akcí, které by jinak vyžadovaly ruční zásah

Protokoly Azure se kategorizují do následujících typů:
* **Protokoly řízení a správy** poskytují informace o Azure Resource Manager operacích vytváření, aktualizace a odstraňování. Další informace najdete v tématu [protokoly aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).

* **Protokoly roviny dat** poskytují informace o událostech vyvolaných v rámci využití prostředků Azure. Příkladem tohoto typu protokolu jsou protokoly událostí systému Windows, zabezpečení a aplikace ve virtuálním počítači a [diagnostické protokoly](../../azure-monitor/platform/platform-logs-overview.md) , které jsou konfigurovány prostřednictvím Azure monitor.

* **Zpracované události** poskytují informace o analyzovaných událostech a výstrahách, které byly zpracovány vaším jménem. Příklady tohoto typu jsou [Azure Security Center výstrahy](../../security-center/security-center-managing-and-responding-alerts.md) , které [Azure Security Center](../../security-center/security-center-intro.md) zpracovaly a analyzovaly vaše předplatné a poskytuje stručné výstrahy zabezpečení.

V následující tabulce jsou uvedeny nejdůležitější typy protokolů, které jsou k dispozici v Azure:

| Kategorie protokolu | Typ protokolu | Využití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivit](../../azure-monitor/platform/platform-logs-overview.md)|Řízení událostí roviny na Azure Resource Managerch prostředcích|  Poskytuje přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného.|    Rozhraní REST API [Azure monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Protokoly prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md)|Častá data o provozu prostředků Azure Resource Manager v předplatném|   Poskytuje přehled o operacích, které provedl váš prostředek.| Azure Monitor|
|[Vytváření sestav Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Protokoly a sestavy | Oznamuje aktivity přihlašování uživatelů a informace o aktivitách systému o správě uživatelů a skupin.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuální počítače a cloudové služby](../../azure-monitor/learn/quick-collect-azurevm.md)|Služba protokolu událostí systému Windows a protokol syslog pro Linux|  Zachycuje systémová data a data protokolování na virtuálních počítačích a přenáší tato data do účtu úložiště podle vašeho výběru.|   Windows (pomocí úložiště Windows Azure Diagnostics [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)]) a Linux v Azure monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště poskytuje data metrik pro účet úložiště.|Poskytuje přehled o požadavcích trasování, analyzuje trendy využití a diagnostikuje problémy s vaším účtem úložiště.|   REST API nebo [Knihovna klienta](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Protokoly toku pro skupinu zabezpečení sítě (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formát JSON, zobrazení odchozích a příchozích toků podle jednotlivých pravidel|Zobrazuje informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Přehled aplikací](../../azure-monitor/app/app-insights-overview.md)|Protokoly, výjimky a vlastní diagnostika|  Poskytuje službu APM (Application Performance Monitoring) pro webové vývojáře na různých platformách.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Zpracování dat/výstrahy zabezpečení](../../security-center/security-center-intro.md)|  Výstrahy Azure Security Center Azure Monitor protokoly|    Poskytuje informace o zabezpečení a výstrahy.|  Rozhraní REST API, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolu s místními SIEM systémy
[Integrace výstrah Security Center](../../security-center/security-center-export-data-to-siem.md) se zabývá tím, jak synchronizovat Security Center výstrahy, události zabezpečení virtuálních počítačů shromažďované protokoly diagnostiky Azure a protokoly auditu Azure s protokoly Azure monitor nebo řešením Siem.

## <a name="next-steps"></a>Další kroky

- [Auditování a protokolování](management-monitoring-overview.md): Chraňte data udržováním viditelnosti a rychlé reakce na včasné výstrahy zabezpečení.

- [Protokolování zabezpečení a shromažďování protokolů v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): vyvynuťte tato nastavení, abyste zajistili, že vaše instance Azure shromažďují správné protokoly zabezpečení a auditu.

- [Konfigurace nastavení auditování pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Pokud jste správcem kolekce webů, načtěte historii akcí jednotlivých uživatelů a historii akcí provedených během konkrétního rozsahu dat.

- [Hledání v protokolu auditu v sadě office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): pomocí Centrum zabezpečení a dodržování předpisů Office 365 můžete vyhledat jednotný protokol auditu a zobrazit aktivitu uživatele a správce v organizaci sady Office 365.
