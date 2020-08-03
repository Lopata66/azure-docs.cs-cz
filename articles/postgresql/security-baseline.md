---
title: Základní plán zabezpečení Azure pro Azure Database for PostgreSQL jeden server
description: Základní plán zabezpečení Azure pro Azure Database for PostgreSQL jeden server
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 475933bcc9219808087671d49778cc3696d8c815
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500789"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Základní plán zabezpečení Azure pro Azure Database for PostgreSQL jeden server

Základní plán zabezpečení Azure pro Azure Database for PostgreSQL jeden server obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: konfigurace privátního odkazu pro Azure Database for PostgreSQL se soukromými koncovými body. Privátní odkaz vám umožní připojit se k různým službám PaaS v Azure prostřednictvím privátního koncového bodu. Privátní propojení Azure v podstatě přináší služby Azure do privátního Virtual Network (VNet). Přenosy mezi vaší virtuální sítí a instancí PostgreSQL cestují páteřní síť Microsoftu.

Alternativně můžete použít koncové body služby Virtual Network k ochraně a omezení síťového přístupu k vašim Azure Database for PostgreSQL implementaci. Pravidla virtuální sítě jsou jednou funkcí zabezpečení brány firewall, která určuje, jestli váš server Azure Database for PostgreSQL přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích.

Můžete také zabezpečit Azure Database for PostgreSQL Server pomocí pravidel brány firewall. Brána firewall serveru znemožní přístup k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

Postup konfigurace privátního odkazu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Jak nakonfigurovat Azure Database for PostgreSQL pravidla brány firewall:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné**materiály: Pokud je vaše instance Azure Database for PostgreSQL zabezpečená pro soukromý koncový bod, můžete virtuální počítače nasadit ve stejné virtuální síti. Ke snížení rizika exfiltrace dat můžete použít skupinu zabezpečení sítě (NSG). Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Postup konfigurace privátního odkazu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Jak povolit protokoly toku NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Povolte DDoS Protection Standard ve virtuálních sítích přidružených k vašim Azure Database for PostgreSQL instancím, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Jak nakonfigurovat DDoS Protection:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Doprovodné**materiály: Pokud je vaše instance Azure Database for PostgreSQL zabezpečená pro soukromý koncový bod, můžete virtuální počítače nasadit ve stejné virtuální síti. Pak můžete nakonfigurovat skupinu zabezpečení sítě (NSG), aby se snížilo riziko exfiltrace dat. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Jak povolit protokoly toku NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: u prostředků, které potřebují přístup k instancím Azure Database for PostgreSQL, použijte značky služby virtuální sítě a definujte řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. SQL. WestUs) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro odpovídající službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Poznámka: Azure Database for PostgreSQL používá značku služby Microsoft. SQL.

Další informace o použití značek služeb:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Pochopení použití značek služby pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě a síťové prostředky přidružené k vašim Azure Database for PostgreSQL instancím Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. DBforPostgreSQL a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for PostgreSQLch instancí. Můžete také využít integrované definice zásad týkající se sítě nebo vašich Azure Database for PostgreSQL instancí, například:

- Měla by být povolená DDoS Protection Standard.

- Pro databázové servery PostgreSQL by mělo být povoleno připojení TLS.

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy ukázky pro síťové služby:https://docs.microsoft.com/azure/governance/policy/samples/

Postup vytvoření Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro prostředky, které se týkají zabezpečení sítě a toku provozu pro vaše Azure Database for PostgreSQL instance pro poskytování metadat a logické organizace.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

Vytváření a používání značek:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Database for PostgreSQL instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Postup při vytváření výstrah v Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, například Azure Database for PostgreSQL pro časová razítka v protokolech.


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: povolení nastavení diagnostiky a protokolů serveru a příjem protokolů pro agregaci dat zabezpečení generovaných vašimi Azure Database for PostgreSQL instancemi. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

Konfigurace a přístup k protokolům serveru pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Postup konfigurace a přístup k protokolům auditu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Jak připojit Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky na vašich instancích Azure Database for PostgreSQL pro přístup k protokolům auditu, zabezpečení a zdrojů. Ujistěte se, že jste konkrétně povolili protokol auditu PostgreSQL. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. Můžete také povolit nastavení diagnostiky protokolů aktivit Azure a odesílat protokoly do stejného Log Analytics pracovního prostoru nebo účtu úložiště.

Konfigurace a přístup k protokolům serveru pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Postup konfigurace a přístup k protokolům auditu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Azure Database for PostgreSQL, nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Ukládání protokolů prostředků do účtu Azure Storage:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů z Azure Database for PostgreSQL instancí pro chování neobvyklé. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

Jak připojit Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Další informace o Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Postup provádění vlastních dotazů v Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Pokyny**: povolení rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Kromě toho můžete povolit protokoly serveru a nastavení diagnostiky pro PostgreSQL a odesílat protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

Jak povolit rozšířenou ochranu před internetovými útoky pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Konfigurace a přístup k protokolům serveru pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Postup konfigurace a přístup k protokolům auditu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Jak připojit Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL nezpracovává ani nevytváří protokoly související s DNS.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: Udržujte inventář uživatelských účtů, které mají přístup správce k rovině řízení (například Azure Portal) vašich Azure Database for PostgreSQL instancí. Kromě toho udržujte inventarizaci účtů pro správu, které mají přístup k rovině dat (v samotné databázi) vašich Azure Database for PostgreSQL instancí. (Při vytváření serveru PostgreSQL zadáte přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů PostgreSQL.)

Azure Database for PostgreSQL nepodporuje integrované řízení přístupu založené na rolích, ale můžete vytvořit vlastní role na základě konkrétních operací poskytovatele prostředků.

Porozumění vlastním rolím pro předplatné Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Principy Azure Database for PostgreSQL operací poskytovatele prostředků:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Pochopení správy přístupu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné**materiály: Azure Active Directory a Azure Database for PostgreSQL nemají koncept výchozích hesel.

Při vytváření samotného Azure Database for PostgreSQL prostředku vynutí Azure vytvoření administrativního uživatele se silným heslem. Po vytvoření instance PostgreSQL můžete použít první účet správce serveru, který jste vytvořili, a vytvořit tak další uživatele a udělit jim přístup pro správu. Při vytváření těchto účtů se ujistěte, že pro každý účet nakonfigurujete jiné silné heslo.

Postup vytvoření dalších účtů pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

Postup aktualizace hesla správce:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k vašim instancím Azure Database for PostgreSQL. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. 

Pochopení Azure Security Center identity a přístupu:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Princip vytváření uživatelů s oprávněními správce v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: přihlášení k Azure Database for PostgreSQL je podporované pomocí uživatelského jména a hesla, které je nakonfigurované přímo v databázi, a také pomocí Azure Active Directory (AD) identity a využití tokenu Azure AD k připojení. Při použití tokenu Azure AD se podporují různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD připojující se k databázi.

Přístup k rovině ovládacího prvku pro PostgreSQL je k dispozici samostatně prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory.

Pro ověřování pomocí Azure Database for PostgreSQL použijte Azure Active Directory:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Pochopení Azure Database for PostgreSQL REST API:https://docs.microsoft.com/rest/api/postgresql/

Vysvětlení jednotného přihlašování pomocí služby Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu. Při použití tokenů Azure AD pro přihlášení k vaší databázi vám to umožní vyžadovat vícefaktorové ověřování pro přihlášení k databázi.

Jak povolit vícefaktorové ověřování v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Pro ověřování pomocí Azure Database for PostgreSQL použijte Azure Active Directory:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Jak monitorovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné**materiály: povolení rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL pro generování výstrah pro podezřelou aktivitu.

Kromě toho můžete použít Azure Active Directory (AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

Postup nastavení rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Postup nasazení Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Vysvětlení zjišťování rizik Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu umožněte portálu a Azure Resource Manager přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: jako centrální ověřování a systém autorizací použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Pro přihlášení k Azure Database for PostgreSQL se doporučuje používat Azure AD a k připojení použít token Azure AD. Při použití tokenu Azure AD se podporují různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD připojující se k databázi.

Přihlašovací údaje Azure AD se můžou používat taky pro správu na úrovni roviny správy (například Azure Portal) k řízení účtů správce PostgreSQL.

Pro ověřování pomocí Azure Database for PostgreSQL použijte Azure Active Directory:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Přečtěte si protokoly Azure Active Directory, které vám pomůžou zjistit zastaralé účty, které můžou zahrnovat Azure Database for PostgreSQL administrativních rolí. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k Azure Database for PostgreSQL a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Pochopení sestav Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat kontroly přístupu Azure identity:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Zkontrolujte uživatele PostgreSQL a přiřazené role:https://www.postgresql.org/docs/current/database-roles.html

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné**materiály: povolení nastavení diagnostiky pro Azure Database for PostgreSQL a Azure Active Directory a posílání všech protokolů do log Analyticsho pracovního prostoru. Nakonfigurujte požadovaná upozornění (například neúspěšné pokusy o ověření) v rámci Log Analytics.

Konfigurace a přístup k protokolům serveru pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Postup konfigurace a přístup k protokolům auditu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Jak integrovat protokoly aktivit Azure do Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné**materiály: povolení rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL pro generování výstrah pro podezřelou aktivitu.

Pomocí funkcí Azure Active Directory Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce. Můžete povolit automatizované odezvy prostřednictvím služby Azure Sentinel, abyste mohli implementovat reakce na zabezpečení vaší organizace.

K dalšímu zkoumání můžete také ingestovat protokoly do Azure Sentinel.

Postup nastavení rozšířené ochrany před internetovými útoky pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Přehled Azure AD Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Jak zobrazit rizikové přihlašování Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Jak připojit Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: aktuálně není k dispozici; Customer Lockbox ještě není pro Azure Database for PostgreSQL podporovaná.

Seznam podporovaných služeb Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: pomocí značek pomáhají při sledování Azure Database for PostgreSQLch instancí nebo souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Pomocí kombinace privátních odkazů, koncových bodů služby a/nebo pravidel brány firewall můžete izolovat a omezit síťový přístup k instancím Azure Database for PostgreSQL.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Postup konfigurace privátního odkazu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Jak nakonfigurovat Azure Database for PostgreSQL pravidla brány firewall:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: při použití virtuálních počítačů Azure pro přístup k instancím Azure Database for PostgreSQL použijte privátní linku, konfigurace sítě PostgreSQL, skupiny zabezpečení sítě a značky služeb, aby se zmírnila možnost datových exfiltrace.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for PostgreSQL a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Jak zmírnit exfiltrace dat pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Pochopení ochrany zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure Database for PostgreSQL podporuje připojení serveru PostgreSQL k klientským aplikacím pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředním případě šifrováním datového proudu mezi serverem a vaší aplikací. V Azure Portal Ujistěte se, že je ve výchozím nastavení povolená možnost vykonat připojení SSL pro všechny vaše Azure Database for PostgreSQL instance.

V současné době je verze TLS podporovaná pro Azure Database for PostgreSQL TLS 1,0, TLS 1,1, TLS 1,2.

Postup konfigurace šifrování při přenosu pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Database for PostgreSQL. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k Azure Database for PostgreSQL rovině ovládacího prvku (např. Azure Portal). Pro přístup k rovině dat (v samotné databázi) použijte dotazy SQL k vytvoření uživatelů a konfiguraci oprávnění uživatele. RBAC nemá vliv na uživatelská oprávnění v rámci databáze.

Jak nakonfigurovat RBAC v Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Jak nakonfigurovat přístup uživatelů pomocí SQL pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for PostgreSQL a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Pochopení ochrany zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Služba Azure Database for PostgreSQL používá šifrovací modul ověřený standardem FIPS 140-2 pro šifrování úložiště dat v klidovém provozu. Data včetně záloh se šifrují na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Šifrování dat pomocí klíčů spravovaných zákazníkem (CMK) pro Azure Database for PostgreSQL jeden server umožňuje využít vlastní klíč (BYOK) pro ochranu dat v klidovém prostředí. V tuto chvíli musíte požádat o přístup k používání této možnosti. Chcete-li to provést, obraťte se na:

AskAzureDBforPostgreSQL@service.microsoft.com.

Vysvětlení šifrování v klidovém umístění pro Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security

Vysvětlení šifrování v klidovém formátu pro Azure Database for PostgreSQL pomocí klíčů spravovaných zákazníkem:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Database for PostgreSQL a dalších důležitých nebo souvisejících prostředcích.

Vytváření upozornění pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: aktuálně není k dispozici; Azure Security Center ještě nepodporuje posouzení ohrožení zabezpečení pro Azure Database for PostgreSQL.

Pokrytí funkcí pro služby Azure PaaS v Azure Security Center:https://docs.microsoft.com/azure/security-center/features-paas

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Database for PostgreSQL.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně instancí Azure Database for PostgreSQL) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

Jak vytvářet dotazy pomocí Azure Resource graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Porozumění službě Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro Azure Database for PostgreSQL instancí a dalších souvisejících prostředků, které dávají metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Database for PostgreSQLch instancí a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se správcem prostředků Azure prostřednictvím skriptů

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v prostředí s vysokým zabezpečením, jako jsou například instance Azure Database for PostgreSQL obsahující citlivé údaje.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Database for PostgreSQL instance pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforPostgreSQL můžete vytvořit vlastní zásady, které budou auditovat nebo vymáhat konfiguraci sítě vašich Azure Database for PostgreSQL instancí. Můžete také využít integrované definice zásad související s vašimi Azure Database for PostgreSQL instancemi, například:

- Pro databázové servery PostgreSQL by mělo být povoleno připojení TLS.

- Pro databázové servery PostgreSQL by se měla povolit protokolovat připojení.

Jak zobrazit dostupné aliasy Azure Policy:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Pochopení Azure Policych účinků:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice Azure Policy pro vaše Azure Database for PostgreSQL instance a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentaci:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforPostgreSQL můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforPostgreSQL můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro vaše Azure Database for PostgreSQL instance a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

Jak nakonfigurovat a spravovat Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: u Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k instancím Azure Database for PostgreSQL použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Database for PostgreSQL. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Jak integrovat se spravovanými identitami Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Postup vytvoření Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit Key Vault ověřování pomocí spravované identity:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: Azure Database for PostgreSQL Server podporuje ověřování Azure Active Directory pro přístup k databázím.  Při vytváření Azure Database for PostgreSQLho serveru poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů databáze.  

V případě Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k serveru Azure Database for PostgreSQL použijte Identita spravované služby ve spojení s Azure Key Vault ukládat a načítat přihlašovací údaje pro Azure Database for PostgreSQL Server. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Postup konfigurace spravovaných identit:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat se spravovanými identitami Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

Jak nastavit skener přihlašovacích údajů:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for PostgreSQL), ale neběží na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for PostgreSQL), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné**materiály: Azure Database for PostgreSQL přebírají zálohy datových souborů a transakčního protokolu. V závislosti na podporované maximální velikosti úložiště vezmeme úplné a rozdílové zálohy (4 TB max. servery úložiště) nebo zálohy snímků (až 16 TB maximálních úložných serverů). Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují pomocí šifrování AES 256-bit.

Postup zálohování serveru v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Principy počáteční konfigurace Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné**materiály: Azure Database for PostgreSQL automaticky vytvoří zálohy serveru a uloží je do místně redundantního nebo geograficky redundantního úložiště v závislosti na volbě uživatele. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou důležitou součástí jakékoli strategie pro provozní kontinuitu, protože chrání vaše data před náhodným poškozením nebo odstraněním.

Pokud chcete ukládat přihlašovací údaje pro instance Azure Database for PostgreSQL pomocí Azure Key Vault, zajistěte pravidelné automatizované zálohování vašich klíčů.

Postup zálohování serveru v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Postup zálohování klíčů Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné**materiály: v Azure Database for PostgreSQL obnovení vytvoří nový server ze zálohy původního serveru. K dispozici jsou dva typy obnovení: obnovení k bodu v čase a geografické obnovení. Obnovení k určitému bodu v čase je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server. Geografické obnovení je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a máte možnost obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

Pravidelně testujte obnovení instancí Azure Database for PostgreSQL.

Postup zálohování serveru v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Doprovodné**materiály: Azure Database for PostgreSQL přebírá úplné a rozdílové zálohy a zálohování protokolů transakcí. Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují pomocí šifrování AES 256-bit.

Principy zálohování a obnovení v Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie centra Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

Jak nastavit Azure Security Center kontakt zabezpečení:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

Postup konfigurace průběžného exportu:https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné**materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

V této části najdete další informace o strategii a provádění testování na základě červeného týmu a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
