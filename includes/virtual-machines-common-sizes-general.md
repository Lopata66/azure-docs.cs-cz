---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 2864946af6bd9ed2a271ef35d3afb385bfa9a71d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815459"
---
Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr procesorů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i propustnost úložiště pro velikosti v této skupině. 

- [DC řad](#dc-series) je nová řada virtuálních počítačů v Azure, která můžou pomoct chránit důvěrnost a integrita dat a kódu při zpracovávání ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2176G s frekvencí 3,7 GHz a technologií SGX. S technologií Intel Turbo Boost Technology mohou tyto počítače dosáhnout frekvence až 4,7 GHz. Instance řady DC umožňují zákazníkům sestavovat bezpečné aplikace založené na enklávě, aby se při jejich použití chránil kód a data.

- Virtuální počítače řady Av2-series je možné nasadit na celou řadu typů hardwaru a procesorů. Díky konfiguraci paměti a výkonu procesoru jsou virtuální počítače řady A-Series nejvhodnější pro úlohy základní úrovně, jako je vývoj a testování. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.

  Vzorové případy použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé a střední databáze, testování konceptů, úložiště kódu.

- Řada Dv2-series, nástupce původní D-series, nabízí výkonnější procesor a optimální konfigurace procesoru k paměti, díky kterým jsou vhodné pro většinu produkčních úloh. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založen na nejnovější generace Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz nebo E5-2673 v4 (Broadwell) 2,3 GHz procesorech a s technologií Intel Turbo Boost Technology 2.0 můžete až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- Dv3-series, funkcí procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo nejnovější procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesoru v konfiguraci typu hyper, poskytuje lepší návrh hodnoty pro většinu obecných úloh účel.  Paměti došlo k rozbalení (z ~3.5 GiB/virtuální procesor do 4 GB a vCPU) zatímco disku a sítě limity byly upraveny na základě vázané na jádro, aby bylo v souladu s přechodem na hyperthreadingem.  Dv3 už má vysoký poměr paměti velikosti virtuálních počítačů řady D/Dv2, ty se přesunuly na novou řadu Ev3.

  Vzorové případy použití řady D-series zahrnují podnikových aplikací, relačních databází, ukládání do mezipaměti a analýzu. 
  
## <a name="b-series"></a>B-Series

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Nepodporuje se

Zvládáním výkonových špiček virtuální počítače řady B-series jsou ideální pro úlohy, které nejsou nutné plný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývoj a testovací prostředí. Tyto úlohy mají obvykle požadavky na výkon zvládáním výkonových špiček. B-Series poskytuje tyto zákazníkům možnost zakoupení velikost virtuálního počítače s výkonem při provádění základní ceny, umožňující instance virtuálního počítače Vybudujte kredity, když virtuální počítač využívá míň než jeho základní výkon. Když virtuální počítač se nashromáždil kredit, můžete převést virtuální počítač nad základní Virtuálního počítače pomocí až 100 % procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

Vzorové případy použití zahrnují servery pro vývoj a testování, servery web s nízkým provozem, malé databáze, mikroslužby, servery pro testování konceptů, buildovací servery.


| Velikost             | Virtuální procesory  | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkonu procesoru virtuálního počítače | Maximální počet výkonu procesoru virtuálního počítače | Počáteční kredit | Kredity bankovních / hodina | Maximální počet bankovních kredity | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40 %                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90 %                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls je podporována pouze v Linuxu

## <a name="dsv3-series-sup1sup"></a>Řada Dsv3-series <sup>1</sup>

ACU: 160-190

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

Velikosti Dsv3-series jsou založené na procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo nejnovější procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesor, který může s technologií Intel Turbo Boost 2.0 dosáhnout frekvence až 3,5 GHz a používají službu premium storage. Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4 000 / 32 (50)                                                       | 3 200 / 48                                | 2 / 1,000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8 000 / 64 (100)                                                      | 6 400 / 96                                | 2 / 2,000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80 000 / 1 200                              | 8 / 30,000                                               |

<sup>1</sup> Dsv3-series Virtuálních počítačů procesorech Intel® Hyper-Threading technologie

## <a name="dv3-series-sup1sup"></a>Řada Dv3-series <sup>1</sup>

ACU: 160-190

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

Velikosti Dv3-series jsou založené na procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesor, které můžete dosáhnout s technologií Intel Turbo Boost 2.0 dosáhnout frekvence až 3,5 GHz. Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1,000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2,000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4,000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8,000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16,000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96 000 / 1 000 / 500                                            | 8 / 30,000                             |

<sup>1</sup> virtuálních počítačů Dv3-series vybavené technologií Intel® Hyper-Threading

## <a name="dsv2-series"></a>DSv2-series

ACU: 210-250

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64 000 / 512 (688) |51 200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-series

ACU: 210-250

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost           | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. datových disků | Propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4×500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8×500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16×500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32×500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64 x 500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-series

ACU: 100

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |

## <a name="dc-series"></a>Řada DC

Premium Storage: Podporováno

Premium Storage ukládání do mezipaměti: Podporováno



| Velikost          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |







