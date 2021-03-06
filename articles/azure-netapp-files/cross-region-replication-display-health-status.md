---
title: Zobrazit stav Azure NetApp Files relace replikace | Microsoft Docs
description: V této části najdete popis postupu zobrazení stavu replikace ve zdrojovém svazku nebo cílovém svazku Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: a14656bc1abc42fdc3e948fdae1ca07469681d21
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249848"
---
# <a name="display-health-status-of-replication-relationship"></a>Zobrazení stavu vztahu replikace 

Stav replikace můžete zobrazit na zdrojovém svazku nebo cílovém svazku.  

## <a name="display-replication-status"></a>Zobrazit stav replikace

1. Ze zdrojového svazku nebo cílového svazku klikněte na **replikace** v části služba úložiště pro libovolný svazek.

    Zobrazí se následující stav replikace a informace o stavu:  
    * **Typ koncového bodu** – určuje, jestli je svazek zdrojem nebo cílem replikace.
    * **Stav** – zobrazí stav vztahu replikace.
    * **Stav zrcadlení** – zobrazuje jednu z následujících hodnot:
        * *Neinicializované*:  
            Toto je počáteční a výchozí stav při vytvoření vztahu partnerských vztahů. Stav zůstane Neinicializovaný, dokud se inicializace dokončí úspěšně.
        * *Zrcadlené*:   
            Cílový svazek byl inicializován a je připraven k příjmu aktualizací zrcadlení.
        * *Přerušeno*:   
            Toto je stav po přerušení vztahu partnerských vztahů. Cílový svazek je `‘RW’` a snímky jsou k dispozici.
    * **Stav vztahu** – zobrazuje jednu z následujících hodnot: 
        * *Nečinné*:  
            Neprobíhá žádná operace přenosu a nejsou zakázány budoucí přenosy.
        * Probíhá *přenos*:  
            Probíhá operace přenosu a budoucí převody nejsou zakázány.
    * **Plán replikace** – ukazuje, jak se budou provádět aktualizace přírůstkového zrcadlení po dokončení inicializace (základní kopie).

    * **Celkový průběh** – zobrazí celkové množství dat přenesených v bajtech pro aktuální operaci přenosu. Toto je skutečné přenesené bity a může se lišit od logického místa, které je v sestavě zdrojové a cílové svazky.  

    ![Stav replikace](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> Vztah replikace zobrazuje stav není v *pořádku* , pokud nejsou dokončené předchozí úlohy replikace. Tento stav je výsledkem přenosu velkých objemů s menším intervalem přenosu (například doba přenosu deset minut pro velký objem). V takovém případě stav vztahu zobrazuje *převádění* a stav stavu není v *pořádku*.

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Správa zotavení po havárii](cross-region-replication-manage-disaster-recovery.md)
* [Metriky replikace svazků](azure-netapp-files-metrics.md#replication)
* [Odstranit replikace svazků nebo svazky](cross-region-replication-delete.md)
* [Řešení potíží s replikací mezi oblastmi](troubleshoot-cross-region-replication.md)

