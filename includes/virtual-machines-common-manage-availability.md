---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ba21dfc900145ceeacab6c363e5de84b830282b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82109540"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Vysvětlení restartování virtuálních počítačů – údržba vs. výpadek
Existují tři scénáře, které mohou vést k ovlivnění virtuálního počítače v Azure: neplánovaná údržba hardwaru, neočekávané výpadky a plánovaná údržba.

* K **neplánovaným událostem údržby hardwaru** dochází v případě, kdy platforma Azure předpoví, že dojde k selhání hardwaru nebo jakékoli komponenty platformy přidružené k fyzickému počítači. Když platforma předpoví selhání, vydá událost neplánované údržby hardwaru, aby se snížil dopad na virtuální počítače hostované na tomto hardwaru. Azure používá technologii [migrace za provozu](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) k migraci Virtual Machines z neúspěšného hardwaru na dobrý fyzický počítač. Migrace za provozu je operace zachovávající virtuální počítač, který pozastaví jenom na krátkou dobu. Paměť, otevřené soubory a síťová připojení se zachovají, ale výkon se může před událostí a/nebo po ní snížit. V případech, kdy není možné použít Migraci za provozu, dojde k neočekávanému výpadku virtuálního počítače, jak je popsáno níže.


* **Neočekávaný výpadek** je při neočekávaném výpadku hardwaru nebo fyzické infrastruktury virtuálního počítače. To může zahrnovat selhání místní sítě, selhání místního disku nebo jiné chyby na úrovni racku. Po zjištění platformy Azure automaticky migruje (zaznamená) váš virtuální počítač do správného fyzického počítače ve stejném datovém centru. Během opravné procedury jsou virtuální počítače odstavené (restartují se) a v některých případech dojde ke ztrátě dočasné jednotky. Připojené disky s operačním systémem a datové disky se vždy zachovají.

  Virtuální počítače můžou také zacházet s výpadky v nepravděpodobném případě výpadku nebo havárie, které mají vliv na celé datové centrum nebo dokonce na celé oblasti. V těchto scénářích poskytuje Azure možnosti ochrany včetně [zón dostupnosti](../articles/availability-zones/az-overview.md) a [spárovaných oblastí](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Plánované události údržby** jsou pravidelné aktualizace základní platformy Azure prováděné Microsoftem za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače. U většiny těchto aktualizací nemá jejich provedení žádný vliv na vaše služby Virtual Machines ani Cloud Services (viz [Údržba se zachováním virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Přestože se platforma Azure pokouší použít údržbu se zachováním virtuálních počítačů kdykoli je to možné, existují výjimečné případy, kdy tyto aktualizace k aplikaci požadovaných aktualizací na základní infrastrukturu vyžadují restartování virtuálního počítače. V takovém případě můžete provést plánovanou údržbu Azure pomocí operace údržba-opětovné nasazení, která zahájí údržbu virtuálních počítačů ve vhodném časovém intervalu. Další informace najdete v tématu [Plánovaná údržba pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Pokud chcete snížit dopad výpadků kvůli jedné nebo několika takovým událostem, doporučujeme pro vaše virtuální počítače následující osvědčené postupy z hlediska vysoké dostupnosti:

* [Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]
* [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]
* [Použití naplánovaných událostí k proaktivní reakci na události s vlivem na virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]
* [Kombinování Load Balancer se skupinami dostupnosti]
* [Použití zón dostupnosti k ochraně před chybami na úrovni datacentra]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Použití zón dostupnosti k ochraně před chybami na úrovni datacentra

[Zóny dostupnosti](../articles/availability-zones/az-overview.md) rozšiřují úroveň řízení, kterou máte k dispozici, abyste zachovali dostupnost aplikací a dat na vašich virtuálních počítačích. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč Zóny dostupnosti, aby se chránily před jednotlivými chybami.

Zóna dostupnosti v oblasti Azure je kombinací **domény selhání** a **aktualizační domény**. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, budou vaše virtuální počítače efektivně distribuovány mezi tři domény selhání a tři aktualizační domény. Platforma Azure tuto distribuci rozpoznává mezi aktualizačními doménami, aby se zajistilo, že se virtuální počítače v různých zónách neaktualizují současně.

Díky Zóny dostupnosti Azure nabízí nejlepší smlouvu SLA 99,99% provozu virtuálního počítače. Díky navrhování vašich řešení pro použití replikovaných virtuálních počítačů v zónách můžete chránit aplikace a data před ztrátou datacentra. Pokud dojde k ohrožení jedné zóny, replikované aplikace a data jsou okamžitě k dispozici v jiné zóně.

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače se [systémem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) v zóně dostupnosti.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance
Skupiny dostupnosti jsou další konfigurace datového centra, která poskytuje redundanci a dostupnost virtuálních počítačů. Tato konfigurace v rámci datového centra zajišťuje, že během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač a splňuje 99,95% Azure SLA. Další informace najdete v tématu [SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Virtuální počítač s jednou instancí v samotné skupině dostupnosti by měl použít SSD úrovně Premium nebo Ultra disk pro všechny disky a datové disky operačního systému, aby bylo možné získat oprávnění SLA pro připojení k virtuálnímu počítači aspoň 99,9%.

Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí **aktualizační doménu** a **doménu selhání**. Dané skupině dostupnosti se ve výchozím nastavení přiřadí pět aktualizačních domén (u nasazení podle modelu Resource Manager je pak možné počet aktualizačních domén navýšit až na 20), které uživatel nemůže konfigurovat a které představují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače konfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání pro nasazení podle modelu Resource Manager (do dvou domén selhání pro model nasazení Classic). Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

<!--Image reference-->
   ![Koncepční nákres konfigurace aktualizačních domén a domén selhání](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti
Pokud aktuálně používáte virtuální počítače s nespravovanými disky, důrazně doporučujeme [převést virtuální počítače ve skupině dostupnosti na používání spravovaných disků](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md) poskytují vyšší spolehlivost skupiny dostupnosti tím, že zajišťují dostatečné oddělení jednotlivých disků virtuálních počítačů ve skupině dostupnosti, aby se zabránilo jedinému bodu selhání. Provede to tím, že automaticky umístí disky do různých domén selhání úložiště (clustery úložiště) a zarovnají je s doménou selhání virtuálního počítače. Pokud doména selhání úložiště selže kvůli selhání hardwaru nebo softwaru, dojde k chybě jenom instance virtuálního počítače s disky v doméně selhání úložiště.
![Doménami selhání spravované disky](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Počet domén selhání pro spravované skupiny dostupnosti se liší podle oblasti – buď dvě, nebo tři na oblast. Doménu selhání pro každou oblast si můžete zobrazit spuštěním následujících skriptů.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Za určitých okolností by dva virtuální počítače ve stejné AvailabilitySet mohly sdílet stejný FaultDomain. Můžete to ověřit tak, že do své skupiny dostupnosti zavedete a zkontrolujete sloupec **Doména selhání** .
> To může být způsobeno při nasazení virtuálních počítačů z následujícího pořadí:
> - Nasazení prvního virtuálního počítače
> - Zastavení nebo zrušení přidělení prvního virtuálního počítače
> - Za těchto okolností nasaďte druhý virtuální počítač. disk s operačním systémem druhého virtuálního počítače se dá vytvořit ve stejné doméně selhání jako první virtuální počítač, takže druhý virtuální počítač se také bude nakládat na stejný FaultDomain. 
> Chcete-li se tomuto problému vyhnout, doporučujeme zastavit nebo zrušit přidělení virtuálních počítačů mezi nasazeními.

Pokud máte v úmyslu používat virtuální počítače s nespravovanými disky, postupujte podle osvědčených postupů pro účty úložiště, kde jsou virtuální pevné disky (VHD) virtuálních počítačů uložené jako [objekty blob stránky](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Uchovávejte všechny disky (s operačním systémem i s daty) přidružené k virtuálnímu počítači ve stejném účtu úložiště.**
2. Před přidáním dalších virtuálních pevných disků do účtu úložiště **Zkontrolujte [omezení](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) počtu nespravovaných disků v Azure Storageovém účtu** .
3. **Pro každý virtuální počítač ve skupině dostupnosti použijte samostatný účet úložiště.** Nesdílejte účty služby Storage mezi více virtuálními počítači ve stejné skupině dostupnosti. Je přijatelné pro virtuální počítače v různých skupinách dostupnosti ke sdílení účtů úložiště, pokud jsou výše osvědčené ![postupy následovány nespravovanými disky doménami selhání](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Použití naplánovaných událostí k proaktivní reakci na události s vlivem na virtuální počítače

Když se přihlásíte k odběru [plánovaných událostí](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), váš virtuální počítač se upozorní na nadcházející události údržby, které můžou mít vliv na váš virtuální počítač. Po povolení naplánovaných událostí se vašemu virtuálnímu počítači přidává minimální doba před provedením aktivity údržby. Například aktualizace operačního systému hostitele, které by mohly mít vliv na váš virtuální počítač, se zařadí do fronty jako události, které určují dopad, a také čas, kdy se údržba provede, když se neprovede žádná akce. Pokud Azure zjistí bezprostřední selhání hardwaru, které by mohlo mít vliv na váš virtuální počítač, můžete také zařadit do fronty plánované události. to vám umožní určit, kdy se má opravit. Zákazníci mohou tuto událost použít k provádění úkolů před údržbou, jako je například ukládání stavu, selhání při selhání do sekundárního a tak dále. Po dokončení logiky pro řádné zpracování události údržby můžete schválit nedokončené plánované události a povolit, aby platforma pokračovala s údržbou.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Konfigurace jednotlivých vrstev aplikace na samostatné zóny dostupnosti nebo skupiny dostupnosti
Pokud jsou vaše virtuální počítače téměř totožné a slouží pro vaši aplikaci stejný účel, doporučujeme pro každou vrstvu aplikace nakonfigurovat zónu dostupnosti nebo skupinu dostupnosti.  Pokud ve stejné zóně dostupnosti nebo sadě umístíte dvě různé úrovně, můžou se všechny virtuální počítače ve stejné aplikační vrstvě restartovat najednou. Konfigurací aspoň dvou virtuálních počítačů v zóně dostupnosti nebo nastavením pro každou úroveň zajistíte, aby byl k dispozici alespoň jeden virtuální počítač v každé vrstvě.

Můžete například umístit všechny virtuální počítače na front-end aplikace, na které běží IIS, Apache a Nginx, v jedné zóně dostupnosti nebo sadě. Ujistěte se, že se ve stejné zóně dostupnosti nebo sadě nacházejí pouze front-end virtuální počítače. Podobně zajistěte, aby byly pouze virtuální počítače datové vrstvy umístěny do vlastní zóny dostupnosti nebo sady, jako jsou například replikované SQL Server virtuální počítače nebo vaše virtuální počítače MySQL.

<!--Image reference-->
   ![Úrovně aplikace](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Kombinování nástroje pro vyrovnávání zatížení se zónami nebo sadami dostupnosti
Zkombinujte [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) se zónou dostupnosti nebo nastavte, aby se získala nejvyšší odolnost aplikace. Azure Load Balancer rozděluje provoz mezi víc virtuálních počítačů. Azure Load Balancer je součástí virtuálních počítačů úrovně Standard. Ne všechny úrovně virtuálních počítačů zahrnují nástroj Azure Load Balancer. Další informace o vyrovnávání zatížení virtuálních počítačů najdete v článku [Vyrovnávání zatížení virtuálních počítačů](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Pokud nástroj pro vyrovnávání zatížení není nakonfigurovaný k vyrovnávání provozu mezi víc virtuálních počítačů, všechny plánované události údržby ovlivní jediný virtuální počítač obsluhující provoz a způsobí výpadek vrstvy aplikace. Umístěním více virtuálních počítačů na stejné vrstvě aplikace do stejné skupiny dostupnosti a pod jeden nástroj pro vyrovnávání zatížení umožníte nepřetržitou obsluhu provozu alespoň jednou instancí.

Kurz o tom, jak vyrovnávat zatížení napříč zónami dostupnosti, najdete v tématu [Vyrovnávání zatížení virtuálních počítačů napříč všemi zónami dostupnosti pomocí Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Kombinování Load Balancer se skupinami dostupnosti]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]: #use-managed-disks-for-vms-in-an-availability-set
[Použití zón dostupnosti k ochraně před chybami na úrovni datacentra]: #use-availability-zones-to-protect-from-datacenter-level-failures
