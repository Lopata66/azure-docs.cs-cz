---
title: Aktualizace Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje přehled o aktualizacích a pokyny k upgradu komponent používaných ve službě Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/25/2019
ms.author: rajanaki
ms.openlocfilehash: dc15c8935cd3c20037bbed01f0f24cd2b8642045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540932"
---
# <a name="service-updates-in-azure-site-recovery"></a>Aktualizace služeb ve službě Azure Site Recovery
Jako organizace budete muset zjistit, jak budete bezpečnost vašich dat a aplikací a úloh v případě plánovaných a neplánovaných výpadků. Azure Site Recovery přispívá ke strategii BCDR tím, že vaše aplikace běžící na virtuálních počítačích a fyzických serverů, které jsou k dispozici, pokud web přestane fungovat. Site Recovery replikuje úlohy spuštěné na virtuálních počítačích a fyzických serverech, aby zůstaly dostupné v sekundárním umístění v případě, že je primární lokalita nedostupná. Po opětovném zprovoznění primární lokality do ní úlohy obnoví.

Site Recovery může spravovat replikaci pro:

- [Virtuální počítače Azure replikované mezi oblastmi Azure](azure-to-azure-tutorial-dr-drill.md).
- Místní virtuální počítače a fyzické servery replikované do Azure nebo sekundární lokality.
Vědět, informace najdete v dokumentaci k [tady](https://docs.microsoft.com/azure/site-recovery) .

Azure Site Recovery vydává aktualizace služeb v pravidelných intervalech – včetně přidání nových funkcí, vylepšení v matici podpory a opravy chyb, pokud existuje. Aby bylo možné zachovat aktuální využívat všechny nejnovější funkce a vylepšení & opravy chyb, pokud existuje, uživatelé byli informováni vždy aktualizovat na nejnovější verze komponent Azure SIte Recovery. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Prohlášení o odborné pomoci pro Azure Site Recovery 

> [!IMPORTANT]
> **S každou novou verzí "n" Azure Site Recovery komponentu, která je všeobecně dostupné, všechny verze nižší než n-4' je považován za bez podpory**. Proto je vždy vhodné k upgradu na nejnovější verze k dispozici.

> [!IMPORTANT]
> Oficiální podpora pro upgrady je z > N-4 na verze N (N je nejnovější verze). Pokud používáte N-6, budete muset nejprve upgradovat na N-4 a poté provedli upgrade na N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Upgrade, pokud rozdíl mezi aktuální verze a nejnovější vydanou verzi je větší než 4

1. Jako první krok upgradovat aktuálně nainstalovanou součást z verze say N na N + 4 a potom přejděte na další kompatibilní verzi. Řekněme, že aktuální verze je 9,24 a jsou na 9.16, nejdříve upgradovat na 9,20 a potom na 9,24.
2. Opakujte stejný postup pro všechny součásti v závislosti na scénáři.

### <a name="support-for-latest-oskernel-versions"></a>Podporu pro nejnovější verze operačního systému nebo jádra

> [!NOTE]
> Pokud máte plánované časové období údržby, a je součástí stejného restartovat počítač, doporučujeme nejdřív upgradovat součásti Site Recovery a pokračovat se zbytkem naplánované aktivity.

1. Před upgradem verze jádra/OS, nejdříve ověřte, pokud cílová verze je podporována službou Azure Site Recovery. Informace najdete v naší dokumentaci pro virtuální počítače Azure, [virtuálních počítačů VMware](vmware-physical-azure-support-matrix.md) & virtuální počítače Hyper-v
2. Přečtěte si naše [aktualizace služeb](https://azure.microsoft.com/updates/?product=site-recovery) a zjistěte, jaké verze služby Site Recovery komponenty podporují konkrétní verzi, kterou chcete provést upgrade na.
3. Nejprve upgradujte na nejnovější verzi lokality obnovit.
4. Nyní upgrade na požadované verze operačního systému nebo jádru.
5. Proveďte restart.
6. Tím se zajistí, že verze operačního systému nebo jádra na počítačích upgradují na nejnovější verzi, a také, že nejnovější změny Site Recovery, které jsou vyžadovány pro podporu nové verze se také načtou na zdrojovém počítači.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače Azure do Azure
V tomto scénáři, důrazně doporučujeme, aby vám [povolit](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) automatické aktualizace. Můžete zvolit, aby Site Recovery ke správě aktualizací následujícími způsoby:

- Jako součást kroku povolení replikace
- Přepnout rozšíření aktualizovat nastavení uvnitř tohoto trezoru

V případě, že jste se rozhodli spravovat aktualizace ručně, postupujte podle těchto kroků:

1. Přejděte na web Azure portal a potom přejděte k vaší "trezor služby Recovery services."
2. Přejděte do podokna "Replikované položky" na webu Azure Portal pro "trezor služby Recovery services."
3. Klikněte na následující oznámení v horní části obrazovky:
    
    *Je dostupná nová aktualizace agenta replikace služby Site Recovery*
    
    *-> Klikněte na nainstalovat*

4. Vyberte virtuální počítače, které chcete použít k aktualizaci a pak klikněte na **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Mezi dvěma místními umístěními VMM
1. Stáhněte si nejnovější aktualizaci kumulativní pro Microsoft Azure Site Recovery Provider.
2. Nejdříve nainstalujte kumulativní aktualizace na serveru VMM v místním, který je Správa lokality pro obnovení.
3. Po obnovení lokality aktualizována, nainstalujte kumulativní aktualizace na serveru VMM, který spravuje primární lokality.

> [!NOTE]
> Pokud VMM vysoce dostupný nástroj VMM (Clusterované VMM), ujistěte se, že instalace upgradu na všech uzlech clusteru, ve kterém je nainstalovaná služba nástroje VMM.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mezi místní lokalitou VMM a službou Azure
1. Stáhněte si kumulativní aktualizace pro Microsoft Azure Site Recovery Provider.
2. Nainstalujte kumulativní aktualizaci na místní server VMM.
3. Nainstalujte nejnovější verzi agenta MARS agenta na všech hostitelích Hyper-V.

> [!NOTE]
> Pokud váš nástroj VMM vysoce dostupný nástroj VMM (Clusterované VMM), ujistěte se, že instalace upgradu na všech uzlech clusteru, ve kterém je nainstalovaná služba nástroje VMM.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Mezi místní lokality Hyper-V a Azure

1. Stáhněte si kumulativní aktualizace pro Microsoft Azure Site Recovery Provider.
2. Nainstalujte zprostředkovatele na každém uzlu servery Hyper-V, které jste zaregistrovali ve službě Azure Site Recovery.

> [!NOTE]
> Pokud technologie Hyper-V je server hostitele v clusteru Hyper-V, ujistěte se, že instalace upgradu na všech uzlech clusteru

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Mezi VMware v místním prostředí nebo fyzického serveru do Azure

Než budete pokračovat s aktualizacemi, odkazovat na [výrazem podpory Site Recovery](#support-statement-for-azure-site-recovery) pochopit způsob upgradu.

1. Na základě vaší aktuální verzi a podporu příkazu výše uvedené, nainstalujte aktualizaci nejprve na místní server pro správu podle pokynů uvedených [tady](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Jedná se o server, který má konfiguračního serveru a role serveru procesu.
2. Pokud máte horizontální navýšení kapacity zpracování servery, je dále aktualizovat podle následujících pokynů daný [tady](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. V dalším kroku aktualizovat agenta mobility na každé chráněné položky, přejděte na web Azure portal a potom přejděte ke **chráněné položky** > **replikované položky** stránky. Vyberte virtuální počítač na této stránce. Vyberte **Update Agent** tlačítko, které se zobrazí v dolní části stránky pro každý virtuální počítač. Tím se aktualizuje agenta služby Mobility pro všechny chráněné virtuální počítače.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Po upgradu agenta mobility restartování zdrojového počítače

Restartování se doporučuje po každém upgradu agenta Mobility Ujistěte se, že jsou načteny všechny nejnovější změny na zdrojovém počítači. Je ale **není povinné**. Pokud rozdíl mezi verze agenta během posledního restartování a aktuální verze je větší než 4, restartování je povinný. Naleznete v následující tabulce pro podrobné vysvětlení.

|**Verze agenta během poslední restartování** | **Upgrade na** | **Je restartovat povinné?**|
|---------|---------|---------|
|9.16 |  9.18 | Není povinná|
|9.16 | 9.19 | Není povinná|
| 9.16 | 9.20 | Není povinná
 | 9.16 | 9.21 | Ano, nejprve upgradovat na 9,20 a potom restartujte před upgradem na 9.21 jako rozdíl mezi verzemi (9.16, kde byla provedena poslední restartování a cílová verze 9.21) je > 4

## <a name="links-to-currently-supported-update-rollups"></a>Odkazy na aktuálně podporovaných kumulativních aktualizací

|Kumulativní aktualizace  |Poskytovatel  |Jednotný instalační program| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Kumulativní aktualizace 36](https://support.microsoft.com/en-in/help/4503156)     |   5.1.4150.0  |  9.24.5211.1   |  5.1.4150.0  | 2.0.9160.0
|[Kumulativní aktualizace 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[Aktualizovat souhrn 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) – opravy hotfix     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Kumulativní aktualizace 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Kumulativní aktualizace 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Kumulativní aktualizace 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0

## <a name="previous-update-rollups"></a>Předchozí kumulativní aktualizace

- [Kumulativní aktualizace 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)
- [Kumulativní aktualizace 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [Kumulativní aktualizace 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [S kumulativní aktualizací z 27.](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [Kumulativní aktualizace 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Kumulativní aktualizace 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Kumulativní aktualizace 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Kumulativní aktualizace 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Kumulativní aktualizace 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Kumulativní aktualizace 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Kumulativní aktualizace 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
