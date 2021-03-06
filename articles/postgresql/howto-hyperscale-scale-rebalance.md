---
title: Vyrovnávání zatížení horizontálních oddílů-– škálovatelné (Citus) – Azure Database for PostgreSQL
description: Pro lepší výkon distribuujte horizontálních oddílů rovnoměrně mezi servery.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026382"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Změna rovnováhy horizontálních oddílů ve skupině serverů Citus ()

Aby bylo možné využít nově přidaných uzlů, je nutné znovu vyrovnávat distribuovanou tabulku [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých horizontálních oddílů z existujících uzlů do nových. Nejdřív ověřte, že nové pracovní procesy úspěšně dokončily zřizování. Pak spusťte nástroj horizontálních oddílů pro vyrovnávání zatížení tak, že se připojíte k uzlu koordinátora clusteru s psql a spuštěným:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkce [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) znovu vyrovnává všechny tabulky ve skupině [kolocation](concepts-hyperscale-colocation.md) v tabulce s názvem v argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

**Další kroky**


- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
- Horizontální navýšení nebo navýšení [kapacity skupiny serverů](howto-hyperscale-scale-grow.md)
- Zobrazit referenční materiál [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
