---
title: Časté otázky – VMware na zotavení po havárii Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek shrnuje běžné otázky fir zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/26/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 22d3bdf8c60e6682c360395b44fe6f1dcc1207b0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925519"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Časté otázky – VMware pro replikaci Azure

Tento článek obsahuje odpovědi na běžné dotazy, když nasazujete zotavení po havárii místních virtuálních počítačů VMware do Azure. 

## <a name="general"></a>Obecné 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co musím udělat pro zotavení po havárii virtuálního počítače VMware?

[Další informace o](vmware-azure-architecture.md) součásti účastnící se zotavení po havárii virtuálních počítačů VMware. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Můžete pomocí Site Recovery můžete migrovat virtuální počítače VMware do Azure?

Ano, kromě nastavení úplné zotavení po havárii pro virtuální počítače VMware pomocí Site Recovery, můžete také použijete Site Recovery k migraci místních virtuálních počítačů VMware do Azure. V tomto scénáři replikaci místních virtuálních počítačů VMware do Azure storage. Pak převezme služby při selhání z místního Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure. Proces je podobný nastavení úplné havárii, s tím rozdílem, že při migraci můžete nemůžete navracet služby po obnovení z Azure.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Oprávnění k vytvoření virtuálních počítačů musí Můj účet Azure?
Pokud si nejste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud si nejste, potřebujete oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a virtuální síť, kterou zadáte při konfiguraci Site Recovery a oprávnění k zápisu do vybraného účtu úložiště nebo spravovaných disků na základě vaší konfigurace. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>Jaké aplikace můžou replikovat?
Můžete replikovat jakoukoli aplikaci nebo úlohu spuštěnou na virtuálním počítači VMware, který splňuje [požadavky na replikaci](vmware-physical-azure-support-matrix.md##replicated-machines).
- Site Recovery poskytuje podporu pro replikaci se sledováním aplikací, tak, aby aplikace, můžete převzetí služeb při selhání a zpět do použitelného stavu se nezdařilo.
- Site Recovery se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory a úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat.
- [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Můžete použít licenci hostovaného operačního systému serveru v Azure?
Ano, můžete použít Microsoft Software Assurance zákazníci [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) a Šetřete na náklady na licencování **počítače s Windows serverem** , které se migrují do Azure nebo použijte Azure pro zotavení po havárii.

## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Jaký přístup Site Recovery potřebuje servery VMware?
Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavení virtuálního počítače VMware s konfiguračního serveru Site Recovery
- Automatické zjišťování virtuálních počítačů pro replikaci. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Jaký přístup Site Recovery potřebuje virtuální počítače VMware?

- Pokud chcete replikovat, musí mít Virtuálním počítači VMware službu Site Recovery Mobility nainstalovaná a spuštěná. Můžete nasadit nástroj ručně, nebo zadat Site Recovery proveďte nabízenou instalaci služby při povolení replikace pro virtuální počítač. 
- Během replikace virtuálních počítačů komunikovat s využitím Site Recovery následujícím způsobem:
    - Virtuální počítače komunikovat s konfiguračním serverem na port HTTPS 443 pro správu replikace.
    - Virtuální počítače odesílají data do procesového serveru na příchozím portu HTTPS 9443 (je možné upravit).
    - Pokud povolíte konzistenci pro více virtuálních počítačů, virtuálních počítačů vzájemně komunikovat přes port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Replikační data odeslaná do Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích. Replikovaná data se přemisťují mezi hypervisory VMware a Azure storage. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.


## <a name="pricing"></a>Ceny
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Výpočet přibližné náklady pro zotavení po havárii VMware

Můžete použít [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) odhadnout náklady při používání Site Recovery.

Podrobný odhad nákladů na, spusťte nástroj Plánovač nasazení pro [VMware](https://aka.ms/siterecovery_deployment_planner)a použít [sestavy odhadu nákladů](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existuje rozdíl nákladů mezi replikace do úložiště nebo přímo na spravované disky?

Spravované disky se účtují poněkud liší od účty úložiště. [Další informace](https://azure.microsoft.com/pricing/details/managed-disks/) informace o cenách spravovaných disků.

## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde najdu instalace služby Mobility?
Instalační programy jsou v **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** složky na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Jak nainstalovat službu Mobility?
Nainstalujete na každý virtuální počítač, který chcete replikovat, pomocí několika metod:
- [Nabízená instalace](vmware-physical-mobility-service-overview.md#push-installation)
- [Ruční instalace](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z uživatelského rozhraní nebo Powershellu.
- Nasazení pomocí nástroje nasazení, jako [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Spravované disky

### <a name="where-does-site-recovery-replicate-data-to"></a>Kde Site Recovery replikovat data?

Site Recovery replikuje místní virtuální počítače VMware a fyzických serverů do managed disks v Azure.
- Procesový server Site Recovery zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti.
- Tyto protokoly se používají k vytvoření bodů obnovení v Azure spravované disky, které mají předponu asrseeddisk.
- Pokud dojde k převzetí služeb při selhání, vyberete bod obnovení slouží k vytvoření nového spravovaného disku cíl. Tento spravovaný disk je připojený k virtuálnímu počítači v Azure.
- Virtuální počítače, které byly dřív replikoval do účtu úložiště (před březnem 2019) nejsou ovlivněny.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Můžete replikovat nové počítače do účtů úložiště?

Ne, od března 2019 na portálu, můžete replikovat do Azure jenom spravované disky. 

Replikace nových virtuálních počítačů na účet úložiště je dostupný pouze pomocí prostředí PowerShell nebo rozhraní REST API (verze 2018-01-10 nebo 2016-08-10).

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Jaké jsou výhody v replikaci na spravované disky?

[Zjistěte, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery zjednodušuje zotavení po havárii se spravovanými disky.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Můžete změnit typ spravovaného disku, jakmile je chráněný počítač?

Ano, můžete snadno [změnit typ spravovaného disku](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pro probíhající replikace. Před změnou typu, ujistěte se, že žádná adresa URL SAS vygenerovaný na spravovaný disk. Přejít k prostředku spravovaného disku na portálu Azure portal a kontrolovat, jestli mají adresy URL SAS banner v okně přehledu. Pokud je k dispozici, klikněte na něj se zrušit probíhající export. Až to bude hotové, změňte typ disku během několika minut. Pokud změníte typ spravovaného disku, počkejte však body čerstvé obnovení Azure Site Recovery vygenerovat. Pomocí nové body obnovení pro všechny testovací převzetí služeb při selhání nebo převzetí služeb při selhání do budoucna.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Můžete přepínat replikace z spravované disky do nespravované disky?

Ne, přepínání ze spravovaného do nespravovaného kódu není podporováno.

## <a name="replication"></a>Replikace


### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaných virtuálních počítačů?

[Další informace](vmware-physical-azure-support-matrix.md##replicated-machines) o virtuální počítač VMware nebo fyzický server – požadavky a podporu.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?
Při replikaci virtuálních počítačů VMware do Azure je průběžné replikace.


### <a name="can-i-extend-replication"></a>Je možné rozšířit replikaci?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci v [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžete provést offline počáteční replikaci?
Toto není podporováno. Žádost o tuto funkci [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="what-is-asrseeddisk"></a>Co je asrseeddisk?
Pro každý zdrojový disk data se replikují na spravovaný disk v Azure. Tento disk má předponu asrseeddisk. Uloží kopii zdrojový disk a všechny snímky bodu obnovení.

### <a name="can-i-exclude-disks-from-replication"></a>Můžete vyloučit disky z replikace?
Ano, můžete vyloučit disky.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky se dají replikovat. Disk s operačním systémem musí být základní disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Pokud budu používat skupiny replikace konzistenci, můžu přidat nový virtuální počítač do existující skupiny replikace?
Ano, můžete přidat nové virtuální počítače do existující skupiny replikace, když povolíte replikaci pro ně.
- Virtuální počítač nelze přidat do existující skupiny replikace po replikaci se zahájí.
- Nejde vytvořit replikační skupinu pro stávající virtuální počítače.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Můžete upravit virtuální počítače, které se replikují přidáním nebo změnou velikosti disků?

Pro replikaci VMware do Azure můžete upravit velikost disku. Pokud chcete přidat nové disky, které potřebujete přidat disk a znovu povolit ochranu virtuálního počítače.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Můžu migrovat místní počítače do nového systému vCenter Server bez dopadu na probíhající replikaci?
Ne, Změna serveru Vcenter nebo migrace ovlivní probíhající replikaci. Budete muset nastavit Site Recovery do nového systému vCenter Server a znovu ji povolte pro počítače.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Můžete replikovat do mezipaměti nebo cílový účet úložiště, který má virtuální sítě (pomocí bran firewall Azure storage) nakonfigurovaný na něm?
Ne, Site Recovery nepodporuje replikaci do úložiště ve virtuální síti.





## <a name="component-upgrade"></a>Upgrade součásti

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moje verze serveru agenta nebo konfigurace služby Mobility je starý a tento upgrade se nezdařil. Co mám udělat?  

Site Recovery se řídí model podpory N-4. [Další informace](https://aka.ms/asr_support_statement) o tom, jak upgradovat z velmi staré verze.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Kde najdu kumulativní poznámky nebo aktualizovat verzi služby Azure Site Recovery?

[Přečtěte si](site-recovery-whats-new.md) o nové aktualizace, a [získat informace o kumulativní](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Kde najdu informace o upgradu pro zotavení po havárii do Azure?

[Další informace o](https://aka.ms/asr_vmware_upgrades) upgrade.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Je potřeba restartovat zdrojové počítače pro každý upgrade?

I když doporučuje, není to povinné pro každý upgrade. [Další informace](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>Konfigurační server

### <a name="what-does-the-configuration-server-do"></a>Co dělá konfiguračního serveru?

Konfigurační server běží v místním součásti Site Recovery, včetně:
- Konfigurační server samotný, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure. Procesový server také provádí nabízenou instalaci služby Mobility na virtuální počítače a provádí automatické zjišťování místních virtuálních počítačů VMware.
- Hlavní cílový server zpracovává replikační data během navrácení služeb po obnovení z Azure.

[Další informace](vmware-azure-architecture.md) o součásti konfiguračního serveru a procesů.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde můžu nastavit konfigurační server?
Potřebujete jeden vysoce dostupný místní VMware virtuálního počítače pro konfigurační server. Pro zotavení po havárii fyzického serveru můžete nainstalovat konfigurační server na fyzickém počítači.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co musím udělat pro konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Můžete ručně nastavit konfigurační server, namísto použití šablony?
Doporučujeme vám [vytvoření virtuálního počítače konfiguračního serveru](vmware-azure-deploy-configuration-server.md) s nejnovější verzí šablony OVF. Pokud z nějakého důvodu nemůžete, například nemáte přístup k serveru VMware, můžete si [Stáhnout](physical-azure-set-up-source.md) instalační soubor z portálu a nastavte konfigurační server.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurační server možná replikace do více než jedné oblasti?
Ne. Chcete-li to provést, musíte konfigurační server v jednotlivých oblastech.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Můžete hostovat konfigurační server v Azure?
Při nejbližším virtuálnímu počítači Azure s konfiguračního serveru potřebuje komunikovat s virtuálními počítači a na místní infrastrukturu VMware. To zvyšuje latenci a má vliv na probíhající replikaci.

### <a name="how-do-i-update-the-configuration-server"></a>Jak můžu aktualizovat konfigurační server?

[Přečtěte si](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) jak aktualizovat konfigurační server.
- Informace o nejnovější aktualizaci najdete na [stránku aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Nejnovější verzi si můžete stáhnout z portálu. Alternativně můžete přímo stáhnout nejnovější verzi z konfiguračního serveru [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Pokud je vaše verze víc než čtyři verze starší než aktuální verze, podívejte se na naše [podporují příkaz](https://aka.ms/asr_support_statement) pokyny k upgradu.

### <a name="should-i-back-up-the-configuration-server"></a>Byste zálohovat konfigurační server?
Doporučujeme pravidelných naplánovaných záloh konfiguračního serveru.
- Pro úspěšné navrácení služeb po obnovení musí existovat virtuální počítač se při navrácení služeb obnoví databázi konfiguračního serveru.
- Konfigurační server musí být spuštěn a v připojeném stavu.
- [Další informace](vmware-azure-manage-configuration-server.md) o běžných úloh správy konfigurace serveru.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Když mám jsem nastavíte konfigurační server, můžete I stáhnout a nainstalovat MySQL ručně?

Ano. Stáhnout MySQL a jeho umístění **C:\Temp\ASRSetup** složky. Nainstalujte je ručně. Když nastavíte konfigurační server virtuálního počítače a přijměte podmínky, MySQL bude uvedeno **už nainstalovaná** v **stáhnout a nainstalovat**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Můžete vyhnout stahování MySQL ale nechat nainstalovat Site Recovery?

Ano. Stáhněte instalační program MySQL a umístěte ho **C:\Temp\ASRSetup** složky.  Když nastavíte konfigurační server VM, přijměte podmínky a klikněte na **stáhnout a nainstalovat**. Portál použije instalační program přidá k instalaci MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Můžete použít konfigurační server virtuálního počítače na něco jiného?
Ne, byste měli používat jenom virtuální počítač pro konfigurační server. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Můžete naklonovat konfiguračního serveru a použít ho k orchestraci?
Ne, byste měli nastavit novou konfiguraci serveru, aby potíže s registrací.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Můžu změnit trezor, ve které je zaregistrovaný konfigurační server?
Ne. Trezor je spojen s konfiguračním serverem, nelze změnit. Kontrola [v tomto článku](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) Další informace o opětovné registraci.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Můžete použít stejný konfigurační server pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů
Ano, ale mějte na paměti, že tento fyzický počítač lze pouze se zpět do virtuálního počítače VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Kde lze stáhnout přístupové heslo pro konfigurační server?
[Zjistěte, jak](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) stáhnout přístupové heslo.

### <a name="where-can-i-download-vault-registration-keys"></a>Kde lze stáhnout registrační klíče trezoru?

V trezoru služby Recovery Services klikněte na tlačítko **konfigurační servery** v **infrastruktura Site Recovery** > **spravovat**. Potom v **servery**vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.

## <a name="process-server"></a>Procesový server

### <a name="unable-to-select-process-server-during-enable-replication"></a>Nelze vybrat procesový server při povolení replikace

Z 9.24 verzi se provedly poskytují vylepšení [zpracovávat výstrahy serveru](vmware-physical-azure-monitor-process-server.md#process-server-alerts) na tom, kdy k nastavení horizontální navýšení kapacity procesového serveru. Toto je chcete zabránit omezování procesu serveru a vyhněte se použití není v pořádku procesový server.

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>Co mám dělat získat přesné stav procesového serveru?

Upgrade součásti Site Recovery na [nejnovější verze](service-updates-how-to.md#links-to-currently-supported-update-rollups) (nejméně 9.24 nebo vyšší).

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Můžu použít procesový server na místní navrácení služeb po obnovení?
Důrazně doporučujeme vytvořit procesový server v Azure pro účely navrácení služeb po obnovení, aby se zabránilo latenci přenosu dat. Kromě toho v případě, že oddělené zdrojové síti virtuálních počítačů s Azure máte sítí na konfiguračním serveru, je nutné použít procesový server vytvořený v Azure pro navrácení služeb po obnovení.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Můžete zachovat IP adresu na převzetí služeb při selhání?
Ano, můžete zachovat IP adresu na převzetí služeb při selhání. Ujistěte se, že zadáte cílovou IP adresu v "Výpočty a síť" nastavení pro virtuální počítač před převzetí služeb při selhání. Také vypněte počítače v okamžiku převzetí služeb při selhání aby nedocházelo ke konfliktům IP adresa během navrácení služeb po obnovení.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Můžete změnit cílovou velikost virtuálního počítače nebo typ virtuálního počítače před převzetí služeb při selhání?
Ano, můžete změnit typy a velikosti virtuálních počítačů kdykoli před převzetí služeb při selhání na nastavení výpočty a síť replikovaný virtuální počítač, na portálu.

### <a name="how-far-back-can-i-recover"></a>Jak daleko mohou obnovit?
Nejstarší bod obnovení, které můžete použít pro replikaci z VMware do Azure je 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak získám přístup virtuálních počítačů Azure po převzetí služeb při selhání?
Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure přes zabezpečené internetové připojení, přes síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit řada věcí, abyste se mohli připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Se převzaly při selhání data odolná?

Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouvy SLA pro Azure. Pokud dojde k převzetí služeb při selhání, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) neprobíhá automaticky. Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [Powershellu](/powershell/module/az.recoveryservices) k aktivaci převzetí služeb při selhání.

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
