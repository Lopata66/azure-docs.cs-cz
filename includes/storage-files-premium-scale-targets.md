---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698021"
---
#### <a name="additional-premium-file-share-level-limits"></a>Další omezení úrovně sdílení souborů Premium

|Plošný  |Cíl  |
|---------|---------|
|Minimální velikost – zvýšení a snížení velikosti    |1 GiB      |
|Směrný IOPS    |400 + 1 IOPS na GiB, až 100 000|
|Shlukování IOPS    |Max (4000, 3x IOPS na GiB), až 100 000|
|Míra odchozích dat         |60 MiB/s + 0,06 * zřízené GiB        |
|Míra příchozího přenosu dat| 40 MiB/s + 0,04 * zřízené GiB |

#### <a name="file-level-limits"></a>Omezení na úrovni souborů

|Plošný  |Standardní soubor  |Soubor úrovně Premium  |
|---------|---------|---------|
|Velikost     |1 TiB         |4 TiB         |
|Maximální počet vstupně-výstupních operací na soubor      |1 000         |Až 8 000 *         |
|Souběžné popisovače     |2 000         |2 000         |
|Výchozí přenos dat     |Zobrazit standardní hodnoty propustnosti souboru         |300 MiB/s (až 1 GiB/s s protokolem SMB vícekanálový náhled) * *         |
|Příchozí přenos dat     |Zobrazit standardní hodnoty propustnosti souboru         |200 MiB/s (až 1 GiB/s s protokolem SMB vícekanálový náhled) * *        |
|Propustnost     |Až 60 MiB/s         |Viz hodnoty vstupně-výstupních souborů Premium         |

\*<sup>Platí pro iOS pro čtení a zápis (obvykle menší počet vstupně-výstupních operací <= 64 KB). Operace s metadaty, kromě čtení a zápisu, mohou být nižší. </sup>

\*\*V <sup> závislosti na omezeních sítě počítačů, dostupné šířce pásma, vstupně-výstupních operacích, hloubkě fronty a dalších faktorech. Podrobnosti najdete v článku o [výkonu vícekanálového protokolu SMB](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
