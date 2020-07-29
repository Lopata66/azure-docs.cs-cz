---
title: Možnosti dostupnosti
description: Přečtěte si o funkcích dostupnosti pro spouštění virtuálních počítačů v Azure.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: d26e1edb53f963d591b1ee1fba58b87fd454e898
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288573"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Možnosti dostupnosti pro virtuální počítače v Azure

Tento článek obsahuje přehled funkcí dostupnosti virtuálních počítačů Azure (VM).

## <a name="high-availability"></a>Vysoká dostupnost

Zatížení se obvykle šíří napříč různými virtuálními počítači, aby se zajistila vysoká propustnost, výkon a vytvořila redundance v případě, že je virtuální počítač ovlivněný z důvodu aktualizace nebo jiné události. 

Existuje několik možností, které Azure poskytuje k dosažení vysoké dostupnosti. Nejdřív si budeme mluvit o základních konstrukcích. 

### <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../availability-zones/az-overview.md) rozšiřují úroveň řízení, kterou máte k dispozici, abyste zachovali dostupnost aplikací a dat na vašich virtuálních počítačích. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři Zóny dostupnosti na jednu podporovanou oblast Azure. 

Každá zóna dostupnosti má samostatný zdroj napájení, síť a chlazení. Díky navrhování vašich řešení pro použití replikovaných virtuálních počítačů v zónách můžete chránit aplikace a data před ztrátou datacentra. Pokud dojde k ohrožení jedné zóny, replikované aplikace a data jsou okamžitě k dispozici v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače se [systémem Windows](./windows/create-powershell-availability-zone.md) nebo [Linux](./linux/create-cli-availability-zone.md) v zóně dostupnosti.


### <a name="fault-domains"></a>Domény selhání

Doména selhání je logická skupina hardwarových komponent, které sdílejí společný zdroj napájení a síťový přepínač (je obdobou stojanu v místním datovém centru). 

### <a name="update-domains"></a>Aktualizační domény

Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. 

Tento přístup zajišťuje, že při pravidelné údržbě Azure zůstává vždycky nejméně jedna instance vaší aplikace spuštěná. Restartování aktualizačních domén nemusí během údržby pokračovat sekvenčně, ale v jednu chvíli se restartuje jenom jedna aktualizační doména.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines škálování sad 

Azure Virtual Machine Scale Sets vám umožní vytvořit a spravovat skupinu virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat v reakci na poptávku nebo podle určeného rozvrhu. Sady škálování poskytují vysokou dostupnost vašim aplikacím a umožňují centrálně spravovat, konfigurovat a aktualizovat spoustu virtuálních počítačů. Pro zajištění vysoce dostupné aplikace a pro splnění [99,95% smlouvy SLA pro Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/)doporučujeme, aby se v rámci sady škálování vytvořily dva nebo víc virtuálních počítačů. Pro samotnou sadu škálování se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače. Pokud jeden virtuální počítač používá [Azure Premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), platí Azure SLA pro neplánované události údržby. Virtuální počítače v sadě škálování se dají nasadit mezi více aktualizačními doménami a doménami selhání, aby se maximalizovala dostupnost a odolnost proti výpadkům kvůli výpadkům datového centra a plánovaným nebo neplánovaným událostem údržby. Virtuální počítače v sadě škálování je taky možné nasadit do jedné zóny dostupnosti nebo v regionu. Možnosti nasazení zóny dostupnosti se můžou lišit v závislosti na režimu orchestrace.

**Domény selhání a aktualizační domény**

Služba Virtual Machine Scale Sets zjednodušuje návrh vysoké dostupnosti tím, že zarovnává domény selhání a aktualizační domény. Pro sadu škálování budete muset definovat jenom počet domén selhání. Počet domén selhání dostupných pro sadu škálování se může v jednotlivých oblastech lišit. Viz [Správa dostupnosti virtuálních počítačů v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Skupiny dostupnosti
Skupina dostupnosti je logické seskupení virtuálních počítačů v rámci datového centra, které umožňuje službě Azure pochopit, jak je vaše aplikace sestavená, aby poskytovala redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače. Pokud jeden virtuální počítač používá [Azure Premium SSD](./windows/disks-types.md#premium-ssd), platí Azure SLA pro neplánované události údržby.

Ve skupině dostupnosti jsou virtuální počítače automaticky distribuované napříč těmito doménami selhání. Tento přístup omezuje dopady potenciálního selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

Virtuální počítače, které používají [Azure Managed Disks](./windows/faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. 

Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. Další informace o těchto doménách selhání spravovaného disku pro [virtuální](./linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) počítače se [systémem Linux nebo virtuální počítače s Windows](./windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)najdete v článku.

![Spravovaná Skupina dostupnosti](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Virtuální počítače v rámci skupiny dostupnosti jsou taky automaticky distribuované napříč aktualizačními doménami. 

![Skupiny dostupnosti](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Další kroky
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](/azure/architecture/checklist/resiliency-per-service).

