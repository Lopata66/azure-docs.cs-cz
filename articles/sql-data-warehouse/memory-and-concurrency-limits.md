---
title: Omezení paměti a souběžnosti ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zobrazení omezení paměti a souběžnosti přidělené pro různé úrovně výkonu a třídy prostředků ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0a92c032027e772020eda0b626a6dc6db024bf57
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595562"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Omezení paměti a souběžnosti pro službu Azure SQL Data Warehouse
Zobrazení omezení paměti a souběžnosti přidělené pro různé úrovně výkonu a třídy prostředků ve službě Azure SQL Data Warehouse. Další informace a použít tyto možnosti pro váš plán úloh správy najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md). 

Aktuálně nejsou k dispozici s využitím SQL Data Warehouse – Gen1 a Gen2 dvou generací. Doporučujeme že využít Gen2 služby SQL Data Warehouse na co nejlepšího výkonu pro vaše úlohy datového skladu. Gen2 zavádí novou mezipaměť disku NVMe SSD, která udržuje nejčastěji používaná data blízko procesorům. Tato operace odebere vzdálené vstupy/výstupy pro největší náročné a náročných úloh. Kromě výkonu Gen2 nabízí nejvyšší úroveň škálování umožňuje škálovat až 30 000 jednotek datového skladu a poskytnutím neomezené sloupcové úložiště. Budeme dál podporovat předchozí generace (Gen1) služby SQL Data Warehouse a zachovat stejné funkce; ale doporučujeme vám [upgradovat na Gen2](upgrade-to-latest-generation.md) nejdříve. 

## <a name="data-warehouse-capacity-settings"></a>Nastavení kapacity datového skladu
Maximální kapacita pro datový sklad na různé úrovně výkonu v následujících tabulkách. Chcete-li změnit úroveň výkonu, [škálování výpočetních - portálu](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 poskytuje 2,5 × více paměti na dotazu než Gen1. Tuto paměť navíc pomáhá Gen2 jeho rychlý výkon.  Úrovně výkonu na Gen2 oblast z DW100c DW30000c. 

| Úroveň výkonu | Výpočetní uzly | Distribuce podle počtu výpočetních uzlů | Paměť na datový sklad (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Maximální DWU Gen2 je DW30000c, který má 60 výpočetních uzlů a k jednomu distribučnímu na výpočetním uzlu. Například datový sklad 600 TB na DW30000c zpracovává přibližně 10 TB na výpočetním uzlu.

### <a name="gen1"></a>Gen1

Úrovně služeb pro Gen1 v rozmezí od DW100 až DW6000. 

| Úroveň výkonu | Výpočetní uzly | Distribuce podle počtu výpočetních uzlů | Paměť na datový sklad (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Maximální hodnoty souběžnosti
Zajistit, že každý dotaz nemá dostatek prostředků k úspěšnému provedení SQL datový sklad sleduje využití prostředků přiřazením slotů souběžnosti každý dotaz. Systém zařadí do fronty na základě důležitosti a slotů souběžnosti dotazy. Dotazy čekat ve frontě, dokud nejsou k dispozici dostatek slotů souběžnosti. [Důležitost](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) a slotů souběžnosti určit priority procesoru. Další informace najdete v tématu [analýza úloh](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Statických tříd prostředků**

Následující tabulka uvádí maximální počet souběžných dotazů a slotů souběžnosti pro každou [statický prostředek třídy](resource-classes-for-workload-management.md).  

| Úroveň služby | Maximální počet souběžných dotazů | Dostupné sloty souběžnosti | Sloty používané staticrc10 | Sloty používané staticrc20 | Sloty používané staticrc30 | Sloty používané staticrc40 | Sloty používané staticrc50 | Sloty používané staticrc60 | Sloty používané staticrc70 | Sloty používané staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamický prostředek třídy**

> [!NOTE]
> Třída prostředků smallrc na Gen2 paměti dynamicky přidá zvyšuje úroveň služby a podporuje pouze maximální 32 souběžných dotazů na DW1000c a 4 a DW100c.  Jakmile je instance škálování nad rámec DW1500c slotů souběžnosti a paměť používanou smallrc se zvyšuje s rostoucím zvýšení úrovně služby. 
>
>

Následující tabulka uvádí maximální počet souběžných dotazů a slotů souběžnosti pro každou [dynamickou třídu prostředků](resource-classes-for-workload-management.md). Na rozdíl od Gen1 jsou skutečně dynamické dynamický prostředek třídy na Gen2.  Gen2 používá 3-10-22-70 procento přidělení paměti pro třídy prostředků (krátkodobé používání) – střední velké xlarge ve všech úrovní služeb.

| Úroveň služby | Maximální počet souběžných dotazů | Dostupné sloty souběžnosti | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Gen1

Statických tříd prostředků

Následující tabulka uvádí maximální počet souběžných dotazů a slotů souběžnosti pro každou [statický prostředek třídy](resource-classes-for-workload-management.md) na **Gen1**.

| Úrovně služeb | Maximální počet souběžných dotazů | Sloty souběžnosti maximální | Sloty používané staticrc10 | Sloty používané staticrc20 | Sloty používané staticrc30 | Sloty používané staticrc40 | Sloty používané staticrc50 | Sloty používané staticrc60 | Sloty používané staticrc70 | Sloty používané staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Dynamický prostředek třídy
> [!NOTE]
> Třída prostředků smallrc na Gen1 přidělí pevnou velikost paměti na dotazu, podobně jako v podobě staticrc10 třídy statických prostředků.  Protože smallrc je statická, má schopnost škálovat na 128 souběžných dotazů. 
>
>

Následující tabulka uvádí maximální počet souběžných dotazů a slotů souběžnosti pro každou [dynamickou třídu prostředků](resource-classes-for-workload-management.md) na **Gen1**.

| Úrovně služeb | Maximální počet souběžných dotazů | Dostupné sloty souběžnosti | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Když je splněna jedna z těchto prahových hodnot, nové dotazy jsou ve frontě a spuštěn na základě FIFO first-in.  Dotazy dokončení a počtu dotazů a slotů klesnou pod mezní hodnoty, SQL Data Warehouse verze ve frontě dotazů. 

## <a name="next-steps"></a>Další postup

Další informace o tom, jak využít třídy prostředků pro optimalizaci vašich úloh další prosím najdete v následujících článcích:
* [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md)
* [Analýza úloh](analyze-your-workload.md)

