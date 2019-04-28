---
title: Řešení potíží s navrácení služeb po obnovení do místní během zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje způsoby řešení potíží navrácení služeb po obnovení a opětovného nastavování ochrany při zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565595"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Řešení potíží s navrácení služeb po obnovení do místní z Azure

Tento článek popisuje, jak řešit potíže se mohou vyskytnout při předání služeb při obnovení virtuálních počítačů Azure do místní infrastrukturu VMware a po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. Pro první krok po převzetí služeb při selhání, budete muset znovunastavení ochrany virtuálních počítačů Azure do místních, tak, aby se začaly replikovat. Druhým krokem je spuštění převzetí služeb při selhání z Azure a navrácení služeb po obnovení v místní lokalitě.

## <a name="troubleshoot-reprotection-errors"></a>Řešení potíží s chybami opětovného nastavování ochrany

Tato část podrobně popisuje běžné chyby opětovného nastavování ochrany a jak je opravit.

### <a name="error-code-95226"></a>Kód chyby: 95226

**Operace opětovného zapnutí ochrany se nezdařila, protože virtuální počítač Azure nemohl kontaktovat místní konfigurační server.**

K této chybě dochází při:

* Virtuální počítač Azure kontaktovat místní konfigurační server. Virtuální počítač nelze zjistit a zaregistrován ke konfiguračnímu serveru.
* Služba InMage Scout application neběží na virtuálním počítači Azure po převzetí služeb při selhání. Služba je potřeba ke komunikaci s místní konfigurační server.

Řešení tohoto problému:

* Zkontrolujte, že síť virtuálních počítačů Azure umožňuje virtuálnímu počítači Azure ke komunikaci s místní konfigurační server. Můžete nastavit síť site-to-site VPN do vašeho místního datového centra nebo připojení Azure ExpressRoute nakonfigurovat soukromý partnerský vztah virtuální sítě virtuálního počítače Azure.
* Pokud virtuální počítač může komunikovat s místní konfigurační server, přihlaste se k virtuálnímu počítači. Zkontrolujte službu InMage Scout application. Pokud se zobrazí, že neběží, spusťte službu ručně. Zkontrolujte, že služba spuštěna typ je nastavena na **automatické**.

### <a name="error-code-78052"></a>Kód chyby: 78052

**U virtuálního počítače nejde dokončit ochrana.**

Tento problém může dojít, pokud virtuální počítač na hlavním cílovém serveru, ke kterému jste se navrácení služeb po obnovení se stejným názvem již existuje.

Řešení tohoto problému:

* Vyberte jiný hlavní cílový server na jiného hostitele tak, aby opětovného nastavování ochrany vytvoří počítače na jiného hostitele, kde není v konfliktu názvů.
* Také můžete řešení vMotion přesunout na hlavním cíli na jiného hostitele, kde kolize názvů neprovede. Pokud existujícího virtuálního počítače je zapomenutý počítač, přejmenujte ho tak, aby nový virtuální počítač se dají vytvořit na stejném hostiteli ESXi.


### <a name="error-code-78093"></a>Kód chyby: 78093

**Virtuální počítač neběží ve stavu, kdy nereaguje, nebo není přístupný.**

Řešení tohoto problému:

K znovunastavení ochrany virtuálního počítače s převzetím služeb při selhání, musí být spuštěn virtuální počítač Azure, tak, aby služba Mobility zaregistruje se pomocí konfigurace místního serveru a může spustit replikaci tím, že komunikuje s procesový server. Pokud tento počítač je připojen k síti nesprávná nebo není spuštěná (ne reagovat nebo vypněte), konfigurační server se nemůže připojit službu Mobility na virtuálním počítači zahajte opětovné nastavování ochrany.

* Aby mohla začít, komunikaci zpět do místních, restartujte virtuální počítač.
* Po spuštění virtuálního počítače Azure, restartujte tuto úlohu znovunastavení ochrany.

### <a name="error-code-8061"></a>Kód chyby: 8061

**Úložiště není přístupný z hostitele ESXi.**

Zkontrolujte [hlavního cílového požadavky a podporovanými úložišti dat](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) navrácení služeb po obnovení.


## <a name="troubleshoot-failback-errors"></a>Řešení potíží s chybami navrácení služeb po obnovení

Tato část popisuje běžné chyby, které mohou nastat během navrácení služeb po obnovení.

### <a name="error-code-8038"></a>Kód chyby: 8038

**Nepovedlo se vyvolat na místním virtuálním počítači z důvodu chyby.**

Tento problém nastane, pokud nedostanete místní virtuální počítač na hostitele, který nemá dostatek paměti zřízené. 

Řešení tohoto problému:

* Zřízení více paměti na hostiteli ESXi.
* Kromě toho můžete použít řešení vMotion pro přesun virtuálního počítače na jiného hostitele ESXi, který má dostatek paměti ke spuštění virtuálního počítače.
