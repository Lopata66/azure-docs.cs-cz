---
title: Časté otázky – VMware na zotavení po havárii Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek shrnuje běžné otázky při nastavování zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: cdb8fe5deb71c014f7e0af01d070e5004d8c9994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418784"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Časté otázky – VMware pro replikaci Azure

Tento článek obsahuje odpovědi na běžné dotazy, které můžeme vidět, pokud nasazení zotavení po havárii místních virtuálních počítačů VMware do Azure. Pokud po přečtení tohoto článku máte dotazy, zveřejněte na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Obecné
### <a name="how-is-site-recovery-priced"></a>Jak se účtuje Site Recovery?
Kontrola [ceny za Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) podrobnosti.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zaplatím za virtuální počítače Azure?
Během replikace data se replikují do služby Azure storage a neplatíte žádné změny virtuálního počítače. Při spuštění převzetí služeb při selhání do Azure Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Následně se vám budeme fakturovat výpočetní prostředky, které využívají v Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Co je možné? s VMware pro replikaci Azure
- **Zotavení po havárii**: Můžete nastavit úplné havárii. V tomto scénáři replikaci místních virtuálních počítačů VMware do Azure storage. Potom Pokud není k dispozici v místní infrastruktuře, můžete převzít služby do Azure. Při převzetí služeb při selhání, se vytvoří virtuální počítače Azure pomocí replikovaná data. Aplikace a úlohy na virtuálních počítačích Azure, mají přístup až do vašeho místního datového centra je opět k dispozici. Pak můžete předat zpět z Azure do místní lokality.
- **Migrace**: Site Recovery můžete použít k migraci místních virtuálních počítačů VMware do Azure. V tomto scénáři replikaci místních virtuálních počítačů VMware do Azure storage. Pak převezme služby při selhání z místního Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Co musím udělat v Azure?
Budete potřebovat předplatné Azure, trezor služby Recovery Services, účet úložiště mezipaměti, spravované disky a virtuální síť. Trezor, účet úložiště mezipaměti spravovaných disků a sítí musí být ve stejné oblasti.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Oprávnění k vytvoření virtuálních počítačů musí Můj účet Azure?
Pokud si nejste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud si nejste, potřebujete oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a virtuální síť, kterou zadáte při konfiguraci Site Recovery a oprávnění k zápisu do vybraného účtu úložiště nebo spravovaných disků na základě vaší konfigurace. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="can-i-use-guest-os-server-license-on-azure"></a>Můžete použít licenční server hostovaný operační systém v Azure?
Ano, můžete použít Microsoft Software Assurance zákazníci [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) a Šetřete na náklady na licencování **počítače s Windows serverem** , které se migrují do Azure nebo použijte Azure pro zotavení po havárii.

## <a name="pricing"></a>Ceny

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Licenční poplatky za zpracování během replikace po převzetí služeb při selhání?

Nejčastější dotazy o licencování najdete [tady](https://aka.ms/asr_pricing_FAQ) Další informace.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak můžete vypočítat přibližné náklady během používání služby Site Recovery?

Můžete použít [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) odhadnout náklady při používání služby Azure Site Recovery. Podrobný odhad nákladů na spuštění nástroje plánovače nasazení (https://aka.ms/siterecovery_deployment_planner) a analyzovat [sestavy odhadu nákladů](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>Existuje rozdíl v náklady zajišťujete při replikaci přímo do spravovaného disku?

Spravované disky se účtují poněkud liší od účty úložiště. Podrobnosti najdete na níže uvedeném příkladu pro zdrojový disk o velikosti 100 GB. V příkladu je specifický pro rozdílové náklady na úložiště. Tyto náklady nezahrnuje náklady za snímky, mezipaměti úložiště a transakce.

* Účet úložiště úrovně Standard Vs. Standardní HDD spravovaný Disk

    - **Disk zřízeného úložiště služba Azure Site Recovery**: S10
    - **Účet úložiště úrovně standard se účtují po využité svazku**: 5 USD za měsíc
    - **Spravovaných disků úrovně Standard vám účtovat na svazku zřízené**: 5.89 $ za měsíc

* Účet Premium storage Vs. Premium SSD Managed Disk 
    - **Disk zřízeného úložiště služba Azure Site Recovery**: P10
    - **Za účet úložiště úrovně Premium na svazku zřízené**: 17.92 $ za měsíc
    - **Spravovaný disk úrovně Premium účtuje na svazku zřízené**: 17.92 $ za měsíc

Další informace najdete na [podrobné informace o cenách spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>Účtují se mi účtovat další poplatky pro účet úložiště mezipaměti se spravovanými disky?

Ne, není účtovat další poplatky za mezipaměti. Mezipaměť je vždy součástí VMware do Azure architektury. Při replikaci do účtu úložiště úrovně standard, toto úložiště mezipaměti je součástí stejný cílový účet úložiště.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Používám Azure Site Recovery už víc než měsíc. Získám i tak prvních 31 dní pro každou chráněnou instanci zadarmo?

Ano, nevadí, jak dlouho používáte Azure Site Recovery. U chráněných instancí se neúčtují žádné poplatky za Azure Site Recovery za prvních 31 dní. Pokud jste třeba posledních 6 měsíců chránili 10 instancí a připojíte k Azure Site Recovery 11. instanci, nebudete prvních 31 dní platit za 11. instanci žádné poplatky za Azure Site Recovery. Za prvních 10 instancí se dál budou účtovat poplatky za Azure Site Recovery, protože jsou chráněné déle než 31 dní.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Budu se mi během prvních 31 dní účtovat nějaké další poplatky Azure?

Ano, i když je Azure Site Recovery během prvních 31 dní chráněné instance zadarmo, můžou se vám účtovat poplatky za Azure Storage, transakce úložiště a přenosy dat. U obnoveného virtuálního počítače se můžou účtovat taky poplatky za výpočty Azure.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>S jakými poplatky mám při používání služby Azure Site Recovery počítat?

Odkazovat na naše [nejčastější dotazy k náklady](https://aka.ms/asr_pricing_FAQ) podrobné informace.

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>Platí se za přidružené k provedení postupů zotavení po Havárii a testovací převzetí služeb při selhání?

Neexistuje žádné zvláštní náklady na zotavení. Bude poplatky za výpočetní kapacitu až virtuální počítač se vytvoří příspěvek testovací převzetí služeb při selhání.

## <a name="azure-site-recovery-components-upgrade"></a>Upgradujte komponenty Azure Site Recovery

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>Agent Mobility/konfigurační Server/je velmi stará verze procesového serveru a tento upgrade se nezdařil. Jak mám upgradovat na nejnovější verzi?

Azure Site Recovery se řídí model podpory N-4. Odkazovat na naše [podporují příkaz](https://aka.ms/asr_support_statement) pochopit podrobnosti o tom, jak upgradovat z velmi staré verze.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Kde najdu kumulativní poznámky nebo aktualizovat verzi služby Azure Site Recovery?

Odkazovat [dokumentu](https://aka.ms/asr_update_rollups) pro informace o poznámkách k vydané verzi. Odkazy instalaci příslušných součástí najdete v každé kumulativní aktualizaci.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Jak upgradovat součásti Site Recovery pro VMware v místním prostředí nebo fyzického serveru do Azure?

Přečtěte si naše doprovodné materiály k dispozici [tady](https://aka.ms/asr_vmware_upgrades) upgradovat vaše komponenty.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>Restartování zdrojového počítače je povinné pro každý upgrade?

I když doporučuje, není to povinné pro každý upgrade. Přečtěte si [tady](https://aka.ms/asr_vmware_upgrades) pro jasné pokyny.

## <a name="on-premises"></a>Lokálně

### <a name="what-do-i-need-on-premises"></a>K čemu potřebuji místní?

Na místním budete potřebovat:
- Site Recovery součásti nainstalovat na jeden virtuální počítač VMware.
- Infrastrukturu VMware, u alespoň jednoho hostitele ESXi a My doporučujeme vCenter server.
- Jeden nebo více virtuálních počítačů VMware pro replikaci.

[Další informace](vmware-azure-architecture.md) o replikaci z VMware do Azure architektury.

Místní konfigurační server můžete nasadit takto:

- Doporučujeme, abyste že nasazení konfiguračního serveru jako virtuálního počítače VMware s konfiguračním serverem předinstalován pomocí šablony OVA.
- Pokud z nějakého důvodu nemůžete použít šablonu, můžete nastavit konfigurační server ručně. [Další informace](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Kde replikace místních virtuálních počítačů do?
Data se replikují do úložiště Azure. Při spuštění převzetí služeb při selhání Site Recovery automaticky vytvoří virtuální počítače Azure z účtu úložiště nebo spravovaný disk, na základě vaší konfigurace.

## <a name="replication"></a>Replikace

### <a name="what-applications-can-i-replicate"></a>Jaké aplikace můžou replikovat?
Můžete replikovat jakoukoli aplikaci nebo úlohu spuštěnou na virtuálním počítači VMware, který splňuje [požadavky na replikaci](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery poskytuje podporu pro replikaci se sledováním aplikací, tak, aby aplikace, můžete převzetí služeb při selhání a zpět do použitelného stavu se nezdařilo. Site Recovery se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory a úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>Můžete chránit virtuální počítač, který má konfiguraci disku Dockeru?

Ne, jedná se o nepodporovaný scénář.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Můžete replikovat do Azure pomocí VPN typu site-to-site?
Site Recovery replikuje data z místních do úložiště Azure přes veřejný koncový bod, nebo pomocí veřejného partnerského vztahu ExpressRoute. Replikace přes síť VPN typu site-to-site se nepodporuje.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Můžete replikovat do Azure pomocí ExpressRoute?
Ano, je možné replikovat virtuální počítače Azure ExpressRoute. Site Recovery replikuje data do úložiště Azure přes veřejný koncový bod. Budete muset nastavit [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#publicpeering) nebo [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) pro účely replikace Site Recovery ExpressRoute. Partnerský vztah Microsoftu je doporučené směrování domény pro replikaci. Ujistěte se, že [síťové požadavky](vmware-azure-configuration-server-requirements.md#network-requirements) splnění pro replikaci. Po převzetí služeb virtuálních počítačů do služby Azure virtual network, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Jak můžete změnit účet úložiště, jakmile je chráněný počítač?

Budete muset zakázat a povolit replikaci upgradovat nebo downgradovat typ účtu úložiště.

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>Můžete replikovat do účtů úložiště pro nový počítač?

Ne, od března "19, můžete replikovat do managed disks v Azure z portálu. Replikace do účtů úložiště pro nový počítač se pouze k dispozici prostřednictvím rozhraní REST API a Powershellu. Pomocí rozhraní API ve verzi 2016-08-10 nebo 2018-01-10 se replikuje do účtů úložiště.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Jaké jsou výhody v replikaci na spravované disky?

Přečtěte si článek věnovaný tomu, jak [Azure Site Recovery zjednodušuje zotavení po havárii se spravovanými disky](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Jak mohu změnit typ spravovaného disku po aktivaci ochrany počítače?

Ano, můžete snadno změnit typ spravovaného disku. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Ale po změníte typ spravovaného disku, ujistěte se čekat body obnovení čerstvé chcete vygenerovat, pokud je potřeba testovat převzetí služeb při selhání nebo převzetí služeb při selhání odeslat tuto aktivitu.

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>Můžete přepínat replikace z spravované disky do nespravované disky?

Ne, přepínání ze spravovaného do nespravovaného kódu není podporováno.

### <a name="why-cant-i-replicate-over-vpn"></a>Proč se můžu replikovat přes síť VPN?

Při replikaci do Azure, provoz replikace dosáhne veřejné koncové body služby Azure Storage, proto vám můžou replikovat jenom přes veřejný internet s ExpressRoute (veřejný partnerský vztah) a VPN nebude fungovat.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Můžete použít Riverbed SteelHeads pro replikaci?

Náš partner, Riverbed, poskytuje podrobné informace o práci s Azure Site Recovery. Najdete jejich [Průvodce řešením](https://community.riverbed.com/s/article/DOC-4627).

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaných virtuálních počítačů?

Pro účely replikace virtuálního počítače VMware musí běžet podporovaný operační systém. Kromě toho virtuální počítač musí splňovat požadavky pro virtuální počítače Azure. [Další informace](vmware-physical-azure-support-matrix.md##replicated-machines) v matici podpory.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?
Při replikaci virtuálních počítačů VMware do Azure je průběžné replikace.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Můžete zachovat IP adresu na převzetí služeb při selhání?
Ano, můžete zachovat IP adresu na převzetí služeb při selhání. Ujistěte se, že cílová IP adresa zmíníte v okně "výpočty a síť' před převzetí služeb při selhání. Také se ujistěte, vypnutí počítače v okamžiku převzetí služeb při selhání aby nedocházelo ke konfliktům IP během navrácení služeb po obnovení.

### <a name="can-i-extend-replication"></a>Je možné rozšířit replikaci?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci v [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžete provést offline počáteční replikaci?
Toto není podporováno. Žádost o tuto funkci [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Vyloučení disků
Ano, můžete vyloučit disky z replikace.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Můžete změnit cílovou velikost virtuálního počítače nebo typ virtuálního počítače před převzetí služeb při selhání?
Ano, můžete změnit typy a velikosti virtuálního počítače před převzetí služeb při selhání tak, že přejdete na výpočty a síť nastavení položky replikace na portálu.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky se dají replikovat. Disk s operačním systémem musí být základní disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Pokud budu používat skupiny replikace konzistenci, můžu přidat nový virtuální počítač do existující skupiny replikace?
Ano, můžete přidat nové virtuální počítače do existující skupiny replikace, když povolíte replikaci pro ně. Virtuální počítač nelze přidat do existující skupiny replikace po replikaci se zahájí, a nelze vytvořit replikační skupinu pro stávající virtuální počítače.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Můžete upravit virtuální počítače, které se replikují přidáním nebo změnou velikosti disků?

Pro replikaci VMware do Azure můžete upravit velikost disku. Pokud chcete přidat nové disky, které potřebujete přidat disk a znovu povolit ochranu virtuálního počítače.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>Můžu migrovat na místní počítače do nového serveru Vcenter bez dopadu na probíhající replikaci?
Ne, Změna serveru Vcenter nebo migrace ovlivní probíhající replikaci. Budete muset nastavení Azure Site Recovery do nového systému Vcenter a povolíte replikaci pro počítače.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Můžete replikovat do mezipaměti nebo cílový účet úložiště, který má virtuální sítě (pomocí bran firewall Azure storage) nakonfigurovaný na něm?
Ne, Azure Site Recovery nepodporuje replikaci do úložiště ve virtuální síti.

## <a name="configuration-server"></a>Konfigurační server

### <a name="what-does-the-configuration-server-do"></a>Co dělá konfiguračního serveru?
Konfigurační server běží v místním součásti Site Recovery, včetně:
- Konfigurační server koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odešle ho do úložiště Azure., že procesový server na virtuálních počítačích, které chcete replikovat a provádí automatické zjišťování virtuálních počítačů VMware v místním také nainstaluje službu Mobility.
- Hlavní cílový server zpracovává replikační data během navrácení služeb po obnovení z Azure.

[Další informace](vmware-azure-architecture.md) o součásti konfiguračního serveru a procesů.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde můžu nastavit konfigurační server?
Potřebujete jeden vysoce dostupný místní VMware virtuálního počítače pro konfigurační server.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Jaké jsou požadavky pro konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Můžete ručně nastavit konfigurační server, namísto použití šablony?
Doporučujeme používat nejnovější verzi šablony OVF pro [vytvoření virtuálního počítače konfiguračního serveru](vmware-azure-deploy-configuration-server.md). Pokud z nějakého důvodu nemůžete, například nemáte přístup k serveru VMware, můžete si [stáhnout soubor sjednocené instalace](physical-azure-set-up-source.md) z portálu, a spusťte na virtuálním počítači.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurační server možná replikace do více než jedné oblasti?
Ne. K tomu potřeba nastavit konfigurační server v jednotlivých oblastech.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Můžete hostovat konfigurační server v Azure?
Při nejbližším virtuálnímu počítači Azure s konfiguračního serveru potřebuje komunikovat s virtuálními počítači a na místní infrastrukturu VMware. Může přidat latenci a mít vliv na probíhající replikaci.

### <a name="how-do-i-update-the-configuration-server"></a>Jak můžu aktualizovat konfigurační server?
[Další informace o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) aktualizuje konfigurační server. Můžete najít informace o nejnovější aktualizaci v [stránku aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery). Můžete také přímo stáhnout nejnovější verzi konfiguračního serveru z [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Pokud je vaše verze starší než verze 4 z aktuální verze, podívejte se na naše [podporují příkaz](https://aka.ms/asr_support_statement) pokyny k upgradu.

### <a name="should-i-backup-the-deployed-configuration-server"></a>Můžu zálohovat nasazené konfiguračního serveru?
Doporučujeme pravidelných naplánovaných záloh konfiguračního serveru. Úspěšné navrácení služeb po obnovení se při navrácení služeb obnoví virtuální počítač musí existovat v databázi konfigurací serveru a konfiguračního serveru musí být spuštěn a v připojeném stavu. Další informace o běžných úloh správy serveru konfigurace [tady](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Když mám jsem nastavíte konfigurační server, můžete I stáhnout a nainstalovat MySQL ručně?

Ano. Stáhnout MySQL a jeho umístění **C:\Temp\ASRSetup** složky. Nainstalujte je ručně. Když nastavíte konfigurační server virtuálního počítače a přijměte podmínky, MySQL bude uvedeno **už nainstalovaná** v **stáhnout a nainstalovat**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Můžete vyhnout stahování MySQL ale nechat nainstalovat Site Recovery?

Ano. Stáhněte instalační program MySQL a umístěte ho **C:\Temp\ASRSetup** složky.  Když nastavíte konfigurační server VM, přijměte podmínky a klikněte na **stáhnout a nainstalovat**, portál použije instalační program přidá k instalaci MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Můžete použít konfigurační server virtuálního počítače na něco jiného?
Ne, byste měli používat jenom virtuální počítač pro konfigurační server. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Můžete naklonovat konfiguračního serveru a použít ho k orchestraci?
Ne, by měl nastavit novou konfiguračního serveru, abyste předešli problémům s registrací.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Můžete změnit trezoru zaregistrovaný na konfiguračním serveru?
Ne. Konfigurační server zaregistruje trezoru, nejde změnit. Kontrola [v tomto článku](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) postup opětovné registraci.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Můžete použít stejný konfigurační server pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů
Ano, ale nezapomeňte tento fyzický počítač může být pouze se zpět do virtuálního počítače VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Kde lze stáhnout přístupové heslo pro konfigurační server?
[Přečtěte si tento článek](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) Další informace o stahování přístupové heslo.

### <a name="where-can-i-download-vault-registration-keys"></a>Kde lze stáhnout registrační klíče trezoru?

V **trezor služby Recovery Services**, **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**. V **servery**vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.



## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde najdu instalace služby Mobility?
Instalační programy jsou uloženy v **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** složky na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Jak nainstalovat službu Mobility?
Nainstalujete na každý virtuální počítač, který chcete replikovat, použití [nabízená instalace](vmware-physical-mobility-service-overview.md#push-installation), nebo [ruční instalace](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z uživatelského rozhraní nebo Powershellu. Alternativně můžete nasadit pomocí nástroje nasazení, jako [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Jaký přístup Site Recovery potřebuje servery VMware?
Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavení virtuálního počítače VMware s konfiguračním serverem a další součásti Site Recovery v místním. [Další informace](vmware-azure-deploy-configuration-server.md)
- Automatické zjišťování virtuálních počítačů pro replikaci. Další informace o přípravě účtu pro automatické zjišťování. [Další informace](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Jaký přístup Site Recovery potřebuje virtuální počítače VMware?

- Pokud chcete replikovat, musí mít Virtuálním počítači VMware službu Site Recovery Mobility nainstalovaná a spuštěná. Můžete nasadit nástroj ručně, nebo zadat Site Recovery proveďte nabízenou instalaci služby při povolení replikace pro virtuální počítač. Site Recovery pro nabízenou instalaci, potřebuje účet, který můžete použít k instalaci součásti service. [Další informace](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Procesový server (spuštěné ve výchozím nastavení na konfiguračním serveru) provádí tuto instalaci. [Další informace](vmware-azure-install-mobility-service.md) o instalace služby Mobility.
- Během replikace virtuálních počítačů komunikovat s využitím Site Recovery následujícím způsobem:
    - Virtuální počítače komunikovat s konfiguračním serverem na port HTTPS 443 pro správu replikace.
    - Virtuální počítače odesílají data do procesového serveru na příchozím portu HTTPS 9443 (je možné upravit).
    - Pokud povolíte konzistenci pro více virtuálních počítačů, virtuálních počítačů vzájemně komunikovat přes port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Replikační data odeslaná do Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích. Replikovaná data se přemisťují mezi hypervisory VMware a Azure storage. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Jsme zachovat místní metadata v rámci zeměpisné oblasti?
Ano. Když vytvoříte trezor služby v oblasti, zajišťujeme, že všechna metadata používat Site Recovery zůstane v rámci dané oblasti geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, jak šifrování během přenosu a [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.


## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Můžu použít procesový server na místní navrácení služeb po obnovení?
Důrazně doporučujeme vytvořit procesový server v Azure, aby latence přenosu dat pro účely navrácení služeb po obnovení. Kromě toho v případě, že oddělené zdrojové síti virtuálních počítačů s Azure máte sítí na konfiguračním serveru, pak je nutné použít procesový Server vytvořený v Azure pro navrácení služeb po obnovení.

### <a name="how-far-back-can-i-recover"></a>Jak daleko mohou obnovit?
Nejstarší bod obnovení, které můžete použít pro replikaci z VMware do Azure je 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak získám přístup virtuálních počítačů Azure po převzetí služeb při selhání?
Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure přes zabezpečené internetové připojení, přes síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit řada věcí, abyste se mohli připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Se převzaly při selhání data odolná?
Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouvy SLA pro Azure. Pokud dojde k převzetí služeb při selhání, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) neprobíhá automaticky. Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [Powershellu](/powershell/module/azurerm.siterecovery) k aktivaci převzetí služeb při selhání.

### <a name="can-i-fail-back-to-a-different-location"></a>Můžu navrátit služby zpět do jiného umístění?
Ano, pokud převzetí služeb při selhání do Azure, můžete navrátit služby zpět do jiného umístění Pokud původní není k dispozici. [Další informace](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Proč potřebuji síť VPN nebo ExpressRoute k navrácení služeb po obnovení?
Když převezmete služby zpět z Azure, se kopírují data z Azure zpět na vaše místní virtuální počítač a soukromý přístup je povinný.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Můžu změnit velikost virtuálního počítače Azure po převzetí služeb při selhání?
Ne, nelze změnit velikost nebo typ cílový virtuální počítač po převzetí služeb.


## <a name="automation-and-scripting"></a>Automatizací a skriptováním

### <a name="can-i-set-up-replication-with-scripting"></a>Můžete nastavit replikaci pomocí skriptování?
Ano. Můžete automatizovat postupy workflow Site Recovery pomocí rozhraní Rest API, Powershellu nebo sady Azure SDK. [Další](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Výkon a kapacita
### <a name="can-i-throttle-replication-bandwidth"></a>Je možné omezovat šířku pásma replikace?
Ano. [Další informace](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Další postup
* [Kontrola](vmware-physical-azure-support-matrix.md) požadavky na podporu.
* [Nastavit](vmware-azure-tutorial.md) replikaci z VMware do Azure replikace.
