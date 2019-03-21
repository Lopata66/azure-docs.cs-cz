---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: e05281b2279f5d40f8a3ba4ed3f49a38e5abf0ee
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261448"
---
Úložiště optimalizované velikosti virtuálních počítačů nabízejí Vysoká propustnost disku a vstupně-výstupní operace a jsou ideální pro velké objemy dat, SQL, NoSQL databáze, datových skladů a velké transakční databáze.  Mezi příklady patří Cassandra, MongoDB, Cloudera a Redis. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i místní úložiště propustnost a šířku pásma sítě pro jednotlivé optimalizované velikosti.

Lsv2 řady funkcí vysokou propustnost, nízká latence, namapuje přímo místní úložiště NVMe systémem [AMD EPYC &trade; 7551 procesoru](https://www.amd.com/en/products/epyc-7000-series) s všechny boost core 2.55 GHz a maximální nárůst 3.0 GHz. Virtuální počítače řady Lsv2 se dodávají ve velikostech od 8 do 80 virtuálních procesorů s konfigurací souběžného multithreadingu.  K dispozici je 8 GiB paměti na virtuální procesor a jedno 1,92TB zařízení NVMe SSD M.2 na 8 virtuálních procesorů. Pro L80s v2 je k dispozici 19,2 TB (10 × 1,92 TB).

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.

> [!NOTE]
> Virtuální počítače Lsv2-series jsou optimalizované pro použití místního disku v uzlu připojené přímo k virtuálnímu počítači a nepoužívat trvalých datových disků.  Díky tomu větší IOPs a propustnost pro vaše úlohy.  Lsv2 a řada Ls-series nepodporují vytváření místní mezipaměti a zvyšuje dosažitelné vstupně-výstupních operací trvalých datových disků. Vysoká propustnost a IOPS místního disku díky Lsv2 a virtuální počítače řady Ls-series ideální pro úložišť typu NoSQL, jako je například Apache Cassandra a MongoDB, které replikujte data napříč několika virtuálních počítačů k přetrvávání v případě selhání jednoho virtuálního počítače. 

## <a name="lsv2-series"></a>Řada Lsv2
ACU: 150-175

Premium Storage: Podporováno

Premium Storage ukládání do mezipaměti: Nepodporuje se

| Velikost          | Virtuální procesory | Paměť (GiB) | Dočasný disk<sup>1</sup> (GiB) | Disky NVMe<sup>2</sup> | Propustnost disku NVMe<sup>3</sup> (čtení IOPS / MB/s) | Maximální propustnost disku data (IOPs nebo MB/s) bez mezipaměti<sup>4</sup> | Maximální počet datových disků | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400,000 / 2,000 | 8,000/160 | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800,000 / 4,000 | 16,000/320 | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1,5 MILIÓNU / 8 000 JEDNOTEK    | 32,000/640 | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9 M / 16 000   | 64,000/1,280 | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.8 M / 20 000   | 80,000/1,400 | 32 | 8 / 32,000 |
 
<sup>1</sup> virtuální počítače řady Lsv2 mají standardní disk počítačových prostředků na základě temp SCSI pro použití souborů stránkování/odkládacího souboru operačního systému (D: ve Windows, /dev/sdb v Linuxu). Tento disk obsahuje 80 GB úložiště, 4 000 vstupně-výstupních operací a 80 MB/s přenosová rychlost pro každých 8 virtuálních procesorů (třeba Standard_L80s_v2 poskytuje 800 GB na 40 000 vstupně-výstupních operací a 800 MB/s). Tím se zajistí, že jednotky NVMe může plně vyhrazený pro použití aplikace. Tento disk je dočasný a ztratí se všechna data na Zastavit/uvolnit.

<sup>2</sup> místní disky NVMe jsou dočasné, data budou ztracena na těchto discích, pokud můžete zastavit/zrušit přidělení virtuálního počítače.

<sup>3</sup> technologie Hyper-V NVMe přímé poskytuje neomezený přístup k místní jednotky NVMe bezpečně mapovat do prostoru hostovaného virtuálního počítače.  Dosažení maximálního výkonu vyžaduje použití nejnovější WS2019 nebo Ubuntu 18.04 nebo 16.04 z Azure Marketplace.  Rychlost zápisu se liší v závislosti na velikosti vstupně-výstupních operací, jednotky zatížení a využití kapacity.

<sup>4</sup> virtuální počítače řady Lsv2 neposkytují mezipaměti hostitele pro datový disk, jak vůbec nevyužívá Lsv2 úlohy.  Virtuální počítače Lsv2 však zvládne volby disku dočasné disky operačního systému virtuálního počítače Azure (až 30 GB). 



## <a name="ls-series"></a>Řada Ls
ACU: 180-240

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Nepodporuje se
 
| Velikost          | Virtuální procesory | Paměť (GiB) | Dočasné úložiště (GiB) | Max. datových disků | Maximální propustnost dočasného úložiště (IOPS nebo MB/s) | Maximální propustnost disku bez mezipaměti (IOPS nebo MB/s) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5 000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1 388 | 32 | 40,000 / 400 | 10 000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2 807 | 64 | 80,000 / 800 | 20 000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1 000     | 8 / 20,000 | 
 

Maximální propustnost disku s virtuální počítače řady Ls-series může být omezená podle počtu, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Pokud chcete získat nejlepší výkon pro vaše virtuální počítače, byste měli omezit počet datových disků na 2 disky na virtuální procesor.
- **Byl očekáván šířky pásma sítě** je maximální agregovaná [šířka pásma přidělená podle typu virtuálního počítače](../articles/virtual-network/virtual-machine-network-throughput.md) pro všechny síťové karty pro všechny cíle. Horní omezení nejsou garantována, ale slouží jako vodítko pro výběr správného typu virtuálního počítače pro příslušnou aplikaci. Skutečný výkon sítě bude záviset na řadě faktorů, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [Optimalizace propustnosti sítě pro Windows a Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Pro dosažení očekávaného výkonu sítě na Linuxu nebo ve Windows může být nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [Spolehlivé testování propustnosti virtuálního počítače](../articles/virtual-network/virtual-network-bandwidth-testing.md).
