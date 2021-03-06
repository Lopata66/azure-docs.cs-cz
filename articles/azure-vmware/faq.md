---
title: Nejčastější dotazy
description: Obsahuje odpovědi na některé běžné dotazy týkající se řešení Azure VMware.
ms.topic: conceptual
ms.date: 1/27/2021
ms.openlocfilehash: 3ef3d1b9e6fc76b5f09acf8c300dbea901b4aef2
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072264"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Nejčastější dotazy týkající se řešení Azure VMware

V tomto článku odpovíme na nejčastější dotazy týkající se řešení Azure VMware.

## <a name="general"></a>Obecné

### <a name="what-is-azure-vmware-solution"></a>Co je Azure VMware Solution?

Vzhledem k tomu, že podniky sledují strategie pro moderní účely, aby vylepšili flexibilitu, snížili náklady a urychlili inovace, hybridní cloudové platformy se vypracovaly jako klíčové aktivátory digitální transformace zákazníků. Řešení Azure VMware spojuje software Software-Defined data Center (SDDC) VMware s ekosystémem globální cloudové služby Azure od Microsoftu. Řešení Azure VMware je spravované tak, aby splňovalo požadavky na výkon, dostupnost, zabezpečení a dodržování předpisů.

## <a name="azure-vmware-solution-service"></a>Služba řešení Azure VMware

### <a name="where-is-azure-vmware-solution-available-today"></a>Kde je řešení Azure VMware k dispozici ještě dnes?

Služba se neustále přidává do nových oblastí, proto si prohlédněte [nejnovější informace o dostupnosti služby](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) , kde najdete další podrobnosti. 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Můžou úlohy spuštěné v instanci řešení Azure VMware spotřebovat nebo integrovat se službami Azure?

Všechny služby Azure budou dostupné pro zákazníky řešení Azure VMware. Omezení výkonu a dostupnosti pro konkrétní služby se musí řešit na základě případu.

### <a name="what-guest-operating-systems-are-compatible-with-azure-vmware-solution"></a>Jaké hostované operační systémy jsou kompatibilní s řešením Azure VMware?

Informace o kompatibilitě hostovaného operačního systému s vSphere najdete pomocí [Průvodce kompatibilitou VMware](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=software&details=1&releases=485&page=1&display_interval=10&sortColumn=Partner&sortOrder=Asc&testConfig=16).  Pokud chcete identifikovat verzi vSphere spuštěnou v řešení Azure VMware, přečtěte si téma [verze softwaru VMware](concepts-private-clouds-clusters.md#vmware-software-versions).

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Používají se stejné nástroje, které teď používám ke správě prostředků privátního cloudu?

Ano. Azure Portal se používá pro nasazení a několik operací správy. vCenter a NSX Manager se používají ke správě prostředků vSphere a NSX-T.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Můžu pomocí místního serveru vCenter spravovat privátní cloud?

V případě spuštění řešení Azure VMware nebude podporovat jednotné prostředí pro správu v místních i privátních cloudových prostředích. Clustery privátního cloudu se budou spravovat pomocí vCenter a NSX Manageru v privátním cloudu.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Můžu používat vRealize Suite spuštěnou místně? 

Konkrétní integrace a případy použití mohou být vyhodnoceny případ od případu.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Můžu migrovat virtuální počítače s vSphere z místního prostředí do privátních cloudů řešení Azure VMware?

Ano. Migrace virtuálních počítačů a vMotion se dají použít k přesunu virtuálních počítačů do privátního cloudu, pokud jsou splněné standardní [požadavky na vMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) pro architekturu vCenter.

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Vyžaduje se v místních prostředích konkrétní verze vSphere?

Všechna cloudová prostředí se dodávají s VMware HCX, vSphere 5,5 nebo novějším v místních prostředích pro vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Jak proces řízení změn vypadá jako?

Aktualizace provedené v samotné službě se řídí standardním procesem změny správy Microsoft Azure. Zákazníci zodpovídají za všechny úlohy správy úloh a přidružené procesy správy změn.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Jak se to liší od řešení Azure VMware podle CloudSimple?

Díky novému řešení Azure VMware mají Microsoft a VMware přímé partnerství s poskytovatelem cloudu. Nové řešení je výhradně navržené, sestavené a podporované společností Microsoft a schválené VMware. Tato řešení jsou v souladu s architekturou technologie VMware, která běží na vyhrazené infrastruktuře Azure.

### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Můžou se virtuální počítače řešení Azure VMware spravovat pomocí VMRC?
Ano. Za předpokladu, že systém, na kterém je nainstalovaný, má přístup k privátnímu cloudu vCenter a používá veřejnou službu DNS k překladu názvů ESXi.

### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Existují zvláštní pokyny k instalaci a používání VMRC s virtuálními počítači řešení Azure VMware?
No. Pro splnění požadavků na virtuální počítač postupujte podle [pokynů od VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html). 

### <a name="is-vmware-hcx-supported-on-vpns"></a>Podporuje se VMware HCX na sítích VPN?
Ne, z důvodu požadavků na šířku pásma a latenci.

### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Dá se Azure bastionu použít pro připojení k virtuálním počítačům řešení VMware Azure?
Služba Azure bastionu se doporučuje připojit k poli s odkazem, abyste zabránili odhalení řešení Azure VMware na Internet. Azure bastionu nejde použít pro připojení k virtuálním počítačům řešení VMware Azure, protože to nejsou objekty Azure IaaS.

### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Dá se Azure Load Balancer interní použít pro virtuální počítače řešení Azure VMware?
No. Azure Load Balancer interní – podporuje jenom virtuální počítače Azure s IaaS. Azure Load Balancer nepodporuje back-end fondy založené na IP adrese; jenom virtuální počítače Azure nebo objekty sady škálování virtuálních počítačů, ve kterých virtuální počítače řešení Azure VMware nejsou objekty Azure.

### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Může se pro připojení k řešení Azure VMware použít existující brána ExpressRoute?
Ano. Použijte existující bránu ExpressRoute pro připojení k řešení VMware Azure, pokud nepřekračuje limit čtyř ExpressRoute okruhů na jednu virtuální síť. Pokud chcete získat přístup k řešení Azure VMware z místního prostředí prostřednictvím ExpressRoute, musíte mít ExpressRoute Global Reach, protože brána ExpressRoute neposkytuje přechodné směrování mezi připojenými okruhy.

### <a name="why-does-azure-vmware-solution-use-a-public-4-byte-autonomous-system-number-asn"></a>Proč řešení Azure VMware používá veřejné číslo 4 bajty autonomního systému (ASN)?
Řešení Azure VMware používá úředně registrované veřejné 4 bajty čísla ASN, aby se zajistilo, že nedojde ke konfliktu s vaším místním používáním privátních čísla ASN v cestě směrování zákazníka do řešení Azure VMware.
 
### <a name="how-can-i-use-expressroute-to-connect-to-azure-vmware-solution-if-the-on-premises-expressroute-carrier-partnersisps-dont-support-4-byte-asn"></a>Jak se dá použít ExpressRoute k připojení k řešení VMware Azure, pokud místní partneři ExpressRoute/poskytovatelé služeb (ISP) nepodporují 4 bajty ASN?
Jediným způsobem, jak se připojit k řešení Azure VMware prostřednictvím ExpressRoute, je pro vaše prostředí a místní partneři ExpressRoute/poskytovatelé, kteří podporují 4 bajty ASN, nebo mají zpětnou kompatibilitu ze 4 bajtů až po 2 bajtové číslo ASN v předponě protokolu BGP ASN.

## <a name="compute-network-storage-and-backup"></a>Výpočetní výkon, síť, úložiště a záloha

### <a name="is-there-more-than-one-type-of-host-available"></a>Je k dispozici více než jeden typ hostitele?

K dispozici je pouze jeden typ hostitele.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Jaké jsou specifikace procesoru v jednotlivých typech hostitelů?

Servery mají duální procesory Intel Core 2,3 GHz.

### <a name="how-much-memory-is-in-each-host"></a>Kolik paměti je v každém hostiteli?

Servery mají 576 GB paměti RAM.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Jaká je kapacita úložiště každého hostitele?

Každý hostitel ESXi má dvě síti vSAN diskgroups s úrovní kapacity 15,2 TB a úrovní mezipaměti NVMe 3,2-TB (1,6 TB v každé ze všech diskových složek).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Kolik šířky pásma sítě je k dispozici na každém hostiteli ESXi?

Každý hostitel ESXi v řešení Azure VMware je nakonfigurovaný pomocí síťových adaptérů 4 25 – 2 GB/s, které jsou zřízené pro přenos systému ESXi, a pro přenosy úloh se zřídí dvě síťové karty. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Jsou data uložená v úložišti datasíti vsans zašifrovaná v klidovém stavu?

Ano, všechna síti vSAN data se ve výchozím nastavení šifrují pomocí klíčů uložených v Azure Key Vault.

###  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Jak fungují nezávislí výrobci softwaru (ISV) řešení pro zálohování pomocí Azure VMware?

CommVault, Veritas a Veeam rozšířila svá záložní řešení pro práci s řešeními Azure VMware.  Jakékoli řešení zálohování, které používá VMware VADP s režimem přenosu HotAdd, by ale fungovalo přímo ze seznamu řešení Azure VMware.

### <a name="what-about-support-for-isv-backup-solutions"></a>Co je podpora řešení pro zálohování ISV?

Jak jsou tato řešení pro zálohování nainstalovaná a spravovaná zákazníky, se můžou obrátit na příslušného nezávislého výrobce softwaru pro podporu. 

### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Jaké jsou správné zásady úložiště pro instalaci odstranění duplicit?

Použijte zásady úložiště *thin_provision* pro šablonu virtuálního počítače.  Výchozí hodnota je *thick_provision*.

### <a name="are-the-snmp-infrastructure-logs-shared"></a>Jsou protokoly infrastruktury SNMP sdílené?

No.

## <a name="hosts-clusters-and-private-clouds"></a>Hostitelé, clustery a privátní cloudy

### <a name="is-the-underlying-infrastructure-shared"></a>Sdílí se podkladová infrastruktura?

Ne, hostitelé a clustery privátního cloudu jsou vyhrazené a bezpečně se vymažou před a po použití.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Jaký je minimální a maximální počet hostitelů na cluster?

Clustery se můžou škálovat mezi 3 a 16 hostiteli ESXi. Clustery zkušební verze jsou omezené na tři hostitele.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Můžu škálovat svoje clustery privátního cloudu?

Ano, clustery se škálují mezi minimálním a maximálním počtem hostitelů ESXi. Clustery zkušební verze jsou omezené na tři hostitele.

### <a name="what-are-trial-clusters"></a>Co jsou zkušební clustery?

Clustery zkušební verze jsou tři hostitelské clustery, které se používají při měsíčních hodnoceních privátních cloudů řešení Azure VMware.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Můžu pro zkušební clustery použít špičkové hostitele?

No. Vysoce koncoví hostitelé ESXi jsou rezervované pro použití v produkčních clusterech.

## <a name="azure-vmware-solution-and-vmware-software"></a>Řešení Azure VMware a software VMware

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Jaké verze softwaru VMware se používají v privátních cloudech?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


### <a name="do-private-clouds-use-vmware-nsx"></a>Používají privátní cloudy VMware NSX?

Ano, NSX-T 2,5 se používá pro softwarově definované sítě v privátních cloudech řešení Azure VMware.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Můžu použít VMware NSX-V v privátním cloudu?

No. NSX-T je jediná podporovaná verze NSX.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Je NSX vyžadováno v místních prostředích nebo sítích, které se připojují k privátnímu cloudu?

Ne, nemusíte používat NSX místně.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Jaký je plán upgradu a aktualizace softwaru VMware v privátním cloudu?

Upgrady sady prostředků privátního cloudu udržují software v rámci jedné verze nejnovější verze softwarového balíčku od VMware. Verze privátního cloudového softwaru se mohou lišit od nejaktuálnější verze jednotlivých softwarových komponent (ESXi, NSX-T, vCenter, síti vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Jak často bude balíček softwaru pro privátní cloud aktualizovaný?

Software privátního cloudu se upgraduje podle plánu, který sleduje vydání softwarového balíčku od VMware. Váš privátní cloud nevyžaduje pro upgrady výpadky.

## <a name="connectivity"></a>Připojení

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Jaké plánování síťových IP adres je potřeba k začleňování privátních cloudů do místních prostředí?

K nasazení privátního cloudu řešení Azure VMware je nutný adresní prostor privátní sítě/22. Tento privátní adresní prostor by se neměl překrývat s ostatními virtuálními sítěmi v předplatném nebo v místních sítích.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Návody se připojit z místních prostředí k privátnímu cloudu řešení Azure VMware?

Ke službě se můžete připojit jedním ze dvou způsobů: 

- S virtuálním počítačem nebo aplikační bránou nasazenou ve službě Azure Virtual Network, která je peered přes ExpressRoute, do privátního cloudu.
- Prostřednictvím ExpressRoute Global Reach z místního datového centra do okruhu Azure ExpressRoute.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Návody připojit virtuální počítač úlohy k Internetu nebo koncovému bodu služby Azure?

V Azure Portal povolte připojení k Internetu pro privátní cloud. Pomocí Správce NSX-T vytvořte směrovač NSX-T T1 a logický přepínač. Pak pomocí vCenter nasadíte virtuální počítač na segment sítě definovaný logickým přepínačem. Tento virtuální počítač bude mít síťový přístup k Internetu a službám Azure.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Potřebuji omezit přístup z Internetu k virtuálním počítačům v logických sítích v privátním cloudu?

No. Příchozí síťový provoz z Internetu přímo do privátních cloudů není ve výchozím nastavení povolen.  Můžete ale vystavovat virtuální počítače řešení Azure VMware na internetu prostřednictvím možnosti [veřejné IP adresy](public-ip-usage.md) v Azure Portal pro váš privátní cloud řešení Azure VMware.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Je potřeba omezit přístup k Internetu z virtuálních počítačů v logických sítích na Internet?

Ano. K vytvoření brány firewall, která bude omezovat přístup virtuálních počítačů k Internetu, budete muset použít Správce NSX-T.


### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Může řešení Azure VMware používat brány ExpressRoute hostované v Azure Virtual WAN?
Ano.

### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Dá se přes Azure Virtual WAN přes ExpressRoute Global Reach navázat připojení mezi místním prostředím a řešením Azure VMware?
Služba Azure Virtual WAN neposkytuje přechodné směrování mezi dvěma připojenými okruhy ExpressRoute a nevirtuální bránou WAN ExpressRoute. Použití ExpressRoute Global Reach umožňuje připojení mezi místním prostředím a řešením Azure VMware, ale prostřednictvím globální sítě Microsoftu místo virtuálního centra WAN.

### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>Můžu použít HCX prostřednictvím veřejné internetové komunikace jako alternativní řešení pro nepodporu HCX při použití VPN S2S s vWAN pro místní komunikaci?

V současné době je jedinou podporovanou metodou pro VMware HCX prostřednictvím ExpressRoute.

## <a name="accounts-and-privileges"></a>Účty a oprávnění

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Jaké účty a oprávnění se zobrazí s novým privátním cloudem řešení Azure VMware?

K dispozici jsou přihlašovací údaje pro uživatele cloudadmin v vCenter a přístup správce NSX-T Manageru. K dispozici je také skupina CloudAdmin, kterou lze použít k začlenění Azure Active Directory. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Může mít správce přístup k hostitelům ESXi?

Ne, přístup správce k ESXi je omezen na splnění požadavků na zabezpečení řešení.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Jaká oprávnění a oprávnění budou v vCenter?

Budete mít CloudAdmin oprávnění skupiny. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Jaká oprávnění a oprávnění bude mít správce NSX-T?

Budete mít oprávnění správce s úplnými oprávněními na NSX-T a spravovat řízení přístupu na základě rolí vSphere, jako byste to měli v místním datovém centru NSX – T. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

> [!NOTE]
> Vytvoří se a nakonfiguruje směrovač T0 v rámci nasazení privátního cloudu. Jakékoli změny tohoto logického směrovače nebo virtuálních počítačů NSX-T hraničních uzlů můžou mít vliv na připojení k vašemu privátnímu cloudu.

## <a name="billing-and-support"></a>Fakturace a podpora

### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Jak budou strukturované ceny pro řešení Azure VMware?

Obecné otázky týkající se cen najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/azure-vmware) řešení Azure VMware. 

### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Dá se řešení Azure VMware koupit prostřednictvím CSP Microsoftu?

Ano, zákazníci můžou nasazovat řešení Azure VMware v rámci předplatného Azure spravovaného CSP.

### <a name="who-supports-azure-vmware-solution"></a>Kdo podporuje řešení Azure VMware?

Microsoft poskytuje podporu pro řešení Azure VMware. [Žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)můžete odeslat. 

U předplatných spravovaných poskytovatelem CSP nabízí první úroveň podpory poskytovatele řešení stejným způsobem jako CSP pro jiné služby Azure.

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Jaké účty potřebuji k vytvoření privátního cloudu řešení Azure VMware?

Budete potřebovat účet Azure v předplatném Azure.

### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Jsou řešení Red Hat podporovaná v řešení Azure VMware?

Microsoft a Red Hat sdílejí integrovaný, společně umístěný tým podpory, který poskytuje jednotný kontaktní bod pro ekosystémy Red Hat běžící na platformě Azure.  Podobně jako jiné služby platformy Azure, které pracují s Red Hat Enterprise Linux, spadají řešení Azure VMware do cloudového přístupu a integrovaného sjednocení podpory. Red Hat Enterprise Linux se podporuje pro běh řešení Azure VMware v rámci Azure.

### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>Je k dispozici VMware HCX Enterprise, a pokud ano, jak spousta stojí?

VMware HCX Enterprise je k dispozici v rámci řešení Azure VMware jako funkce nebo služby ve *verzi Preview* . I když je řešení VMware HCX Enterprise for Azure ve verzi Preview, je to bezplatná funkce nebo služba a podléhá podmínkám a ujednáním služby ve verzi Preview. Jakmile se služba VMware HCX Enterprise dostane do GA, dostanete 30denní oznámení o tom, že účtování bude přepínat. Můžete ho přepnout nebo odhlásit od služby.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Jak Návody požádat o zvýšení kvóty hostitele pro řešení Azure VMware?

U předplatných spravovaných poskytovatelem CSP musí zákazník odeslat žádost partnerovi. Partnerský tým se potom zavazuje Microsoftu, aby zvýšil kvótu pro předplatné. Další informace najdete v tématu [Povolení prostředku řešení Azure VMware](enable-azure-vmware-solution.md). 

Pro předplatná EA použijte následující postup. Nejdřív budete potřebovat:

* [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.
* Účet Azure v předplatném Azure.

Než budete moct vytvořit prostředek řešení Azure VMware, odešlete lístek podpory, který bude mít přidělené hostitele. Ověření a splnění vaší žádosti trvá až pět pracovních dnů. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více hostitelů, Projděte si stejný postup.

1. V Azure Portal v části **pomoc a podpora** vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
   - **Typ problému:** Odbornou
   - **Předplatné:** Vyberte své předplatné.
   - **Služba:** Všechny služby > řešení Azure VMware
   - **Prostředek:** Obecná otázka 
   - **Shrnutí:** Potřebná kapacita
   - **Typ problému:** Problémy se správou kapacity
   - **Podtyp problému:** Požadavek zákazníka na další kvótu/kapacitu hostitele

1. V **popisu** lístku podpory zadejte na kartě **Podrobnosti** :

   - KONCEPCE nebo produkce 
   - Název oblasti
   - Počet hostitelů
   - Všechny další podrobnosti

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři hostitele, aby se nastavil váš privátní cloud a redundance N + 1 hostitelů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a chcete požádat o další hostitele, musíme pro přidělení hostitelů potřebovat pět pracovních dnů. 

1. Než budete moci zřídit hostitele, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md). 

### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Jsou rezervované instance dostupné k nákupu prostřednictvím programu Cloud Solution Provider (CSP)?

Ano. CSP může koupit rezervované instance pro zákazníky. Další informace najdete v tématu [úspory nákladů pomocí rezervované instance](reserved-instance.md). 

### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Nabízí řešení Azure VMware víceklientské prostředí pro hostování partnerů CSP?

No. V současné době řešení Azure VMware nenabízí víceklientské prostředí.

### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>Budou přenosy dat mezi místními a Azure VMware přes ExpressRoute účtovány za všechny poplatky za odchozí přenosy dat v plánu měřených dat?

Provoz v okruhu řešení Azure VMware ExpressRoute se nijak neměří. Provoz z okruhu ExpressRoute, který se připojuje k místnímu prostředí do Azure, se účtuje podle cenových tarifů ExpressRoute.


## <a name="customer-communication"></a>Komunikace se zákazníky

### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Jak obdržím výstrahu, když Azure pošle oznámení o stavu služby do předplatného Azure?

Problémy se službami, plánovaná údržba a Poradce pro stav, oznámení o výstrahách zabezpečení se zveřejňují prostřednictvím **Service Health** v Azure Portal.  Když nastavíte výstrahy protokolu aktivit pro tato oznámení, můžete provést včasné akce. Další informace najdete v tématu [vytvoření výstrah služby Service Health pomocí Azure Portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Snímek obrazovky s oznámením o Service Health":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
