---
title: M-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady M-Series.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 79d0386e12b38eb6590b68ff959911ddc0a0ab5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505542"
---
# <a name="m-series"></a>Řada M

Řada M-Series nabízí vysoký počet vCPU (až 128 vCPU) a velké množství paměti (až 3,8 TiB). Je to také ideální pro extrémně velké databáze nebo jiné aplikace, které využívají vysoké počty vCPU a velké množství paměti. Velikosti řady M-Series se podporují na &reg; &reg; procesorech Intel Xeon E7-8890 V3 @ 2.50 GHz a Intel &reg; Xeon &reg; Platinum 8280M (Cascade Lake).

Funkce virtuálního počítače řady M-Series s &reg; technologií Intel Hyper-Threading.

ACU: 160–180

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

Akcelerátor zápisu:  [podporováno](./how-to-enable-write-accelerator.md)

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4|2000  |
| Standard_M16ms                   | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1 792   | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2 048   | 4 096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M128ms <sup>1, 2</sup>   | 128 | 3892   | 4 096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1 792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2 048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |

<sup>1</sup> více než 64 vCPU vyžaduje jeden z těchto podporovaných hostovaných operačních systémech: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7,3 nebo Oracle Linux 7,3 s lis 4.2.1.

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.