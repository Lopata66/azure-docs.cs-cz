---
title: Prostředek založený na DTU databáze SQL Azure omezuje elastických fondů | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné prostředek založený na DTU limity pro elastické fondy Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/14/2019
ms.openlocfilehash: 6a2b3af4240a5c400bd1eaf4fd1e93b09fc702b1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002710"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí nákupní model založený na DTU

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database a použití nákupní model založený na DTU databáze ve fondu.

Založený na DTU nákupní model omezení prostředků pro izolované databáze, najdete v části [omezení prostředků založený na DTU – izolované databáze](sql-database-vcore-resource-limits-elastic-pools.md). Omezení prostředků založený na virtuálních jádrech najdete v tématu [omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastický fond: Velikosti úložiště a výpočty velikostí

Následující tabulky pro elastické fondy SQL Database, zobrazit prostředky dostupné na jednotlivých úrovních služby a vypočítat velikost. Můžete nastavit úroveň služby, výpočetního prostředí a pomocí velikost úložiště [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní příkazového řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), nebo [rozhraní REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Škálování pokyny a důležité informace, najdete v části [škálování elastického fondu](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obvykle stejné jako u izolovaných databází mimo fondů na základě jednotek Dtu a na úrovni služby. Maximální počet souběžných pracovních procesů pro databáze S2 je například 120 pracovních procesů. Ano maximální počet souběžných pracovních procesů pro databáze ve fondu Standard je také 120 pracovních procesů pokud maximální počet jednotek DTU na databázi ve fondu je 50 Dtu (což odpovídá na S2).

### <a name="basic-elastic-pool-limits"></a>Základní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální počet možností úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| OLTP v paměti max. úložiště na fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet databází pro každý fond | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 |
| Možnosti maximální počet Edtu na databázi | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximální velikost úložiště na databázi (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximální počet možností úložiště na fond (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| OLTP v paměti max. úložiště na fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet databází pro každý fond | 100 | 200 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Možnosti maximální počet Edtu na databázi | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximální velikost úložiště na databázi (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Standard (pokračování)

| Počet eDTU na fond | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximální počet možností úložiště na fond (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| OLTP v paměti max. úložiště na fond (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet databází pro každý fond | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Možnosti maximální počet Edtu na databázi | 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Maximální počet možností úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Prémiové limity elastického fondu

| Počet eDTU na fond | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Maximální počet možností úložiště na fond (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| OLTP v paměti max. úložiště na fond (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximální počet databází pro každý fond | 50 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovních procesů na fond (požadavky) | 200 | 400 | 800 | 1600 | 2400 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet eDTU na databázi | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1 000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximální počet eDTU na databázi | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1 000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Premium (pokračování)

| Počet eDTU na fond | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096 |
| Maximální počet možností úložiště na fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096|
| OLTP v paměti max. úložiště na fond (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximální počet databází pro každý fond | 100 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximální počet souběžných relací na fond | 30000 | 30000 | 30000 | 30000 | 30000 |
| Možnosti min Edtu na databázi | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Možnosti maximální počet Edtu na databázi | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, střed USA – Západ, oblastí pro úlohy ministerstva obrany USA a US Government centrální. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB.  Další informace najdete v tématu [aktuálních omezení pro P11 – P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Pokud se využívají všechny DTU elastického fondu, pak každá databáze ve fondu obdrží stejné množství prostředků ke zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Pokud je minimální počet DTU na databázi nastaven na nenulovou hodnotu, pro každou databázi bude garantováno spravedlivé sdílení prostředků elastického fondu je společně s libovolným množstvím prostředků.

> [!NOTE]
> Pro `tempdb` omezení, najdete v článku [omezení databáze tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet eDTU na databázi |Maximální počet eDTU, které může využívat libovolná databáze ve fondu za předpokladu, že jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet eDTU na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet eDTU na databázi dostatečně vysoký, aby databáze zvládly využití ve špičkách. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli. Předpokládejme například, že využití ve špičce na databázi je 20 eDTU a špička v jednu chvíli nastává pouze u 20 % ze 100 databází ve fondu. Pokud je maximální počet eDTU na databázi nastaven na 20 eDTU, je rozumné fond 5krát předimenzovat a nastavit počet eDTU na fond na 400. |
| Minimální počet eDTU na databázi |Minimální počet eDTU garantovaných pro každou databázi ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet eDTU na databázi může být nastaven na 0, což je i výchozí hodnota. Tato vlastnost je nastavena na libovolnou hodnotu mezi 0 a průměrným využitím eDTU na databázi. Násobek počtu databází ve fondu a minimálním počtem eDTU na databázi nemůže překročit počet eDTU na fond. Pokud je například ve fondu 20 databází a minimální počet eDTU na databázi je nastaven na 10 eDTU, pak musí být počet eDTU na fond alespoň 200 eDTU. |
| Max. úložiště na databázi |Maximální velikosti databáze nastavena podle uživatele pro databázi ve fondu. Nicméně databáze ve fondu sdílejí úložiště přidělené fondu. I v případě celková maximální velikost úložiště *na databázi* je nastavena na být větší než celkový úložiště k dispozici *místo fondu*, celkového místa ve skutečnosti používá všechny databáze, nebudou moci být delší než limit fondu k dispozici. Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další postup

- VCore omezení prostředků pro izolovanou databázi, naleznete v tématu [omezení prostředků pro izolované databáze pomocí nákupní model založený na virtuálních jádrech](sql-database-vcore-resource-limits-single-databases.md)
- Omezení prostředků DTU pro izolovanou databázi, naleznete v tématu [omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU](sql-database-dtu-resource-limits-single-databases.md)
- VCore omezení prostředků pro elastické fondy najdete v tématu [limity pro elastické fondy pomocí nákupní model založený na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance najdete v tématu [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru najdete v tématu [přehled omezení prostředků na serveru služby SQL Database](sql-database-resource-limits-database-server.md) informace o omezeních na úrovni serveru a předplatné.