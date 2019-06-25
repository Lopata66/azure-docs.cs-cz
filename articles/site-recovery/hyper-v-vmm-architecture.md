---
title: Architektura pro zotavení po havárii Hyper-V do sekundární místní lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled architektury pro zotavení po havárii místních virtuálních počítačů Hyper-V do sekundární lokality System Center VMM s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 22f21f11b0c374724bc6924f30ea20a21de6ab90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66398162"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura – replikace Hyper-V do sekundární lokality

Tento článek popisuje komponenty a procesy využívané při replikaci místních virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality VMM s využitím služby [Azure Site Recovery](site-recovery-overview.md) na webu Azure Portal.


## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a obrázek poskytuje souhrnný přehled komponenty používané pro replikaci Hyper-V do sekundární lokality.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure | V předplatném Azure vytvoříte trezor služby Recovery Services sloužící k orchestraci a správě replikace mezi umístěními VMM.
**Server VMM** | Potřebujete primární a sekundární umístění VMM. | Doporučujeme jeden server VMM v primární lokalitě a další v sekundární lokalitě.
**Server Hyper-V** |  Jeden nebo několik hostitelských serverů Hyper-V v primárním a sekundárním cloudu VMM. | Data se replikují mezi primárním a sekundárním hostitelským serverem Hyper-V přes síť LAN nebo VPN na základě protokolu Kerberos nebo ověření certifikátem.  
**Virtuální počítače Hyper-V** | Na hostitelském serveru Hyper-V. | Zdrojový hostitelský server musí mít alespoň jeden virtuální počítač, který chcete replikovat.

**V místním místní architektury**

![Z lokálního prostředí do lokálního prostředí](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikace

1. Při aktivaci počáteční replikace se [snímek virtuálního počítače Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) pořízení snímku.
2. Virtuální pevné disky na virtuálním počítači se replikují jeden po druhém, do sekundární lokality.
3. Pokud dojde ke změnám na disku, kdy probíhá počáteční replikace, Hyper-V Replica Replication Tracker zaznamenává změny zaznamená jako protokoly replikace technologie Hyper-V (.hrl). Tyto protokolové soubory jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor .hrl, který je odeslán do sekundárního umístění. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní a zahájí rozdílová replikace.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

- Můžete převzetí služeb při selhání jednoho počítače nebo vytvořit plány obnovení a orchestrovat převzetí služeb při selhání více počítačů.
- Můžete spustit plánované nebo neplánované převzetí služeb při selhání mezi místními lokalitami. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Pokud provádíte neplánovanému převzetí služeb při selhání do sekundární lokality, po převzetí služeb při selhání počítače v sekundární lokalitě nejsou chráněné.
    - Pokud jste spustili plánované převzetí služeb při selhání, počítače v sekundárním umístění chráněné budou.
- Po počáteční převzetí služeb při selhání spuštění, potvrdíte ho, chcete-li začít používat úlohu na replikovaném virtuálním počítači.
- Když primární lokality opět dostupná, můžete službu navrátit.
    - Zahájíte zpětnou replikaci, ke spuštění replikace ze sekundární lokality na primární. Po zpětné replikaci budou virtuální počítače v chráněném stavu, ale sekundární datové centrum bude stále aktivním umístěním.
    - Chcete-li z primární lokality opět udělat aktivní, zahajte plánované převzetí služeb ze sekundární lokality do primární, následované další zpětnou replikací.



## <a name="next-steps"></a>Další postup


Postupujte podle [v tomto kurzu](hyper-v-vmm-disaster-recovery.md) povolit replikaci technologie Hyper-V mezi cloudy VMM.
