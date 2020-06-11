---
title: Řada Ddv4 a Ddsv4
description: Specifikace pro virtuální počítače s dv4, Ddv4, Dsv4 a Ddsv4-Series.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 5f241847ee25d3e964d101bd43a8384f764b4f36
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672778"
---
# <a name="ddv4-and-ddsv4-series"></a>Řada Ddv4 a Ddsv4

Ddv4 a Ddsv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a poskytují lepší polohu pro většinu úloh pro obecné účely. Nabízí trvalou veškerou rychlost Turbo, 3,4 GHz, [Intel Turbo Production &reg; Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) a [ &reg; rozšíření Intel Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Podporují taky [ &reg; zvýšení úrovně Intel s hloubkovým učením](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Tyto nové velikosti virtuálních počítačů budou mít 50% větší místní úložiště a také lepší počet vstupně-výstupních operací pro čtení i zápis ve srovnání s [Dv3/Dsv3mi](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) [virtuálními počítači Gen2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

Mezi případy použití řady D-Series patří aplikace na podnikové úrovni, relační databáze, ukládání do mezipaměti v paměti a analýzy.

## <a name="ddv4-series"></a>Ddv4-Series

Velikosti řady Ddv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Ddv4-Series nabízí kombinaci vCPU, paměti a dočasného disku pro většinu produkčních úloh.

Nové velikosti virtuálních počítačů Ddv4 zahrnují rychlé, větší místní úložiště SSD (až 2 400 GiB) a jsou navržené pro aplikace, které využívají nízkou latenci, vysokorychlostní místní úložiště, jako jsou třeba aplikace, které vyžadují rychlé čtení a zápisy do dočasného úložiště nebo které vyžadují dočasné úložiště pro ukládání do mezipaměti nebo dočasné soubory. K virtuálním počítačům s Ddv4 můžete připojit standardní úložiště HDD a standardní SSD. Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů.

ACU: 195-210

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Ddsv4-Series

Ddsv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Ddsv4-Series nabízí kombinaci vCPU, paměti a dočasného disku pro většinu produkčních úloh.

Nové velikosti virtuálních počítačů Ddsv4 zahrnují rychlé, větší místní úložiště SSD (až 2 400 GiB) a jsou navržené pro aplikace, které využívají nízkou latenci, vysokorychlostní místní úložiště, jako jsou třeba aplikace, které vyžadují rychlé čtení a zápisy do dočasného úložiště nebo které vyžadují dočasné úložiště pro ukládání do mezipaměti nebo dočasné soubory. 

 > [!NOTE]
 >Měřiče cen a účtování pro velikosti Ddsv4 jsou stejné jako pro Ddv4-Series.

ACU: 195-210

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
