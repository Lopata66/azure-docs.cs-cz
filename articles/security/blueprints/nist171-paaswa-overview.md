---
title: Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci pro SP NIST 800-171
description: Zabezpečení Azure a dodržování předpisů – PaaS webové aplikace podle NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127323"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci pro speciální publikace NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny, které pomáhá zákazníkům nasadit platformu jako službu (PaaS) webové aplikace v Azure, která implementuje podmnožinu NIST SP 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů. Slouží také jako základ pro zákazníky, kteří pro sestavení a nakonfigurovat vlastní webovou aplikaci v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky. Neměl by se používat jako-je v produkčním prostředí. Nasazení této architektury bez jakýchkoli úprav není dostatečná k SP NIST 800-171 zcela požadavkům. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů vytvořené pomocí této architektury řešení. Požadavky může lišit v závislosti na konkrétní implementaci každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty

Toto zabezpečení Azure a dodržování předpisů poskytuje referenční architektura pro webové aplikace PaaS pomocí Azure SQL Database back-endu. Webová aplikace je hostován v izolovaném prostředí služby App Service, což je privátním a vyhrazeném prostředí v datovém centru Azure. Prostředí rozloží provoz pro webovou aplikaci ve virtuálních počítačích (VM) spravuje Azure. Tato architektura také zahrnuje skupiny zabezpečení sítě (Nsg), Azure application gateway, Azure DNS a nástroje pro vyrovnávání zatížení Azure.

Pro rozšířené analýzy a generování sestav lze nastavit s indexy columnstore Azure SQL Database. Azure SQL Database můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci vybraného datového centra zákazníka pro odolnost proti chybám. Geograficky redundantní úložiště zajišťuje, že data jsou replikovat do sekundárního datového centra stovky mil okamžitě a znovu uložená jako tři kopie v rámci datového centra. Toto uspořádání zabraňuje za následek ztrátu dat nepříznivých událostí ve primárního datového centra zákazníka.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory (Azure AD) řízení přístupu na základě role (RBAC) se používá k řízení přístupu k nasazené prostředky. Tyto prostředky zahrnují klíče zákazníků ve službě Azure Key Vault. V nástroji Azure Monitor se monitoruje stav systému. Zákazníci nakonfigurovat tuto službu monitorování pro zachycení protokoly. Zobrazí se stav systému v jednom řídicím panelu, který se snadno používá.

Běžně je spravované SQL Database přes SQL Server Management Studio. Spuštění z místního počítače, který je nakonfigurovaný pro přístup k databázi SQL přes zabezpečené připojení Azure ExpressRoute nebo VPN.

Application Insights poskytuje správu výkonu aplikací v reálném čase a analytics přes protokoly Azure monitoru *společnost Microsoft doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a data importovat do odkazu architektury podsítě.*

![Webová aplikace PaaS pro diagram referenční architektury SP NIST 800-171](images/nist171-paaswa-architecture.png "PaaS webovou aplikaci pro SP NIST 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v tématu [architektura nasazení](#deployment-architecture) oddílu.

- Azure Virtual Machines
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (((1) /16 sítě
    - (4) /24 sítě
    - (4) skupiny zabezpečení sítě
- Azure Application Gateway
    - Brána firewall webových aplikací
        - Režim brány firewall: ochrany před únikem informací
        - Sada pravidel: OWASP
        - Port naslouchacího procesu: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Nástroj pro vyrovnávání zatížení Azure
- Azure Monitor (logs)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure Resource Manager**: [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) zákazníci lze použít pro práci s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci pomocí šablony pro nasazení. Šablona může fungovat v různých prostředích, jako je například testování, Fázování a produkce. Resource Manager poskytuje zabezpečení, auditování a označování příznaky funkcí, které pomáhá zákazníkům spravovat svoje prostředky po nasazení.

**Bastion host**: Bastion host je jediný bod položku, kterou uživatelé mohou používat pro přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Pro povolení provozu vzdálené plochy, musí být definován zdrojový provoz v této skupině.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Key Vault.
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače, když se nepoužívá.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povoleno spuštění přihlašovacích údajů a dalších tajných kódů v chráněném prostředí, která je oddělená od operačního systému.

**Webové aplikace**: [Webové aplikace](https://docs.microsoft.com/azure/app-service/) je funkce služby Azure App Service. Zákazníci, můžete tyto informace použít pro tvorbu a hostování webových aplikací v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysoká dostupnost. Podporuje Windows a Linux a umožňuje automatizované nasazení z Githubu, Azure DevOps nebo libovolného úložiště Git.

**App Service Environment**: [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce služby App Service. Poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací služby App Service ve velkém měřítku.

App Service environment je izolovaná na spuštění pouze jedné aplikace. Vždy je nasazena do virtuální sítě. Z důvodu izolace funkce referenční architektura obsahuje kompletní tenanta izolace a odebere se z víceklientské prostředí Azure. Zákazníci mají podrobnou kontrolu nad obě aplikace příchozí a odchozí síťový provoz. Aplikace můžete vytvářet vysokorychlostní zabezpečená připojení prostřednictvím virtuálních sítí a místním podnikovým prostředkům. Zákazníci můžou "škálování" s na základě zatížení metriky, k dispozici rozpočtu nebo definovaný plán služby App Service Environment.

Použití služby App Service Environment pro tuto architekturu poskytuje následující ovládací prvky a konfigurace:

- Hostování v zabezpečené virtuální síti Azure a pravidel zabezpečení sítě.
- Podepsaný svým držitelem interního nástroje pro vyrovnávání certifikát pro komunikaci přes protokol HTTPS. Jako osvědčený postup společnost Microsoft doporučuje použití důvěryhodné certifikační autority nabízí vyšší zabezpečení.
- [Interní režim vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (režim 3).
- Zakázat [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Změna [šifer TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Ovládací prvek [příchozí provoz N/W porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Firewall webových aplikací – omezení dat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Povolit [provoz Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
- Jedna skupina zabezpečení sítě pro službu Application Gateway
- Jedna skupina NSG pro App Service Environment
- Jedna skupina zabezpečení sítě pro službu SQL Database
- Jedna skupina zabezpečení sítě pro hostitele bastionu

Skupiny zabezpečení sítě obsahují konkrétní porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou uložené v účtu úložiště a povoleny.
  - Protokoly služby Azure Monitor je připojený k [diagnostiky skupin zabezpečení sítě a](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsítě**: Každá podsíť je přidružený jeho odpovídající skupiny zabezpečení sítě.

**Azure DNS**: V systému DNS (Domain Name) je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba určená pro domény DNS, která nabízí řešení názvů pomocí infrastruktury Azure. Hostování domény v Azure, uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturačních podmínek jako u ostatních služeb Azure. Azure DNS podporuje také privátní DNS domény.

**Azure Load Balancer**: [Nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lze zákazníkům škálovat svoje aplikace a poskytovat vysokou dostupnost služeb. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí a odchozí. Poskytuje nízkou latenci a vysokou propustnost a škálování až na úrovni milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: Pro splnění požadavků pro šifrovaná data v klidovém stavu, všechny [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato funkce pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené SP NIST 800-171.

**Azure Disk Encryption**: [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) používá funkci BitLocker systému Windows pro zajištění šifrování svazku pro datové disky. Řešení se integruje se službou Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: Instanci služby SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, když k nim dojde. Nabízí výstrahy zabezpečení podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé databázové vzory přístupu k.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v systému databáze. Po povolení šifrování dat pouze klientských aplikací nebo aplikačních serverů s přístupu ke klíčům můžou k datům ve formátu prostého textu.
- [Dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat neoprávněným uživatelům nebo aplikacím. Může automaticky zjistit potenciálně citlivých data a navrhnout odpovídající masky uplatňovat. Dynamické maskování dat pomáhá omezit přístup tak, aby citlivá data nejsou ukončení databáze prostřednictvím před neoprávněným přístupem. *Zákazníci odpovídají za nastavení dodržovat svoje schéma databáze.*

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro víceklientské cloudové adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří ve službě Azure AD a zahrnovat uživatele, kteří přistupují k databázi SQL.
-   Pomocí služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu Jak [integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Šifrování sloupců databáze také používá Azure AD k ověření aplikace ke službě SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správci definovat oprávnění velice přesně kontrolovat přístup. S ním že můžete udělit pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každý uživateli neomezený přístup pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k prostředkům a datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) je možné zákazníky, chcete-li minimalizovat počet uživatelů, kteří mají přístup k určité informace. Správci můžou používat Azure AD Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce také umožňuje vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci. Prozkoumá také podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [služby Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je hardwaru chráněné modulem zabezpečení 2048bitový klíč RSA.
- Všichni uživatelé a identity jsou udělena minimální požadovaná oprávnění pomocí RBAC.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Azure Security Center**: S [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Security Center také přistupuje k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Security Center používá celou řadu možností detekce zákazníkům potenciální útoky výstrahy, které se zaměřují jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se aktivují místo pořízením hrozby nebo podezřelé aktivity. Zákazníci můžou využít [vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Security Center nabízí výstrahy zabezpečení seřazené podle priority a incidentů. Security Center usnadňuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozeb. Reakce na incidenty týmy mohou pomocí sestavy, když vyšetřením a odstraněním hrozeb.

**Azure Application Gateway**: Architektura snižuje riziko ohrožení zabezpečení pomocí služby application gateway firewall webových aplikací nakonfigurované a sady pravidel OWASP povolena. Další možnosti patří:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim ochrany před únikem informací).
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) sady pravidel OWASP 3.0.
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondy stavu vlastní](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Security Center také poskytuje systém pověst.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly úložiště, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uživatelé můžou konfigurovat dobu uchování až do 730 dnů podle svých specifických požadavků.

**Protokoly Azure monitoru**: Protokoly jsou konsolidovány do [protokoly Azure monitoru](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění dat jsou uspořádány do samostatných tabulek pro jednotlivé datové typy v rámci pracovních prostorů Log Analytics. Tímto způsobem lze všechna data pohromadě, analyzovat bez ohledu na jejich původní zdroj. Security Center umožňuje integrací s protokoly Azure monitoru. Zákazníci mohou pomocí Kusto dotazy pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Azure [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontrola stavu služby Active Directory řešení posuzuje rizika a stav prostředí serveru v pravidelných intervalech. Poskytuje seznam seřazený podle priority doporučení specifická pro nasazenou serverové infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Zákazníkům přináší uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Řešení Agent Health sestavy, kolik agentů je nasazených a jejich geografické distribuce. Zároveň udává, kolik agentů jsou reagovat a počet agentů, které odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby SQL Database. Zákazníci můžou využít automatizaci [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožní snadno identifikovat změny v prostředí.

**Azure Monitor**: [Monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů. Organizace slouží k auditovat, vytvářet upozornění a archivovat data. Také mohou sledovat volání rozhraní API ve svých prostředků Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je služba správy výkonu rozšiřitelné aplikace pro webové vývojáře ve více platformách. Application Insights detekuje anomálie výkonu. Zákazníci můžou použít ho k monitorování živé webové aplikace. Application Insights zahrnuje výkonné analytické nástroje, které pomáhají zákazníkům diagnostikovat potíže a porozumíte, co uživatelé dělají s jejich aplikací. To&#39;s zákazníkům průběžně vylepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-paaswa-tm) nebo najdete tady. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace PaaS pro model hrozeb SP NIST 800-171](images/nist171-paaswa-threat-model.png "PaaS webovou aplikaci pro model hrozeb SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 PaaS webové aplikace ovládací prvek implementace matice](https://aka.ms/nist171-paaswa-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky jsou vyřešeny architektura pro webové aplikace PaaS. Obsahuje podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurován pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektury PaaS webové aplikace. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s využitím Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síť Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Vzhledem k tomu, že provoz v rámci tunelového připojení sítě VPN prochází přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí další ještě větší zabezpečení možností připojení. ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Připojení ExpressRoute připojují přímo k poskytovatele telekomunikačních zákazníka. Data v důsledku toho nebude procházet přes Internet a není vystavený. Tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
