---
title: Contoso – nastavení infrastruktury migrace | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso nastaví infrastruktury Azure pro migraci do Azure.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: raynew
ms.openlocfilehash: 6ee05af0391311b4782211807f41ce099a6c24a2
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889932"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso – nasazení infrastruktury migrace

V tomto článku Contoso připraví svoji místní infrastrukturu pro migraci a nastaví infrastrukturu Azure v rámci přípravy na migraci a vedení firmy v hybridním prostředí.

- Je ukázkové architektury, které jsou specifické pro společnost Contoso.
- Ať už budete potřebovat všechny prvky, které jsou popsané v tomto článku, závisí na strategii migrace. Například pokud vytváříte jen aplikace nativní pro cloud v Azure, může být nutné strukturu méně složité sítě.

Tento článek je součástí série článků, které zdokumentujte, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a řadu scénářů nasazení, které ukazují, jak nastavit infrastrukturu migrace vyhodnotit vhodnost migrace místních prostředků a spouštět různé druhy migrace. Scénáře jejich složitost v. V čase se přidají do série článků.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
Článek 2: Nasazení infrastruktury Azure | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | V tomto článku
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | SContoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.   
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje do aplikace pomocí služby Site Recovery k migraci virtuálních počítačů aplikace a Database Migration Service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso se dokončí migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje svou aplikaci osTicket Linux do virtuálních počítačů Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorovat aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migraci své aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure pomocí Pomocníka s migrací databáze. | K dispozici.    
[Článek 10: Refaktorovat Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje jeho Linuxovou aplikaci osTicket do webové aplikace Azure v několika lokalitách. Webová aplikace je integrovaná se službou GitHub pro průběžné doručování. Migrace databáze aplikace ke službě Azure Database for MySQL instance. | K dispozici.
[Článek 11: Refaktorovat a Team Foundation Server na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a app databáze s využitím Azure SQL Database. | K dispozici.    
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici. 

V tomto článku, který Contoso nastaví všechny prvky infrastruktury potřebné k dokončení všech scénářů migrace. 


## <a name="overview"></a>Přehled

Než Contoso můžete migrovat do Azure, je nezbytné k přípravě infrastruktury Azure.  Obecně platí jsou pět oblastí široké, Contoso se musí zvážit:

**Krok 1: Předplatná Azure**: Jak bude Contoso nákupu služeb Azure a komunikovat s platformou Azure a službami?  
**Krok 2: Hybridní identita**: Jak budete spravovat a řídit přístup k místním a prostředky Azure po migraci? Jak společnosti Contoso rozšířit nebo přesunout správy identit do cloudu?  
**Krok 3: Zotavení po havárii a odolnost**: Jak Contoso zajistí, že jeho aplikace a infrastruktury jsou odolné výpadků a havárií dojde-li?  
**Krok 4: Sítě**: Jak Contoso návrh síťové infrastruktury a navázat připojení mezi místním datacentrem a Azure?  
**Krok 5: Zabezpečení**: Jak se bude jejich zabezpečení nasazení hybridní nebo Azure?  
**Krok 6: Zásady správného řízení**: Jak zachovat nasazení v souladu s požadavky na zabezpečení a zásad správného řízení Contoso

## <a name="before-you-start"></a>Než začnete

Než začneme, prohlížení infrastrukturu, můžete chtít načíst některé základní informace o možnosti Azure, které si popíšeme v tomto článku:

- Existuje několik možností k nákupu Azure přístup, včetně s průběžnými platbami, smlouvy Enterprise (EA) programu Open Licensing od prodejců v systému Microsoft nebo z Microsoft Partners označované jako poskytovatele Cloud Solution Provider (CSP). Další informace o [možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/)a přečtěte si, jak [předplatná Azure jsou uspořádané](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Získejte přehled o Azure [správu identit a přístupu](https://www.microsoft.com/trustcenter/security/identity). Zejména, přečtěte si o [Azure AD a rozšíření místní služby AD do cloudu](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Je užitečné ke stažení e knihy o [Správa identit a přístupu (IAM) v hybridním prostředí](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure poskytuje robustní síťová infrastruktura s možnostmi pro hybridní připojení. Získejte přehled o [sítě a sítě řízení přístupu](https://docs.microsoft.com/azure/security/security-network-overview).
- Úvod k [zabezpečení Azure](https://docs.microsoft.com/azure/security/azure-security)a přečtěte si informace o vytváření plánu pro [zásad správného řízení](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Místní architektury

Zde je diagram znázorňující aktuální místní infrastrukturou společnosti Contoso.

 ![Architektura contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso má jeden hlavní datové centrum, které jsou umístěné v města New York východní USA.
- Existují tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí fiber metro ethernetová připojení (500 MB/s).
- Každou větev je místně připojen k Internetu pomocí obchodní třídy připojení s IPSec VPN tunely zpět do hlavního datového centra. Díky tomu celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Contoso má dva virtualizace hostitele ESXi 6.5 spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větve spustit na fyzických serverech.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Krok 1: Nákup a přihlášení k odběru do Azure

Contoso potřebuje zjistit, jak koupit Azure, navrhovat předplatná a licence služby a prostředky.

### <a name="buy-azure"></a>Koupit Azure

Contoso je dál [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). To zahrnuje zavážou k využívání služeb Azure, opravňující Contoso získat skvělé výhody – včetně flexibilních možností fakturace a optimalizované ceny.

- Odhadovaná Contoso, co bude své roční náklady na Azure. Když jej podepsat smlouvu, Contoso platili za první rok v plném rozsahu.
- Contoso je potřeba použít všechny závazky dřív, než skončí v roce nebo ke ztrátě hodnoty těchto dolarů.
- Pokud z nějakého důvodu Contoso překračuje jeho závazek a stráví více, Microsoft je fakturaci pro rozdíl.
- Žádné náklady vynaložené výše závazku bude účtovat stejné sazby a ty v kontraktu Contoso. Neexistují žádné následky nad.

### <a name="manage-subscriptions"></a>Správa předplatných

Contoso po platit za Azure, musí zjistit, jak spravovat předplatná Azure. Contoso má smlouvu EA, a proto neomezený počet předplatných Azure ji můžete nastavit.

- Registrace Azure Enterprise definuje jak společnost obrazce a používá služby Azure a definuje strukturu zásad správného řízení core.
- Jako první krok Contoso určil struktuře (označované jako vygenerované uživatelské rozhraní enterprise pro podnikové registrace. Contoso používá [v tomto článku](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance) vám pomůže pochopit a navrhnout vygenerované uživatelské rozhraní.
- Prozatím se rozhodl používat funkční přístup ke správě předplatných Contoso.
    - V podnikové síti se bude používat jednoho oddělení IT, která určuje rozpočet Azure. Toto bude jedinou skupinu s předplatnými.
    - Tento model contoso rozšíří v budoucnu, tak, aby další podnikové skupiny se může připojit k jako oddělením ve podnikového zápisu.
    - V IT oddělení společnosti Contoso má strukturované dva odběry, produkcí a vývojem.
    - Contoso potřebuje další předplatná v budoucnu, je potřeba spravovat přístup, zásady a dodržování předpisů u těchto předplatných. Společnosti Contoso, který se zavedením [skupin pro správu Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), jako další vrstvu nad předplatných.

    ![Struktura organizace](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Prozkoumejte licencování

S předplatnými nakonfigurovaný můžete si prohlédnout Contoso programů společnosti Microsoft. Licencování strategie závisí na prostředky, které chcete migrovat do Azure a jak jsou vybrané a nasadit virtuální počítače Azure a služby společnosti Contoso. 

#### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Při nasazování virtuálních počítačů v Azure, standardní Image zahrnují licenci, která se bude účtovat Contoso po minutách software používaný. Contoso však byl dlouhodobě zákazníků společnosti Microsoft a má spravuje EAs a otevřít licencí se Software Assurance (SA). 

Zvýhodněné hybridní využití Azure poskytuje nákladově efektivní metodu pro Contoso migrace tím, že ho a Ušetřete na virtuálních počítačích Azure a SQL Server úloh převedením nebo opětovné použití systému Windows Server Datacenter a licence k edici Standard kryté službou Software Assurance. To vám umožní Contoso nižší závislosti výpočetní sazbu za virtuální počítače a serveru SQL Server. [Další informace](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>License Mobility

Mobilitu licencí v rámci programu SA poskytuje zákazníkům multilicenčních programů společnosti Microsoft, například Contoso flexibilitu při nasazování nárokovatelných serverových aplikací s aktivním programem SA v Azure. Tím se eliminuje potřeba kupovat nové licence. S žádné poplatky za související s mobilitou můžete stávající licence snadno nasadit v Azure. [Další informace](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Rezervované instance pro úlohy s předvídatelným

Předvídatelné úlohy jsou ty, které vždy musí být k dispozici s virtuálními počítači spuštěná. Například řádek obchodní aplikace například systém SAP ERP.  Na druhé straně nepředvídatelnými úlohami jsou ty, které jsou proměnné. Například virtuální počítače, které jsou vysoce poptávky a vypnout v době mimo špičku.

![Rezervovaná instance](./media/contoso-migration-infrastructure/reserved-instance.png) 

Výměnou za použití rezervované instance pro konkrétní virtuální počítač instancí se musí udržovat velké dob trvání doby, konzoly, získáte slevy i kapacitu s určenou prioritou. Pomocí [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/)společně s programem Azure Hybrid Benefit Contoso můžete ušetřit až 82 % slevu regulární s průběžnými platbami ceny (duben 2018).


## <a name="step-2-manage-hybrid-identity"></a>Krok 2: Spravovat hybridní identita

Poskytuje tak a řízení přístupu uživatelů k prostředkům Azure pomocí správy identit a přístupu (IAM) je důležitým krokem při přijímání změn dohromady infrastruktury Azure.  

- Contoso se rozhodne vlastní místní službu Active Directory rozšířit do cloudu, místo vytvoření nového samostatného systému v Azure.
- Vytvoří služby založené na Azure Active Directory k tomu.
- Contoso nemá Office 365 na místě, takže je potřeba zřizovat nové služby Azure AD.
- Office 365 používá Azure AD pro správu uživatelů. Pokud Contoso se používá Office 365, byste už mít tenanta Azure AD a použít je jako primární služby AD.
- [Další informace](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) o službě Azure AD pro Office 365 a další [jak přidat předplatné](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) do existujícího tenanta Azure AD.

### <a name="create-an-azure-ad"></a>Vytvoření Azure AD

Contoso je pomocí bezplatné edice Azure AD, který je součástí s předplatným Azure. Správce společnosti Contoso adresář služby AD nastavíte takto:

1. V [webu Azure portal](http://portal.azure.com/), uživatel přejde na **vytvořit prostředek** > **Identity** > **Azure Active Directory**.
2. V **vytvořit adresář**, určí název adresáře, počáteční název domény a oblasti, ve kterém se vytvoří v adresáři Azure AD.

    ![Vytvoření služby Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Počáteční název domény má adresář, který je vytvořen ve formuláři **domainname.onmicrosoft.com**. Název nelze změnit ani odstranit. Místo toho musí přidat její registrovaný název domény do Azure AD.

### <a name="add-the-domain-name"></a>Přidání názvu domény

Použití názvu standardní domény, musí správci Contoso ho přidat jako vlastního názvu domény do Azure AD. Tato možnost umožňuje jim přiřadit známé uživatelská jména. Například se uživatel může přihlásit se pomocí e-mailovou adresu billg@contoso.com, namísto nutnosti billg@contosomigration.microsoft.com. 

Nastavit vlastní název domény, přidejte ho do adresáře, přidání položky DNS a ověřte název ve službě Azure AD.

1. V **vlastní názvy domén** > **přidat vlastní doménu**, přidávají domény.
2. Pro použití v Azure, které potřebují k registraci u svého registrátora domény záznam DNS. 

    - V **vlastní názvy domén** seznamu, poznamenejte si informace o DNS pro název. Používá záznam MX.
    - Potřebují přístup k názvu serveru k tomu. Přihlaste se do domény Contoso.com a vytvořit nový záznam MX položky DNS poskytuje Azure AD pomocí podrobností, které jste si poznamenali.  
1. Po rozšíření záznamů DNS v názvu podrobnosti pro doménu, klepnutím na **ověřte** ke kontrole vlastní název domény.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Nastavit místní a skupiny služby Azure a uživatelé

Teď, když Azure AD je vytvořená a spuštěná, Contoso musí správci přidat zaměstnancům, aby místní skupiny AD, které se bude synchronizovat se službou Azure AD. Musí používat místní názvy skupin, které shodovat s názvy skupin prostředků v Azure. Díky tomu pak snadno identifikujete shody pro účely synchronizace.

#### <a name="create-resource-groups-in-azure"></a>Vytvoření skupiny prostředků v Azure

Skupiny prostředků Azure shromažďování prostředků Azure. Pomocí ID skupiny prostředků umožňují na platformě Azure k provedení operací s prostředky v rámci skupiny.

- Předplatné Azure může mít více skupin prostředků, ale skupina prostředků může existovat pouze v rámci jednoho předplatného.
- Kromě toho jedna skupina prostředků může mít několik prostředků, ale prostředek může patřit pouze do jedné skupiny prostředků.

Správce společnosti Contoso nastavit skupiny prostředků Azure dle souhrnu v následující tabulce.

**Skupina prostředků** | **Podrobnosti**
--- | ---
**ContosoCobRG** | Tato skupina obsahuje všechny prostředky vztahující se k provozní kontinuity podnikání (PALICE).  Zahrnuje trezorů, které Contoso použije pro službu Azure Site Recovery a služba Azure Backup.<br/><br/> Bude také zahrnovat prostředky používané pro migraci, včetně Azure Migrate a Database Migration Service.
**ContosoDevRG** | Tato skupina obsahuje prostředky pro vývoj a testování.
**ContosoFailoverRG** | Tato skupina slouží jako cílová zóna se nezdařilo u prostředků.
**ContosoNetworkingRG** | Tato skupina obsahuje všechny síťové prostředky.
**ContosoRG** | Tato skupina obsahuje materiály související s produkčních aplikací a databází.

Vytváření skupiny prostředků následujícím způsobem:

1. Na webu Azure Portal > **skupiny prostředků**, přidávají skupiny.
2. Pro každou skupinu, zadejte název, předplatné, do které patří skupině a oblast.
3. Skupiny prostředků se zobrazí v **skupiny prostředků** seznamu.

    ![Skupiny prostředků](./media/contoso-migration-infrastructure/resource-groups.png) 

##### <a name="scaling-resource-groups"></a>Škálování skupiny prostředků

V budoucích verzích bude Contoso přidat jiné skupiny prostředků na základě potřeb. Například může definují skupinu prostředků pro každou aplikaci nebo službu, je možné spravovat a zabezpečit nezávisle na sobě.

#### <a name="create-matching-security-groups-on-premises"></a>Odpovídající zabezpečení skupiny místní vytvoření

1. V místní Active Directory, správce společnosti Contoso vytvořit skupiny zabezpečení s názvy, které odpovídají názvy skupin prostředků Azure.
 
    ![Místní skupiny zabezpečení služby AD](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Pro účely správy jejich vytvoření další skupiny, které se přidají do všech ostatních skupin. Tato skupina bude mít práva pro všechny skupiny prostředků v Azure. Omezený počet globální správci se přidají do této skupiny.

### <a name="synchronize-ad"></a>Synchronizace AD

Contoso chce poskytovat společnou identitu pro přístup k prostředkům místně i v cloudu. K tomuto účelu se bude integrovat s Azure AD v místní službě Active Directory. V tomto modelu:

- Uživatelé a organizace využít jednu identitu pro přístup k aplikacím – místních a cloudových služeb, jako je Office 365 nebo jiných webů na Internetu.
- Správci můžou využívat skupiny ve službě AD k implementaci [na základě řízení přístupu Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) v Azure.

Pro usnadnění integrace, společnost Contoso využívá [nástroj Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Když nainstalujete a nakonfigurujete nástroj na řadiči domény, synchronizuje místní místních identit AD do služby Azure AD. 

### <a name="download-the-tool"></a>Stáhněte si nástroj


1. Na webu Azure Portal, správce společnosti Contoso, přejděte na **Azure Active Directory** > **Azure AD Connect**a stáhněte si nejnovější verzi nástroje na server, při použití pro synchronizaci.

    ![Stažení AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Spuštění **AzureADConnect.msi** instalace s **použít Expresní nastavení**. Toto je nejběžnější instalace a jde použít pro jednoduchou doménovou strukturu s synchronizaci hodnot hash hesel pro ověřování.

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. V **připojit ke službě Azure AD**, zadejte přihlašovací údaje pro připojení k Azure AD (ve formuláři CONTOSO\admin nebo contoso.com\admin).

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. V **připojit ke službě AD DS**, zadejte přihlašovací údaje pro místní AD.

     ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. V **připraveno ke konfiguraci**, kliknou **po dokončení konfigurace spustit proces synchronizace** pro okamžité spuštění synchronizace. Pak nainstalují.

Poznámky:
- Contoso má přímé připojení k Azure. Pokud místní AD je za proxy serverem, přečtěte si tento [článku](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Po první synchronizaci pro místní objekty AD si můžete prohlédnout ve službě Azure AD.

    ![Místní AD v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT tým je reprezentován v každé skupině, na základě jeho role.

    ![Místní AD členů v Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Nastavení RBAC

Azure [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesnou správu přístupu pro Azure. Pomocí RBAC, můžete udělit pouze takovou úroveň přístupu, že uživatelé potřebují k provádění úloh. Můžete přiřadit příslušné role RBAC uživatelům, skupinám a aplikacím na úrovni oboru. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek. 

Contoso správce nyní přiřadí role do skupiny AD, které jsou synchronizované z místní.

1. V **ControlCobRG** skupinu prostředků, klikněte na tlačítko **řízení přístupu (IAM)** > **přidat přiřazení role**.
2. V **přidat přiřazení role** > **Role**, > **Přispěvatel**, vyberou **ContosoCobRG** skupiny AD ze seznamu. Skupiny se pak objeví v **Vybraní členové** seznamu. 
3. Opakujte tento postup se stejnými oprávněními pro jiné skupiny prostředků (s výjimkou **ContosoAzureAdmins**), přidáním oprávnění přispěvatele do účtu AD, který odpovídá skupině prostředků.
4. Pro **ContosoAzureAdmins** skupiny AD, přiřadí **vlastníka** role.

    ![Místní AD členů v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Krok 3: Návrh pro odolnost a po havárii

### <a name="set-up-regions"></a>Nastavení oblasti

Prostředky Azure se nasadí v rámci oblasti.

- Oblasti jsou uspořádané do zeměpisných oblastí a požadavky na rezidenci, suverenitu, dodržování předpisů a odolnosti dat se uplatní v zeměpisné oblasti.
- Oblast se skládá ze sady datových centrech. Tato datová centra jsou nasazené hraniční hranic s definovanou latencí a připojené prostřednictvím vyhrazené místní sítě s nízkou latencí.
- Každá oblast Azure je spárovaná s jinou oblastí pro odolnost proti chybám.
- Přečtěte si informace o [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/)a seznamte se s [jak spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso má rozhodnutí přejít k USA – východ 2 (nachází se ve Virginii) jako primární oblasti a střed USA (nachází se v Iowě) jako sekundární oblasti. Existuje několik důvodů:

- Contoso datacenter se nachází v New Yorku, a Contoso považovat za čekací doba na nejbližší datové centrum.
- Oblast východní USA 2 má služby a produkty, které je potřeba použít Contoso. Ne všechny oblasti Azure jsou stejné z hlediska produktů a služeb, které jsou k dispozici. Můžete zkontrolovat [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/).
- USA (střed) je spárované oblasti Azure v oblasti východní USA 2.

Jak operátoru o hybridní prostředí, je potřeba zvážit, jakým způsobem chcete sestavit oblasti návrhu odolnosti a strategii zotavení po havárii Contoso. Široce strategie rozsahu nasazení v jedné oblasti, které spoléhá na funkce jako doménami selhání a oblastní párování, díky kterému pro odolnost, prostřednictvím úplné aktivní / aktivní model cloudové služby a databáze jsou nasazené a údržby platformou Azure. uživatelé z dvou oblastech.

Contoso se rozhodl se střední cestách. Bude nasazovat aplikace a prostředky v primární oblasti a zachovat úplné kopie infrastruktury v sekundární oblasti, tak, aby byl připraven tak, aby fungoval jako úplné zálohování v případě havárie výsledné aplikace nebo selhání v oblasti.

### <a name="set-up-availability-zones"></a>Nastavení zón dostupnosti

Zóny dostupnosti ochraně aplikace a data před selháními datových center.

- Každá zóna dostupnosti je jedinečný fyzické umístění v rámci oblasti Azure.
- Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. 
- Existuje minimálně tří samostatných zón ve všech oblastech, povolené.
- Fyzické oddělení zóny v rámci oblasti chrání aplikace a data před selháními datových center.

Contoso nasadí dostupnost zóny jako aplikace volat pro škálovatelnost, vysokou dostupnost a odolnost proti chybám. [Další informace](https://docs.microsoft.com/azure/availability-zones/az-overview). 


## <a name="step-4-design-a-network-infrastructure"></a>Krok 4: Návrh síťové infrastruktury

V oblasti návrhu na místě je připraven k vezměte v úvahu síťové strategie Contoso. Je potřeba uvažovat o jak v místním datacentrem a Azure a komunikovat mezi sebou a postup návrhu sítě infrastruktury v Azure. Konkrétně Contoso potřebuje:

- **Plánování hybridního připojení k síti**: Zjistěte, jak chce připojit sítě napříč místními a Azure.
- **Návrh infrastruktury Azure network**: Rozhodněte, jak se nasadí sítí nad oblastí. Způsob sítě komunikace v rámci stejné oblasti a v oblastech?
- **Návrh a nastavení sítě Azure**: Nastavení sítě Azure a podsítě a rozhodnout, co se bude nacházet v nich.

### <a name="plan-hybrid-network-connectivity"></a>Plánování hybridního připojení k síti

Contoso považovat za [počet architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) pro hybridní sítě mezi Azure a místním datovým centrem. [Přečtěte si další](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) srovnávající možnosti.

Připomínáme Contoso místní síťové infrastruktury se skládá právě datového centra v New Yorku, a místní větve ve východní části USA.  Všechna místa mít obchodní třídy připojení k Internetu.  Každé větve je připojen k datovému centru prostřednictvím tunelu IPSec VPN přes internet.

![Síť společnosti Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Zde je, jak společnosti Contoso rozhodla implementovat hybridní možnosti připojení:

1. Nastavte nové připojení VPN typu site-to-site mezi datovým centrem Contoso v New Yorku a tyto dvě oblasti Azure v oblastech východní USA 2 a USA (střed).
2. Větev office provoz směřující k virtuálním sítím Azure bude směrovat přes hlavní datové centrum společnosti Contoso. 
3. Jak Contoso škálování nasazení v Azure, naváže se připojení ExpressRoute mezi datovým centrem a oblastmi Azure. Pokud k tomu dojde, Contoso si zachovají připojení site-to-site VPN pro převzetí služeb při selhání pouze pro účely.
    - [Další informace](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o výběru mezi hybridní řešení sítě VPN a ExpressRoute.
    - Ověřte [ExpressRoute umístění a podporu](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Pouze sítě VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Připojení VPN a ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Návrh infrastruktury sítě Azure

Je velmi důležité, že Contoso umístí sítí místo tak, aby díky hybridním nasazení bezpečná a škálovatelná. K tomuto účelu Contoso trvá dlouhodobý přístup a návrhu virtuálních sítí (VNets) odolné proti chybám a připraveno pro podniky. [Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o plánování virtuální sítě.

Pro připojení dvou oblastech, se rozhodl implementaci modelu rozbočovače k centrální síti Contoso:

- V rámci každé oblasti bude Contoso používají model střed a paprsek.
- Pro připojení, sítě a rozbočovače, Contoso použije Azure vnet peering.

#### <a name="network-peering"></a>Vnet peering

Azure poskytuje sítě partnerských vztahů pro připojení virtuálních sítí a rozbočovače. Globální partnerský vztah umožňuje připojení mezi virtuálními sítěmi/hubs v různých oblastech. Místní vytvoření partnerského vztahu se připojí virtuální sítě ve stejné oblasti. Partnerský vztah virtuální sítě poskytují celou řadu výhod:

- Síťový provoz mezi partnerskými virtuálními sítěmi je privátní.
- Provoz mezi virtuálními sítěmi, zůstane na páteřní síti Microsoftu. Při komunikaci mezi virtuálními sítěmi je požadována bez veřejného Internetu, brány nebo šifrování.
- Vytvoření partnerského vztahu představuje výchozí, s nízkou latencí a velkou šířku pásma při propojení prostředků v různých virtuálních sítích.

[Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o vnet peering.

#### <a name="hub-to-hub-across-regions"></a>Centrum pro rozbočovač napříč oblastmi

Contoso nasadí rozbočovač v jednotlivých oblastech. Centrum je virtuální síť (VNet) v Azure, která funguje jako ústřední bod připojení k vaší místní síti. Centrum virtuálních sítí se připojit k sobě navzájem pomocí globální VNet peering. Globální VNet peering se připojí virtuální sítě v různých oblastech Azure.

- Centrum v jednotlivých oblastech má partnerský vztah pro své Centrum partnera v jiné oblasti.
- Centrum je v partnerském vztahu do každé sítě v jeho oblast a může připojit ke všem síťovým prostředkům.

    ![Globální partnerský vztah](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Střed a paprsek v rámci oblasti

V rámci každé oblasti nasadí Contoso virtuálních sítí pro různé účely, jako paprsky sítě z centra oblasti. Virtuální sítě v rámci oblasti slouží k připojení do svého centra a k sobě navzájem partnerský vztah.

#### <a name="design-the-hub-network"></a>Návrh centrální síti

V rámci model střed a paprsek, který se rozhodl Contoso musí přemýšlet o tom, provoz z místního datového centra a z Internetu, se budou směrovat. Zde je, jak se rozhodl zpracovávají směrování pro USA – východ 2 a USA (střed) hubs Contoso:

- Contoso je navrhování sítě označované jako "reverzní c", je to cesta, která příchozí pro odchozí síťové pakety podle pokynů.
- Architektura sítě má dvě hranice, zóně nedůvěryhodné front-endu hraniční a zóny důvěryhodných serverů back-end.
- Brána bude mít na síťový adaptér v každé zóně, řízení přístupu do zóny důvěryhodných serverů.
- Z Internetu:
    - Přenosy z Internetu, se setkají s vyrovnáváním zatížení veřejnou IP adresu v hraniční síti.
    - Tento provoz je směrován přes bránu firewall, v souladu s pravidly brány firewall.
    - Po dokončení implementace jsou řízení přístupu k síti, provoz se předají do příslušného umístění, do zóny důvěryhodných serverů.
    - Odchozí provoz z virtuální sítě se budou směrovat do Internetu použijte trasy definované uživatelem (udr). Provoz se vynucené přes bránu firewall a kontrole souladu se zásadami společnosti Contoso.
- Z datového centra společnosti Contoso:
    - Příchozí provoz přes VPN typu site-to-site (nebo ExpressRoute) narazí na veřejnou IP adresu brány Azure VPN.
    - Provoz je směrován přes bránu firewall a můžou se pravidla brány firewall.
    - Po použití pravidla brány firewall, je předán provoz interní nástroj pro vyrovnávání zatížení (standardní skladové položky) na důvěryhodné zóny interní podsítě.
    - Odchozí provoz z podsítě pro důvěryhodného do místního datového centra prostřednictvím sítě VPN je směrován přes brány firewall a pravidla platila před přechodem přes připojení site-to-site VPN.



### <a name="design-and-set-up-azure-networks"></a>Návrh a nastavení sítě Azure

Contoso je síť a směrování topologie v místě, připraveno k nastavení sítě Azure a podsítě.

- Contoso se implementace třídy A privátní sítě v Azure (0.0.0.0-127.255.255.255). Tento postup funguje, od místní má v současnosti třídy B privátní adresní prostor 172.160.0/16 tak společnost Contoso může být nesmí být žádné překrytí rozsahy adres.
- Bude se to nasazení virtuální sítě v primárních a sekundárních oblastech.
- Společnost Contoso použije zásady vytváření názvů, který obsahuje předponu **VNET** a oblasti – zkratka **EUS2** nebo **kapacitní jednotky**. Pomocí této normy, bude mít název sítě centra **připojení typu VNET-HUB-EUS2** (východní USA 2), a **připojení typu VNET-HUB-kapacitních jednotek** (centrální USA).
- Contoso nemá [řešení IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), takže je potřeba naplánovat směrováním v síti bez překladu adres.


#### <a name="virtual-networks-in-east-us-2"></a>Virtuální sítě v oblasti východní USA 2

Východní USA 2 je primární oblasti, kterou Contoso použije k nasazení prostředků a služeb. Zde je, jak bude Contoso navrhovat sítě:

- **Hub**: Virtuální síť v oblasti východní USA 2 centrum je ústřední bod primární připojení k místnímu datovému centru.
- **Virtuální sítě**: Virtuální sítě paprsků v oblasti východní USA 2 lze použít k izolaci úloh v případě potřeby. Kromě virtuální síť centra budou mít Contoso paprsku dvou virtuálních sítí v oblasti východní USA 2:
    - **PŘIPOJENÍ TYPU VNET-DEV-EUS2**. Tuto virtuální síť se poskytují vývoj a testování tým s plně funkční síť pro vývojových projektů. Bude fungovat jako oblast pilotního nasazení produkčního prostředí a bude záviset na provozní infrastruktuře na funkci.
    - **VNET-PROD-EUS2**: Komponenty produkčního prostředí Azure IaaS budou umístěny v této síti. 
    -  Každá virtuální síť bude mít svůj vlastní jedinečný adresní prostor se nepřekrývá. Contoso v úmyslu nakonfigurovat směrování bez NAT
- **Podsítě**:
    - Bude obsahovat podsíť každá síť pro každou vrstvu aplikace
    - Každá podsíť v produkční síti bude mít odpovídající podsítě ve virtuální síti vývoje.
    - Kromě toho produkční síť obsahuje podsítě pro řadiče domény.

V následující tabulce jsou shrnuté virtuálních sítí v oblasti východní USA 2.

**VNet** | **rozsah** | **Peer**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![Centra/paprsky v primární oblasti](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Podsítě ve východní USA 2 centra sítě (virtuální síť-HUB-EUS2)

**Podsíť/zóny** | **CIDR** | ** Použitelných IP adres
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Podsítě ve východní USA 2 vývojový sítě (VNET-DEV-EUS2)

Vývoj pro virtuální síť se používá vývojový tým jako oblast pilotního nasazení produkčního prostředí. Má tři podsítě.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Virtuální počítače databáze


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Podsítě ve východní USA 2 produkční sítě (VNET-PROD-EUS2)

Komponenty Azure IaaS jsou umístěné v podnikové síti. Každá úroveň aplikace má vlastní podsíti. Podsítě odpovídají polím v síti vývoje, a uveďte podsítě pro řadiče domény.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Virtuální počítače databáze
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | Virtuální počítače řadiče domény


![Centrum architektury sítě](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Virtuální sítě v oblasti střed USA (sekundární oblast)

USA (střed) je sekundární oblasti společnosti Contoso. Zde je, jak bude Contoso navrhovat sítě:

- **Hub**: Centrum, virtuální síť v oblasti východní USA 2 je ústřední bod připojení k místním datovým centrem a paprsku, virtuálních sítí v oblasti východní USA 2 se dá použít k izolaci úloh v případě potřeby spravovat odděleně od ostatních paprsků.
- **Virtuální sítě**: Contoso bude mít dvě virtuální sítě v oblasti střed USA:
    - VNET-PROD-CUS. Tuto virtuální síť je produkční sítě, podobně jako VNET PROD_EUS2. 
    - VNET-ASR-CUS. Tuto virtuální síť bude sloužit jako umístění, ve kterém se vytvoří virtuální počítače po převzetí služeb při selhání z místního nebo jako umístění pro virtuální počítače Azure, které při selhání z primární do sekundární oblasti. Tato síť se podobá produkčních sítích, ale bez všechny řadiče domény na něj.
    -  Každá virtuální síť v oblasti budou mít svůj vlastní adresní prostor se nepřekrývá. Contoso se konfigurace směrování bez překladu adres.
- **Podsítě**: Podsítě se navržený v podobným způsobem jako ty v oblastech východní USA 2. Výjimkou je, že Contoso nemusí podsíť pro řadiče domény.

Virtuální sítě v oblasti střed USA jsou shrnuty v následující tabulce.

**VNet** | **rozsah** | **Peer**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Centra/paprsky v spárované oblasti](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Podsítě v centrální USA centrální síti (virtuální síť centra CUS)

**Podsíť** | **CIDR** | **Použitelných IP adres**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**Podsíť brány** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Podsítě v centrální nás produkční sítě (VNET-PROD-kapacitní jednotky)

Paralelní do produkční sítě v primární oblasti USA – východ 2 je produkční sítě v sekundární oblasti USA (střed). 

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | Virtuální počítače databáze
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | Virtuální počítače řadiče domény

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Podsítě v síti převzetí služeb při selhání nebo obnovení centrální USA v oblasti střed USA (virtuální sítě Azure Site Recovery CUS)

Kapacitní jednotky virtuální sítě Azure Site Recovery síť se používá pro účely převzetí služeb při selhání mezi oblastmi. Site Recovery se použije k replikaci a převzetí služeb při selhání virtuálních počítačů Azure mezi regiony. Taky funguje jako datacenter společnosti Contoso na síť Azure pro chráněné úlohy, které zůstanou v místním, ale převzetí služeb při selhání do Azure pro zotavení po havárii.

Kapacitní jednotky virtuální sítě Azure Site Recovery je základní stejné podsíti jako produkční virtuální síť v oblasti východní USA 2, ale bez nutnosti pro podsíť řadiče domény.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | Virtuální počítače databáze

![Centrum architektury sítě](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Nakonfigurovat partnerské připojení

Centrum v jednotlivých oblastech se jde navázat partnerský vztah k centru v jiné oblasti a všechny virtuální sítě v rámci oblasti rozbočovače. To umožňuje centra ke komunikaci a chcete-li zobrazit všechny virtuální sítě v rámci oblasti. Poznámky:

- Vytvoření partnerského vztahu se vytvoří připojení oboustranný. Od zahájení druhé strany na první virtuální sítě a jiný v druhé sítě VNet.
- V hybridním nasazení provozu, který předává mezi rovnocennými počítači musí být viditelné z připojení VPN mezi místním datacentrem a Azure. Chcete-li povolit, existují některé konkrétní nastavení, které musí být nastavena na partnerské připojení.

Pro všechna připojení z virtuální sítě paprsků prostřednictvím centra k místnímu datovému centru společnosti Contoso musí povolit provoz tak, aby předané a propojeními VPN Gateway.

##### <a name="domain-controller"></a>Řadič domény

Pro řadiče domény v síti VNET-PROD-EUS2 Contoso chce, aby se tok mezi EUS2 centra/produkční síť i přes připojení VPN do místního provozu. Správce společnosti Contoso provést toto it musí povolit následující možnosti:

1. **Povolit přesměrovaný přenos** a **povolit průchod konfigurace brány** partnerské připojení. V našem příkladu to může být připojení typu VNET-HUB-EUS2 připojení VNET-PROD-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering1.png)

2. **Povolit přesměrovaný přenos** a **používat vzdálené brány** na druhé straně partnerského vztahu, na připojení typu VNET-PROD-EUS2 připojení VNET-HUB-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering2.png)

3. V místním, které budete nastavili statickou trasu, která směruje místní provoz směrovat přes tunelové propojení sítě VPN do virtuální sítě. Konfigurace by dokončení na bráně, která poskytuje tunelového připojení sítě VPN ze společnosti Contoso do Azure. Používají pro tuto službu RRAS.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Produkční sítě 

Síť spoked peer neuvidí spoked partnerské sítě v jiné oblasti přes rozbočovač.

Pro produkční sítě společnosti Contoso v obou oblastech zobrazíte mezi sebou správce společnosti Contoso potřeba vytvořit přímé partnerské propojení pro připojení typu VNET-PROD-EUS2 a zakázat-PROD-kapacitní jednotky. 

![Partnerské vztahy](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Nastavení DNS

Při nasazování prostředků ve virtuálních sítích, máte několik možností pro překlad názvů domén. Můžete použít překlad poskytovaný platformou Azure, nebo zadejte servery DNS pro překlad. Typ překladu adres, které používáte závisí na tom, jak vaše prostředky musejí komunikovat mezi sebou. Získat [informace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) o službě Azure DNS.

Správce společnosti Contoso se rozhodli, že služba Azure DNS není vhodná v hybridním prostředí. Místo toho jsou teď využívají místní servery DNS.

- Protože se jedná o hybridní síť všechny virtuální počítače místní a v Azure musí být schopné překládat názvy fungovat správně. To znamená, že je nutné použít vlastní nastavení DNS na všechny virtuální sítě.
- Contoso má aktuálně nasazené v datovém centru společnosti Contoso a v pobočkách řadiče domény. Primární servery DNS nejsou CONTOSODC1(172.16.0.10) a CONTOSODC2(172.16.0.1)
- Při nasazení virtuální sítě, nastaví se má být použit jako servery DNS v sítích místních řadičích domény. 
- Chcete-li nastavit tuto konfiguraci, při využití vlastního DNS ve virtuální síti, musí být Azure rekurzivní překladače IP adresu (například adresy 168.63.129.16) přidané do seznamu DNS.  K tomuto účelu Contoso nakonfiguruje nastavení serveru DNS na každé virtuální síti. Například vlastní nastavení DNS pro připojení typu VNET-HUB-EUS2 sítě by měl vypadat takto:
    
    ![Vlastní DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Kromě řadičů domény v místním se chystáte Contoso implementovat čtyři další pro podporu sítě Azure, dvě pro každou oblast. Zde je, co bude Contoso nasazují v Azure.

**Oblast** | **DC** | **VNet** | **Podsíť** | **IP adresa**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
KAPACITNÍ JEDNOTKY | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
KAPACITNÍ JEDNOTKY | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

Po nasazení místních řadičích domény, je potřeba aktualizace nastavení DNS v sítích na buď oblasti mají být zahrnuty nových řadičů domény seznam serverů DNS společnosti Contoso.



#### <a name="set-up-domain-controllers-in-azure"></a>Nastavení řadiče domény v Azure

Po aktualizaci nastavení sítě, správce společnosti Contoso připraveni k sestavení řadičů domény v Azure.

1. Na webu Azure Portal jejich nasazení nového virtuálního počítače Windows serveru do příslušné virtuální sítě.
2. Vytváření skupiny dostupnosti v každém umístění pro virtuální počítač. Skupiny dostupnosti, postupujte takto:
    - Ujistěte se, že prostředky infrastruktury Azure rozděluje virtuálních počítačů do jiné infrastruktury v oblasti Azure. 
    -  Umožňuje společnosti Contoso se 99,95 % smlouva SLA pro virtuální počítače v Azure.  [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Skupina dostupnosti](./media/contoso-migration-infrastructure/availability-group.png) 
3. Po nasazení virtuálního počítače, otevřete místní síťové rozhraní virtuálního počítače. Nastavte na statickou privátní IP adresu a zadejte platnou adresu.

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. Nyní jsou připojit nový datový disk k virtuálnímu počítači. Tento disk obsahuje databázi služby Active Directory a sdílené složky sysvol. 
    - Velikost disku určí počet vstupně-výstupních operací, který ji podporuje.
    - V čase velikost disku může být nutné zvýšit růst objemu prostředí.
    - Jednotka neměla být nastavena pro čtení a zápis pro použití mezipaměti u hostitele. Databáze služby Active Directory to nepodporuje.

     ![Active Directory disku](./media/contoso-migration-infrastructure/ad-disk.png)

5. Po přidání disku, připojení k virtuálnímu počítači přes vzdálenou plochu a otevřete Správce serveru.
6. Potom v **Souborová služba a služba úložiště**, spuštění Průvodce vytvořením svazku, zajištění, že jednotka je daný písmenem F: nebo vyšší na místním virtuálním počítači.

     ![Průvodce vytvořením svazku](./media/contoso-migration-infrastructure/volume-wizard.png)

7. Ve Správci serveru, přidejte **Active Directory Domain Services** role. Poté se nakonfigurovat virtuální počítač jako řadič domény.

      ![Role serveru](./media/contoso-migration-infrastructure/server-role.png)  

9. Po virtuální počítač je nakonfigurovaný jako řadič domény a restartován, otevřete Správce DNS a nakonfigurovat překladač Azure DNS jako server pro předávání.  To umožňuje řadič domény pro předávání dotazů DNS, které nelze vyřešit v Azure DNS.

    ![Server DNS pro předávání](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Teď se aktualizovat vlastní nastavení DNS pro každou virtuální síť pomocí příslušného řadiče domény pro oblasti virtuální sítě. Patří mezi ně řadiče místní domény v seznamu.

### <a name="set-up-active-directory"></a>Nastavení služby Active Directory

AD je důležité služby v síti a musí být správně nakonfigurované. Správce společnosti Contoso se sestaví servery AD pro datové centrum společnosti Contoso a pro oblasti EUS2 a kapacitní jednotky.  

1. Vytvoří dvě nové lokality (AZURE EUS2 a AZURE CUS) spolu s web datového centra (ContosoDatacenter).
2. Po vytvoření webů, jejich vytvoření podsítí v lokalitách, tak, aby odpovídaly virtuální sítě a datacenter.

    ![Řadič domény podsítě](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Potom vytvořte dvě spojení sítí pro všechno, co připojení. Řadiče domény by měl poté přesunut do jejich umístění.

    ![Řadič domény odkazy](./media/contoso-migration-infrastructure/dc-links.png)

4. Po dokončení konfigurace všechno, co replikační topologie služby Active Directory je na místě.
    
    ![Replikace řadiče domény](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Všechno dokončeno seznam řadičů domény a servery zobrazují v Centru pro správu v místním Active Directory.

    ![Centrum pro správu AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Krok 5: Plánování zásad správného řízení

Azure poskytuje celou řadu ovládacích prvků zásad správného řízení napříč službami a platformou Azure. [Přečtěte si další](https://docs.microsoft.com/azure/security/governance-in-azure) pro základní znalosti o možnosti.

Konfigurace identit a řízení přístupu, Contoso již začala chcete změnit některé aspekty zabezpečení a zásad správného řízení na místo. Obecně existují tři oblasti, je potřeba vzít v úvahu:

- **Zásady**: Zásady v Azure se vztahuje a vynucuje pravidla a efekty u vašich prostředků tak, aby prostředky i nadále dodržovalo firemní požadavky a smlouvy o úrovni služeb.
- **Zamkne**: Azure vám umožní zámek předplatná, skupiny prostředků a další prostředky, tak, aby se moci upravovat jenom ti s autoritou Uděláte to tak.
- **Značky**: Prostředky můžete řídit, auditovat a spravovat pomocí značky. Značky připojit k prostředkům, poskytnutí informací o prostředky a vlastníci metadat.

### <a name="set-up-policies"></a>Nastavení zásad

Službu Azure Policy vyhodnotí prostředky, vyhledává ty, které nejsou kompatibilní s definic zásad, které máte na místě. Například může mít zásadu, která pouze umožňuje určitého typu virtuálních počítačů, nebo vyžaduje prostředky s konkrétní značkou. 

Zásady Azure zadejte definici zásady a přiřazení zásad určuje obor, ve kterém se mají uplatnit zásady. Rozsah musí být v rozsahu skupiny pro správu do skupiny prostředků. [Přečtěte si](../governance/policy/tutorials/create-and-manage.md) o vytváření a Správa zásad.

Contoso chce začít pracovat s několika zásad:

- Chce zásadu, která Ujistěte se, že prostředky je možné nasadit jenom v oblastech EUS2 a kapacitní jednotky.
- Chce omezit SKU virtuálních počítačů na pouze schválené SKU. Záměrem je zajistit, že nejsou použity nákladné skladové položky virtuálních počítačů.

#### <a name="limit-resources-to-regions"></a>Omezení prostředků do oblastí

Společnost Contoso využívá předdefinovanou definici zásady **povolená umístění** k omezení prostředků oblastech.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby**a vyhledejte **zásady**.
2. Vyberte **přiřazení** > **přiřazení zásad**.
3. Vyberte v seznamu zásad **povolená umístění**.
4. Nastavte **oboru** název předplatného Azure a vyberte tyto dvě oblasti v seznamu povolených aplikací.

    ![Zásady povolené oblastí](./media/contoso-migration-infrastructure/policy-region.png)

5. Ve výchozím nastavení je zásada nastavená s **Odepřít**, to znamená, že pokud, někdo spustí nasazení v rámci předplatného, který není v EUS2 nebo kapacitní jednotky, instalace se nezdaří. Zde je, co se stane, když někdo v rámci předplatného Contoso pokusí nastavení nasazení v oblasti západní USA.

    ![Zásady se nezdařilo](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Povolit konkrétní skladové položky virtuálních počítačů

Společnost Contoso použije předdefinovanou definici zásady **měly virtuální počítače SKU** pro omezení typů virtuálních počítačů, které mohou být vytvořeny v rámci předplatného. 

![Skladová položka zásad](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Zkontrolovat dodržování zásad

Zásady vejdou v platnost okamžitě a Contoso můžete zkontrolovat prostředky pro dodržování předpisů. 

1. Na webu Azure Portal, klikněte na tlačítko **dodržování předpisů** odkaz.
2. Zobrazí se řídicí panel pro dodržování předpisů. Další podrobnosti můžete procházet hierarchii.

    ![Dodržování zásad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Nastavení uzamčení

Contoso dlouho používá rozhraní ITIL pro správu systémů. Jedním z nejdůležitějších aspektů rozhraní framework je řízení změn a Contoso chce se ujistit, že řízení změn je implementována v nasazení Azure.

Contoso přechází k implementaci zámky následujícím způsobem:

- Jakékoli produkční nebo převzetí služeb při selhání součásti musí být ve skupině prostředků, který má zámek ReadOnly.  To znamená, že k úpravě nebo odstranění položek produkčního prostředí, musí odebrat zámek. 
- Skupiny prostředků v neprodukčním prostředí budou mít CanNotDelete zámky. To znamená, že oprávnění uživatelé můžou číst nebo upravovat prostředek, ale nelze odstranit.

[Další informace](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) o zámky.

### <a name="set-up-tagging"></a>Nastavit značky

Ke sledování prostředky, jako jste přidali, je čím dál důležitější pro Contoso na prostředky přidružit příslušné oddělení, zákazníků a prostředí. 

Kromě poskytování informací o prostředcích a vlastníky, značky vám umožní Contoso, agregace a skupiny prostředků a použít tato data pro účely vracení peněz.

Contoso potřebuje vizualizovat své prostředky Azure způsobem, který dává smysl pro firmy. Například tím, že role nebo oddělení. Všimněte si, že prostředky nemusí nacházet ve stejné skupině prostředků se stejnou značkou. Contoso se vytvoří taxonomie jednoduché značky, tak, aby všichni používají stejný značky.

**Název značky** | **Hodnota**
--- | ---
Nákladové středisko | 12345: Musí být platný nákladových středisek od SAPU.
BusinessUnit | Název organizační jednotky (od SAPU.). Matches CostCenter.
ApplicationTeam | E-mailový alias týmu, který vlastní podporu pro aplikaci.
CatalogName | Název aplikace nebo ShareServices, za katalogu služeb, kterou prostředek podporuje.
Portálu Service Manager | E-mailový alias ITIL Service Manager pro prostředek.
COBPriority | Priorita nastavena obchod pro zajištění BCDR. Hodnoty 1 – 5.
ENV | Možné hodnoty jsou PROD DEV, STG. Představující, vývoj, přípravným a produkčním prostředím.

Příklad: 

 ![Azure značky](./media/contoso-migration-infrastructure/azure-tag.png)

Po vytvoření značky Contoso přejděte zpět a vytvoření nových definic Azure policy a přiřazení k vynucení použití požadované značky celé organizace.


## <a name="step-6-consider-security"></a>Krok 6: Zvažte zabezpečení

V cloudu je zásadní zabezpečení a Azure nabízí širokou škálu nástrojů zabezpečení a možnosti. Ty umožňují vytvářet bezpečná řešení, na zabezpečené platformě Azure. Čtení [jistota v důvěryhodném cloudu](https://azure.microsoft.com/overview/trusted-cloud/) získat další informace o zabezpečení Azure.

Existuje několik aspektů pro společnost Contoso vzít v úvahu:

- **Azure Security Center**: Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Se službou Security Center můžete používat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat útoky a reagovat na ně.  [Další informace](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Skupiny zabezpečení sítě (Nsg)**: Skupina zabezpečení sítě je filtr (Brána firewall), který obsahuje seznam zabezpečení pravidla, která při použití, povolit nebo odpírají síťový provoz prostředků připojených k virtuálním sítím Azure. [Další informace](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Šifrování dat**: Azure Disk Encryption je funkce, která umožňuje šifrovat disky virtuálních počítačů Windows a Linuxem v režimu IaaS. [Další informace](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Práce s Azure Security Center

Contoso je hledáte rychlý přehled o stavu zabezpečení jeho nové hybridní cloud a konkrétně jeho úloh Azure.  Proto Contoso se rozhodla implementovat Azure Security Center spuštění s následujícími funkcemi: 

- Centralizovaná správa zásad
- Nepřetržité posuzování
- Užitečná doporučení 

#### <a name="centralize-policy-management"></a>Centralizujte si správu zásad

Centralizovaná správa zásad bude Contoso zajistit soulad s požadavky na zabezpečení díky centrální správě zásad zabezpečení napříč celým prostředím. To můžete rychle a jednoduše implementovat zásady, které se vztahuje na všechny její prostředky Azure.

![Zásady zabezpečení](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Vyhodnocení a akce

Contoso bude využívat posouzení zabezpečení, která monitoruje zabezpečení počítačů, sítí, úložiště, dat a aplikací. Chcete-li zjistit potenciální potíže se zabezpečením. 

- Security Center analyzuje stav zabezpečení výpočetních společnosti Contoso, infrastruktury a datovým prostředkům a aplikace Azure a služeb.
- Nepřetržité posuzování pomáhá provozní tým Contoso zjistit potenciální problémy se zabezpečením, jako jsou systémy s chybějícími aktualizacemi zabezpečení nebo zveřejněné síťové porty. 
- Zejména Contoso chce zajistit, aby že všechny virtuální počítače jsou chráněné. Security Center pomáhá to ověření stavu virtuálních počítačů a provádění užitečným doporučení k nápravě ohrožení zabezpečení dříve, než jste zneužít.

![Monitorování](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Práce pomocí skupin zabezpečení sítě

Společnost Contoso může omezení síťového provozu směřujícího do prostředků ve virtuální síti pomocí skupin zabezpečení sítě.

- Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odepírají příchozí nebo odchozí síťový provoz v závislosti na zdrojové nebo cílové IP adrese, portu a protokolu.
- Při použití k podsíti, pravidla se použijí na všechny prostředky v podsíti. Kromě síťových rozhraní jedná se instance služeb Azure v podsíti nasazené.
- Skupiny zabezpečení aplikací (asg) umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace, umožňuje seskupení virtuálních počítačů a definovat zásady zabezpečení sítě na základě těchto skupin.
    - Skupiny zabezpečení aplikací znamená, že Contoso můžete znovu použít zásady zabezpečení ve velkém měřítku bez potřeby ruční údržby explicitních IP adres. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku.
    - Contoso můžete určit skupinu zabezpečení aplikace jako zdroj a cíl v pravidlu zabezpečení. Po definování zásady zabezpečení společnosti Contoso můžete vytvořit virtuální počítače a přiřaďte síťová rozhraní virtuálního počítače do skupiny. 


Contoso implementuje směs skupin Nsg a asg. Contoso je starosti o správu skupiny zabezpečení sítě. To je také obavy o nadměrnému využití skupin zabezpečení sítě a složitostí pro provozní personál. Zde je, co udělá Contoso:

- Veškerý provoz do a ze všech podsítí (sever jih), budou platit pravidlo skupiny zabezpečení sítě, s výjimkou GatewaySubnets v sítích rozbočovače.
- Skupiny Nsg pro podsíť a síťovou kartu skupin zabezpečení sítě budou chráněné všechny brány firewall nebo řadič domény.
- Všechny produkční aplikace bude mít skupiny asg použít.


Contoso má integrované modelu, jak to bude vypadat pro své aplikace.

![Zabezpečení](./media/contoso-migration-infrastructure/asg.png)


Skupiny zabezpečení sítě přidružené k skupiny asg se nakonfigurují s nejnižšími oprávněními, aby, která povoluje jenom pakety do konečného tok z jedné části sítě.

**Akce** | **Název** | **Zdroj** | **Cíl** | **Port**
--- | --- | --- | --- | --- 
Povolit | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
Povolit | AllowWebToApp | APP1-FE | APP1-DB | 1433
Povolit | AllowAppToDB | APP1-APP | Všechny | Všechny
Odepřít | DenyAllInbound | Všechny | Všechny | Všechny

### <a name="encrypt-data"></a>Šifrování dat

Azure Disk Encryption se integruje se službou Azure Key Vault a pomáhá řídit a spravovat šifrování disku klíčů a tajných kódů v předplatném služby key vault. Zajišťuje, že všechna data na discích virtuálních počítačů jsou zašifrovaná rest ve službě Azure storage.  

- Contoso bylo zjištěno, že konkrétní virtuální počítače vyžadovat šifrování.
- Contoso se uplatní šifrování pro virtuální počítače s zákazníka, důvěrné, nebo data ICP.


## <a name="conclusion"></a>Závěr

V tomto článku se nastavení zásad pro předplatné Azure a služby infrastruktury Azure Contoso hybridní identifikovat, zotavení po havárii, sítí, zásady správného řízení a zabezpečení. 

Všechny kroky, které Contoso dokončit, tady jsou požadovány pro migraci do cloudu. V tomto případě ho chtěli plánování síťové infrastruktury, který lze použít pro všechny typy migrace a jsou zabezpečené, odolné a škálovatelné. 

Tato infrastruktura na místě je připravena k přesunutí a vyzkoušejte si migraci Contoso.

## <a name="next-steps"></a>Další postup

Jako první scénáři migrace, se bude Contoso [vyhodnocení dvouvrstvém aplikace v místním SmartHotel360 pro migraci do Azure](contoso-migration-assessment.md). 
