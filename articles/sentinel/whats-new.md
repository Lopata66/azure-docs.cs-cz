---
title: Co je nového v Azure Sentinel
description: Tento článek popisuje nové funkce služby Azure Sentinel v posledních měsících.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 0eb3b8feda09d931654d1781ea37737d68a1b377
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526632"
---
# <a name="whats-new-in-azure-sentinel"></a>Co je nového v Azure Sentinel

Tento článek obsahuje seznam posledních funkcí přidaných pro službu Azure Sentinel a nové funkce v souvisejících službách, které poskytují vylepšené uživatelské prostředí Azure Sentinel.

Informace o dřívějších funkcích, které jsou dodávány, najdete v našem [blogech pro technickou komunitu](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Vyznačené funkce jsou aktuálně ve verzi PREVIEW. [Doplňkové podmínky Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.


> [!TIP]
> Naši týmy pro lovecké hrozby napříč Microsoft Contribute dotazy, playbooky, sešity a poznámkové bloky do [komunity Sentinel Azure](https://github.com/Azure/Azure-Sentinel), včetně specifických [loveckých dotazů](https://github.com/Azure/Azure-Sentinel) , které vaše týmy můžou přizpůsobovat a používat. 
>
> Můžete také přispět. Připojte se k nám ve [komunitě Azure Sentinel Threat Hunters GitHub](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Leden 2021

- [AZ. SecurityInsights PowerShell Module (Public Preview)](#azsecurityinsights-powershell-module-public-preview)
- [Konektor SQL Database](#sql-database-connector)
- [Vylepšené komentáře k incidentům](#improved-incident-comments)
- [Vyhrazené Log Analytics clustery](#dedicated-log-analytics-clusters)
- [Spravované identity Logic Apps](#logic-apps-managed-identities)
- [Vylepšené ladění pravidel pomocí grafů s náhledem analytického pravidla](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights PowerShell Module (Public Preview)

Azure Sentinel teď podporuje nový modul PowerShellu [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

Modul **AZ. SecurityInsights** podporuje běžné případy použití Sentinel v Azure, jako je například interakce s incidenty ke změně Statues, závažnosti, vlastníka a tak dále, přidávání komentářů a popisků k incidentům a vytváření záložek.

I když pro kanál CI/CD doporučujeme používat šablony [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) , modul **AZ. SecurityInsights** je vhodný pro úlohy po nasazení a je zaměřený na automatizaci SOC.  Například automatizace SOC může zahrnovat kroky ke konfiguraci datových konektorů, vytváření pravidel analýzy nebo přidávání akcí automatizace do pravidel analýz.

Další informace, včetně úplného seznamu a popisu dostupných rutin, popisů parametrů a příkladů, najdete v [dokumentaci AZ. SecurityInsights PowerShellu](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Konektor SQL Database

Azure Sentinel teď poskytuje konektor Azure SQL Database, který umožňuje streamovat auditování a diagnostické protokoly vašich databází do služby Azure Sentinel a průběžně monitorovat aktivity ve všech vašich instancích.

Azure SQL je plně spravovaný databázový stroj PaaS (platforma jako služba), který zpracovává většinu funkcí správy databází, jako je upgrade, opravy, zálohování a monitorování, bez zásahu uživatele.

Další informace najdete v tématu [připojení protokolů diagnostiky a auditu služby Azure SQL Database](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Vylepšené komentáře k incidentům

Analytici používají komentáře k incidentům ke spolupráci na incidentech, postupy při dokumentaci a kroky ručně nebo jako součást PlayBook. 

Naše vylepšené možnosti přidávání komentářů k incidentům umožňují formátovat komentáře a upravovat nebo odstraňovat existující komentáře.

Další informace najdete v tématu [Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Vyhrazené Log Analytics clustery

Funkce Azure Sentinel teď podporuje vyhrazené clustery Log Analytics jako možnost nasazení. Vyhrazený cluster doporučujeme zvážit v těchto případech:

- Ingestování **na 1 TB za den** do pracovního prostoru Sentinel Azure
- **Mít několik pracovních prostorů služby Azure Sentinel** v registraci Azure

Vyhrazené clustery umožňují používat funkce, jako jsou klíče spravované zákazníkem, bezpečnostní modul, dvojité šifrování a rychlejší dotazy mezi jednotlivými pracovními prostory, pokud máte ve stejném clusteru více pracovních prostorů.

Další informace najdete v tématu [Azure monitor protokolu vyhrazených clusterů](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Spravované identity Logic Apps

Azure Sentinel teď podporuje spravované identity pro konektor Azure Sentinel Logic Apps a umožňuje udělit oprávnění přímo ke konkrétnímu playbooku, aby fungovala na Azure Sentinel místo vytváření dalších identit.

- **Bez spravované identity** konektor Logic Apps vyžaduje samostatnou identitu s rolí RBAC Sentinel Azure, aby bylo možné spustit službu Azure Sentinel. Samostatnou identitou může být uživatel služby Azure AD nebo instanční objekt, jako je například aplikace registrovaná v Azure AD.

- **Zapnutím podpory spravovaných identit ve vaší aplikaci logiky** se zaregistruje aplikace logiky pomocí Azure AD a poskytne ID objektu. Použijte ID objektu v Azure Sentinel k přiřazení aplikace logiky k roli Azure RBAC v pracovním prostoru Sentinel Azure. 

Další informace naleznete v tématu:

- [Ověřování pomocí spravované identity v Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
- [Dokumentace ke konektoru služby Azure Sentinel Logic Apps](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Vylepšené ladění pravidel pomocí grafů s náhledem analytického pravidla (Public Preview)

Azure Sentinel teď pomáhá lépe vyladit pravidla analýzy, což vám pomůže zvýšit přesnost a snížit šum.

Po úpravě pravidla analýzy na kartě **nastavit logiku pravidla** Najděte na pravé straně oblast **simulace výsledků** . 

Vyberte **test s aktuálními daty** , aby Azure Sentinel spouštěl simulaci posledních 50 spuštění pravidla analýzy. Vygeneruje se graf, který zobrazí průměrný počet výstrah, které se pravidlo vygenerovalo, na základě vyhodnocených nezpracovaných dat události. 

Další informace najdete v tématu [Definování logiky dotazu pravidla a konfigurace nastavení](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Prosinec 2020

- [80 nové integrované lovecké dotazy](#80-new-built-in-hunting-queries)
- [Vylepšení agenta Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nové integrované lovecké dotazy
 
Integrované lovecké dotazy služby Azure Sentinel umožňují analytikům SOC snižovat mezery v aktuálním pokrytí detekce a Ignite nové lovecké zájemce.

Tato aktualizace pro službu Azure Sentinel zahrnuje nové lovecké dotazy, které poskytují pokrytí v rámci matice MITRE ATT&CK Framework:

- **Kolekce**
- **Příkaz a ovládací prvek**
- **Přístup k přihlašovacím údajům**
- **Rozpoznávání**
- **Realizaci**
- **Exfiltrace**
- **Dopad**
- **Počáteční přístup**
- **Dočasné**
- **Eskalace oprávnění**

Přidané lovecké dotazy jsou navržené tak, aby vám pomohly najít podezřelou aktivitu ve vašem prostředí. I když můžou vracet legitimní aktivity a potenciálně škodlivou aktivitu, můžou být užitečné při vyplňování vašeho lovu. 

Po spuštění těchto dotazů máte jistotu, že budete chtít výsledky převést na analytická pravidla nebo přidat lovecké výsledky existujícím nebo novým incidentům.

Všechny přidané dotazy jsou k dispozici prostřednictvím stránky pro lov v rámci služby Azure Sentinel. Další informace najdete v tématu věnovaném [Zalovenému pro hrozby pomocí služby Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Vylepšení agenta Log Analytics

Výhodou pro uživatele Azure Sentinel z následujících vylepšení agenta Log Analytics:

- **Podpora pro další operační systémy**, včetně CentOS 8, RedHat 8 a SUSE Linux 15.
- **Podpora Pythonu 3** kromě Pythonu 2

Azure Sentinel používá agenta Log Analytics k posílání událostí do vašeho pracovního prostoru, včetně událostí zabezpečení systému Windows, událostí syslog, protokolů CEF a dalších.

> [!NOTE]
> Agent Log Analytics se někdy označuje jako agent OMS nebo Microsoft Monitoring Agent (MMA). 
> 

Další informace najdete v dokumentaci k [Log Analytics](/azure/azure-monitor/platform/log-analytics-agent) a v [poznámkách k verzi agenta Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Listopad 2020

- [Monitorování Logic Apps Playbooky ve službě Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Konektor programu Microsoft 365 Defender (Public Preview)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Monitorování Logic Apps Playbooky ve službě Azure Sentinel

Služba Azure Sentinel se teď integruje s [aplikacemi Azure log](/azure/logic-apps/), což je cloudová služba, která vám pomůže plánovat, automatizovat a orchestrovat úkoly, obchodní procesy a pracovní postupy.

Použijte aplikaci logiky Azure v Azure Sentinel jako PlayBook, která se dá automaticky vyvolat při vytvoření incidentu, nebo při třídění a práci s incidenty. 

Pokud chcete poskytnout přehled o stavu, výkonu a využití vašich playbooky, včetně těch, které přidáte pomocí Azure Logic Apps, Přidali jsme do služby [Azure Workbook](/azure/azure-monitor/platform/workbooks-overview) s názvem **playbooky monitoring Health**. 

Pomocí sešitu **sledování stavu playbooky** můžete monitorovat stav svého playbookyu, nebo se můžete podívat na anomálie v množství úspěšných nebo neúspěšných spuštění. 

Sešit **monitorování stavu playbooky** je teď dostupný v galerii šablon Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Ukázka sešitu monitorování stavu Playbooky":::

Další informace naleznete v tématu:

- [Dokumentace k Logic Apps](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Dokumentace k Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Konektor programu Microsoft 365 Defender (Public Preview)
 
Konektor programu Microsoft 365 Defender pro Azure Sentinel umožňuje streamovat pokročilé protokoly o lovu (typ nezpracovaných dat událostí) z Microsoft 365 Defenderu do Azure Sentinel. 

Díky integraci programu [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) do bezpečnostního kolektoru v [Microsoft 365 Defenderu](/microsoft-365/security/mtp/microsoft-threat-protection) teď můžete shromáždit rozšířené lovecké události v programu Microsoft Defender pro koncové body pomocí konektoru Microsoft 365 Defenderu a streamovat je přímo do nových účelových tabulek v pracovním prostoru Sentinel Azure. 

Tabulky Sentinel Azure jsou postavené na stejném schématu, které se používá na portálu Microsoft 365 Defender, a poskytuje úplný přístup k kompletní sadě pokročilých protokolů pro lov. 

Další informace najdete v tématu [připojení dat z Microsoft 365 Defenderu k Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender byl dřív známý jako Microsoft Threat Protection nebo MTP. Microsoft Defender pro koncové body se dřív jmenoval jako rozšířená ochrana před internetovými útoky v programu Microsoft Defender nebo MDATP.
> 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[On-Board – Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Získání přehledu o upozorněních](quickstart-get-visibility.md)
