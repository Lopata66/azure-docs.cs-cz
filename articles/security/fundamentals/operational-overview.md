---
title: Přehled služby Azure Operational Security | Microsoft Docs
description: Další informace o provozním zabezpečení Azure najdete v tomto přehledu. Provozní zabezpečení odkazuje na služby, ovládací prvky a funkce ochrany Asset Protection.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: f3ec284de3d43e75bd0832b1d98bb9d319a7ca0e
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925307"
---
# <a name="azure-operational-security-overview"></a>Přehled služby Azure Operational Security

[Provozní zabezpečení Azure](/azure/security/fundamentals/operational-security) odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure. Je to architektura, která zahrnuje znalostní báze získané prostřednictvím nejrůznějších funkcí, které jsou jedinečné pro společnost Microsoft. Mezi tyto možnosti patří Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center a důkladné povědomí o kyberbezpečnosti hrozbách.

## <a name="azure-management-services"></a>Služby správy Azure

Provozní tým IT je zodpovědný za správu infrastruktury Datacenter, aplikací a dat, včetně stability a zabezpečení těchto systémů. Nicméně získání přehledů o zabezpečení ve všech rostoucích složitých prostředích IT často vyžaduje, aby organizace Cobble data z více systémů zabezpečení a správy.

[Protokoly sledování Microsoft Azure](/azure/operations-management-suite/operations-management-suite-overview) jsou cloudové řešení pro správu IT, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu. Základní funkce nabízí následující služby, které běží v Azure. Azure obsahuje několik služeb, které vám pomůžou se správou a ochranou místní a cloudové infrastruktury. Každá služba poskytuje konkrétní funkci správy. Můžete kombinovat služby a dosáhnout tak různých scénářů správy. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/azure-monitor/overview) shromažďuje data ze spravovaných zdrojů do centrálních úložišť dat. Tato data můžou zahrnovat události, data o výkonu nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po shromáždění dat je k dispozici pro výstrahy, analýzu a export.

Můžete konsolidovat data z nejrůznějších zdrojů a kombinovat data ze služeb Azure s existujícím místním prostředím. Protokoly Azure Monitor také jednoznačně oddělují shromažďování dat z akce provedené u těchto dat, takže všechny akce jsou k dispozici pro všechny druhy dat.

### <a name="automation"></a>Automation

[Azure Automation](/azure/automation/automation-intro) poskytuje možnost automatizace ručních, dlouhotrvajících a často opakovaných úloh, které se běžně provádějí v cloudovém a podnikovém prostředí. Šetří čas a zvyšuje spolehlivost administrativních úloh. V pravidelných intervalech také plánuje automatické provádění těchto úkolů. Procesy můžete automatizovat pomocí runbooků nebo automatizovat správu konfigurace pomocí konfigurace požadovaného stavu.

### <a name="backup"></a>Backup

[Azure Backup](/azure/backup/backup-introduction-to-azure-backup) je služba založená na Azure, kterou můžete použít k zálohování (nebo ochraně) a obnovení dat v Microsoft Cloud. Azure Backup nahradí vaše stávající místní nebo jiné řešení zálohování cloudovým řešením, které je spolehlivé, bezpečné a nákladově konkurenční.

Azure Backup nabízí komponenty, které stáhnete a nasadíte na příslušném počítači nebo serveru nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny součásti Azure Backup (bez ohledu na to, jestli chráníte data v místním nebo cloudovém prostředí) se dají použít k zálohování dat do trezoru Azure Recovery Services v Azure.

Další informace najdete v [tabulce Azure Backup komponenty](/azure/backup/backup-overview#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) zajišťuje kontinuitu podnikových prostředí tím, že orchestruje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud vaše primární lokalita není k dispozici, převezmete služby při selhání do sekundárního umístění, aby uživatelé mohli i nadále pracovat. Po návratu systémů do funkčního pořadí dojde k navrácení služeb po obnovení. Pomocí Azure Security Center můžete provádět inteligentnější a efektivní detekci hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) je komplexní služba identity, která:

-   Povolí správu identit a přístupu (IAM) jako cloudovou službu.
-   Poskytuje správu centrálního přístupu, jednotné přihlašování (SSO) a vytváření sestav.
-   Podporuje správu integrovaného přístupu pro [tisíce aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) v Azure Marketplace, včetně Salesforce, Google Apps, box a Concur.

Azure AD zahrnuje také plnou sadu [funkcí pro správu identit](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), včetně těchto:

- [Vícefaktorové ověřování](/azure/multi-factor-authentication/multi-factor-authentication)
- [Samoobslužná správa hesel](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobslužná správa skupin](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Správa privilegovaných účtů](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Řízení přístupu na základě role v Azure (Azure RBAC)](/azure/role-based-access-control/overview)
- [Monitorování využití aplikací](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Rozšířené auditování](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorování a upozorňování zabezpečení](/azure/operations-management-suite/oms-security-responding-alerts)

Díky Azure Active Directory mají všechny aplikace publikované pro vaše partnery a zákazníky (firmy nebo spotřebitele) stejné možnosti správy identit a přístupu. To vám umožňuje významně snižovat provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že zvýšená viditelnost (a kontrolu nad zabezpečením vašich prostředků Azure). Poskytuje integrované monitorování zabezpečení a správu zásad ve vašich předplatných. Pomáhá detekovat hrozby, které by jinak neinformovaly, a funguje s širokou ekosystémem řešení zabezpečení.

[Zabezpečte data virtuálních počítačů](/azure/security-center/security-center-linux-virtual-machine) v Azure tím, že zajistíte lepší nastavení zabezpečení virtuálního počítače a monitorování hrozeb. Security Center může u virtuálních počítačů monitorovat:

- Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Chybí zabezpečení systému a kritické aktualizace.
- Doporučení pro ochranu koncového bodu.
- Ověření šifrování disku.
- Síťové útoky.

Security Center používá [řízení přístupu na základě role v Azure (Azure RBAC)](/azure/role-based-access-control/role-assignments-portal). RBAC poskytuje [předdefinované role](../../role-based-access-control/built-in-roles.md) , které je možné přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastník, přispěvatel nebo čtenář pro předplatné nebo skupinu prostředků, do které prostředek patří.

>[!Note]
>Další informace o rolích a povolených akcích v Security Center najdete v tématu [oprávnění v Azure Security Center](/azure/security-center/security-center-permissions).

Security Center používá Microsoft Monitoring Agent. Toto je stejný agent, kterého používá služba Azure Monitor. Data shromážděná z tohoto agenta se ukládají do stávajícího Log Analyticsho [pracovního prostoru](/azure/log-analytics/log-analytics-manage-access) přidruženého k vašemu předplatnému Azure nebo novému pracovnímu prostoru, přičemž se vezme v úvahu geografická poloha virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor

Problémy s výkonem ve vaší cloudové aplikaci můžou ovlivnit vaši firmu. S více propojenými komponentami a často vydanými verzemi může degradace probíhat kdykoli. A pokud vyvíjíte aplikaci, uživatelé obvykle zjišťují problémy, které nebyly při testování nalezeny. O těchto problémech byste měli okamžitě informovat a měli byste mít nástroje pro diagnostiku a řešení problémů.

[Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) je základní nástroj pro monitorování služeb spuštěných v Azure. Poskytuje data na úrovni infrastruktury týkající se propustnosti služby a okolního prostředí. Pokud spravujete své aplikace v Azure a rozhodujete, jestli chcete škálovat prostředky směrem nahoru nebo dolů, Azure Monitor je místo, kde se má začít.

K získání podrobných přehledů o vaší aplikaci můžete použít taky data monitorování. Tato znalostní báze vám může pomoci zvýšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Azure Monitor obsahuje následující součásti.

### <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytuje přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného. Dříve se nazýval protokol auditu nebo provozní protokol, protože pro vaše předplatná hlásí události řízení a roviny.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

[Diagnostické protokoly Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Obsah těchto protokolů se liší podle typu prostředku.

Protokoly událostí systému Windows jsou jednou z kategorií diagnostických protokolů pro virtuální počítače. Protokoly objektů blob, tabulek a front jsou kategoriemi diagnostických protokolů pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit nabízí přehled o operacích provedených u prostředků v rámci vašeho předplatného. Diagnostické protokoly poskytují přehled o operacích, které se provedly samotným prostředkem.

### <a name="metrics"></a>Metriky

Azure Monitor poskytuje telemetrii, která vám poskytne přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitější typ dat telemetrie Azure jsou [metriky](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (označované taky jako čítače výkonu) vydávané většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics povoluje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z různých zdrojů. Aktuálně se podporuje [role cloudových služeb Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuální počítače Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service) se systémem Microsoft Windows a [Azure Service Fabric](/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Zákazníci vytvářejí komplexní síť v Azure tím, že orchestrují a sestavují jednotlivé síťové prostředky, jako jsou virtuální sítě, Azure ExpressRoute, Azure Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředcích.

Koncová síť může mít složitou konfiguraci a interakce mezi prostředky. Výsledkem jsou komplexní scénáře, které vyžadují monitorování založené na scénářích prostřednictvím [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher zjednodušuje monitorování a diagnostiku vaší sítě Azure. Nástroje pro diagnostiku a vizualizaci v Network Watcher můžete použít k těmto akcím:

- Pořiďte vzdálené zachycení paketů na virtuálním počítači Azure.
- Získejte přehled o provozu v síti pomocí protokolů toků.
- Diagnostikujte Azure VPN Gateway a připojení.

Network Watcher v současné době má následující možnosti:

- [Topologie](/azure/network-watcher/network-watcher-topology-overview): poskytuje přehled o různých propojeních a přidruženích mezi síťovými prostředky ve skupině prostředků.
- [Zachytávání paketů proměnných](/azure/network-watcher/network-watcher-packet-capture-overview): zachycuje data paketů na virtuálním počítači a z něj. Rozšířené možnosti filtrování a jemně vyladěné ovládací prvky, jako je možnost nastavit omezení času a velikosti, poskytují všestrannost. Data paketů se můžou ukládat do úložiště objektů BLOB nebo na místní disk ve formátu. Cap.
- [Ověření toku protokolu IP](/azure/network-watcher/network-watcher-ip-flow-verify-overview): kontroluje, jestli je paket povolený nebo zakázaný na základě parametrů paketu o hodnotě 5 řazené kolekce členů (cílová IP adresa, zdrojová adresa IP, cílový port, zdrojový port a protokol). Pokud skupina zabezpečení zamítne paket, vrátí se pravidlo a skupina, které paket zamítly.
- [Další směrování](/azure/network-watcher/network-watcher-next-hop-overview): Určuje další segment směrování pro pakety směrované do síťových prostředků Azure, abyste mohli diagnostikovat všechny nesprávně nakonfigurované trasy definované uživatelem.
- [Zobrazení skupiny zabezpečení](/azure/network-watcher/network-watcher-security-group-view-overview): Získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači.
- [Protokoly toku NSG pro skupiny zabezpečení sítě](/azure/network-watcher/network-watcher-nsg-flow-logging-overview): umožňuje zachytit protokoly související s provozem, který je povolený nebo zakázaný pravidly zabezpečení ve skupině. Tok je definovaný pomocí informací o 5-řazené kolekci členů: zdrojová IP adresa, cílová IP adresa, zdrojový port, cílový port a protokol.
- [Řešení potíží s bránou virtuální sítě a připojením](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): poskytuje možnost odstraňovat brány a připojení virtuální sítě.
- [Omezení pro předplatné sítě](/azure/network-watcher/network-watcher-monitoring-overview): umožňuje zobrazit využití síťových prostředků proti limitům.
- [Diagnostické protokoly](/azure/network-watcher/network-watcher-monitoring-overview): poskytuje jedno podokno pro povolení nebo zakázání diagnostických protokolů pro síťové prostředky ve skupině prostředků.

Další informace najdete v tématu [konfigurace Network Watcher](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Transparentnost přístupu poskytovatele cloudové služby

[Customer Lockbox pro Microsoft Azure](customer-lockbox-overview.md) je služba integrovaná do Azure Portal, která poskytuje explicitní řízení ve vzácných instancích, když podpora Microsoftu inženýr může potřebovat přístup k vašim datům za účelem vyřešení problému.
Existuje hodně instancí, jako je například ladění problému se vzdáleným přístupem, kde podpora Microsoftu inženýr vyžaduje zvýšená oprávnění k vyřešení tohoto problému. V takových případech technici Microsoftu využívají službu pro přístup za běhu, která poskytuje omezenou časovou autorizaci s přístupem omezeným na službu.  
I když společnost Microsoft vždycky získala souhlas zákazníka s přístupem, Customer Lockbox nyní poskytuje možnost kontrolovat a schvalovat nebo odmítat takové žádosti z webu Azure Portal. Pracovníkům podpory Microsoftu nebude udělen přístup, dokud žádost neschválíte.

## <a name="standardized-and-compliant-deployments"></a>Standardizovaná a vyhovující nasazení

[Plány Azure](/azure/governance/blueprints/overview) umožňují cloudové architekty a skupiny centrálních informačních technologií definovat opakující se sadu prostředků Azure, které implementují a vyhovují standardům, vzorům a požadavkům organizace.  
Díky tomu mohou týmy DevOps rychle sestavovat a vytvářet nová prostředí a důvěřovat tomu, že jsou sestavují s infrastrukturou, která udržuje dodržování předpisů organizace.
Modrotisky poskytují deklarativní způsob pro orchestraci nasazení různých šablon prostředků a dalších artefaktů, jako jsou:

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manageru
- Skupiny prostředků

## <a name="devops"></a>DevOps

Před vývojem aplikací [(DevOps)](https://www.visualstudio.com/learn/what-is-devops/) byly týmy zpoplatněné shromažďování podnikových požadavků pro softwarový program a psaní kódu. Pak samostatný tým služby QA testoval program v izolovaném vývojovém prostředí. V případě splnění požadavků tým QA uvolnil kód pro operace, které chcete nasadit. Týmy nasazení byly dále rozděleny do skupin, jako jsou sítě a databáze. Pokaždé, když se softwarový program vyvolal přes zeď, do nezávislého týmu, přidal se k němu kritická místa.

DevOps umožňuje týmům dodávat bezpečnější a vyšší kvalitu řešení rychleji a efektivněji. Zákazníci očekávají dynamický a spolehlivý zážitek při využívání softwaru a služeb. Týmy musí rychle iterovat na aktualizace softwaru a měřit dopad aktualizací. Musí rychle reagovat pomocí nových iterací vývoje, aby vyřešily problémy nebo poskytovaly více hodnot.  

Cloudové platformy, jako je Microsoft Azure, odstranily tradiční kritické body a pomohl pomohly infrastrukturu. Vládne softwaru v každé firmě jako klíčové rozlišení a faktor v obchodních výstupech. Pohyb DevOps není možné nebo by se neměl používat žádná organizace, vývojář nebo pracovník IT.

Vyspělí DevOps lékaři přijímají několik z následujících postupů. Tyto postupy [zahrnují osoby](https://www.visualstudio.com/learn/what-is-devops-culture/) , které tvoří strategie založené na obchodních scénářích. Pomocí nástrojů můžete automatizovat různé postupy.

- Techniky [agilního plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) slouží k plánování a izolaci práce do sprintů, správě kapacity týmu a pomáhat týmům rychle se přizpůsobit měnícím se potřebám firmy.
- Správa [verzí, obvykle s Git](https://www.visualstudio.com/learn/what-is-git/), umožňuje týmům umístěným kdekoli na světě sdílet zdroje a integrovat s nástroji pro vývoj softwaru za účelem automatizace kanálu vydávání verzí.
- [Průběžná integrace](https://www.visualstudio.com/learn/what-is-continuous-integration/) Drive průběžné slučování a testování kódu, který vede k brzkému nalezení vad.  Mezi další výhody patří méně času v boji proti problémům sloučení a rychlé zpětné vazby pro vývojové týmy.
- [Průběžné doručování](https://www.visualstudio.com/learn/what-is-continuous-delivery/) softwarových řešení do produkčních a testovacích prostředí pomáhá organizacím rychle opravovat chyby a reagovat na neustále se měnící obchodní požadavky.
- [Monitorování](https://www.visualstudio.com/learn/what-is-monitoring/) spuštěných aplikací – včetně produkčních prostředí pro stav aplikací a zákaznického využití – pomáhá organizacím vytvořit hypotézu a rychle ověřit nebo prokázat strategie.  Bohatá data jsou zachycena a ukládána v různých formátech protokolování.
- [Infrastruktura jako kód (IAC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) je postup, který umožňuje automatizaci a ověřování vytváření a rozboru sítí a virtuálních počítačů při poskytování zabezpečených a stabilních hostujících platforem aplikací.
- Architektura [mikroslužeb](https://www.visualstudio.com/learn/what-are-microservices/) se používá k izolaci případů podnikového použití do malých opakovaně použitelných služeb.  Tato architektura umožňuje škálovatelnost a efektivitu.

## <a name="next-steps"></a>Další kroky

Další informace o řešení Security and Audit najdete v následujících článcích:

- [Zabezpečení a dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
