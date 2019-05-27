---
title: Použití dynamické SQL ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro používání dynamické SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861821"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamické SQL ve službě SQL Data Warehouse
Tipy pro používání dynamické SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="dynamic-sql-example"></a>Příklad dynamické SQL

Při vývoji kódu aplikace pro službu SQL Data Warehouse, možná budete muset použít dynamické sql pomůžou dodávat flexibilní, obecné a modulární řešení. SQL Data Warehouse typy dat objektů blob v tuto chvíli nepodporuje. Nejsou podporovány typy dat objektů blob může omezit velikost vašeho řetězce, protože datový typ objektu blob může být typy varchar(max) a nvarchar(max). Pokud používáte tyto typy k sestavení dlouhých řetězců v kódu aplikace, musíte k rozdělení kódu do bloků dat a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud řetězec je krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normálním způsobem.

> [!NOTE]
> Příkazy spuštění jako dynamické SQL budou pořád platit všech ověřovacích pravidel TSQL.
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

