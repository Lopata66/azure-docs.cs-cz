---
title: Mv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady Mv2-Series.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: c91e9832e4b415a59c12b71964a0da580e9fda21
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651127"
---
# <a name="mv2-series"></a>Řada Mv2

Mv2-Series funkce s vysokou propustností a nízkou latencí běžící na procesorech Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) se základní frekvencí 2,5 GHz a maximální frekvencí 3,8 GHz. Všechny velikosti virtuálních počítačů řady Mv2-Series můžou využívat trvalé disky úrovně Standard i Premium. Instance Mv2-Series jsou paměťové optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu rozsáhlých databází a úloh s vysokým poměrem paměti, který je ideální pro servery relačních databází, velké mezipaměti a analýzu v paměti.

Funkce virtuálního počítače Mv2-Series Intel® technologie Hyper-Threading

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

Akcelerátor zápisu: [podporováno](./how-to-enable-write-accelerator.md)

|Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> virtuální počítače řady Mv2-Series jsou pouze 2. generace a podporují podmnožinu podporovaných imagí 2. generace. Úplný seznam podporovaných imagí pro Mv2-Series najdete níže. Pokud používáte Linux, přečtěte si téma [Podpora pro virtuální počítače generace 2 v Azure](./linux/generation-2.md) , kde najdete pokyny, jak najít a vybrat obrázek. Pokud používáte Windows, přečtěte si téma [Podpora pro virtuální počítače generace 2 v Azure](./windows/generation-2.md) , kde najdete pokyny, jak najít a vybrat obrázek. 

- Windows Server 2019 nebo novější
- SUSE Linux Enterprise Server 12 SP4 a novější nebo SUSE Linux Enterprise Server 15 SP1 a novější
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 nebo novější 
- Oracle Enterprise Linux 7,7 nebo novější



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.