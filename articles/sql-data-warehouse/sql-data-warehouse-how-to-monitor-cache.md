---
title: Optimalizace vaší mezipaměti Gen2 | Dokumentace Microsoftu
description: Další informace o monitorování Gen2 mezipaměti pomocí webu Azure portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7b9a4e7a37d1f09bc8ae44d75637aa4f5ad95590
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775097"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorovat Gen2 cache
Architektura úložiště Gen2 automaticky rozprostírá nejčastěji dotazované columnstore segmenty v mezipaměti na NVMe, které jsou založené na SSD určená pro datové sklady Gen2. Vyšší výkon začne vyplácet, když vaše dotazy načítají segmenty, které je umístěný v mezipaměti. Tento článek popisuje, jak monitorovat a řešení potíží s výkonem pomalých dotazů tak, že určíte, jestli vaše úlohy optimální využití mezipaměti Gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí webu Azure portal
Pokud chcete zobrazit metriky mezipaměti Gen2 řešení potíží s výkonem dotazů můžete použít Azure Monitor. Nejprve přejděte na web Azure Portal a klikněte na monitorování:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Klikněte na tlačítko metriky a vyplňte **předplatné**, **prostředků** **skupiny**, **typ prostředku**, a **prostředků název** datového skladu.

Jsou klíčové metriky pro řešení potíží s mezipaměti Gen2 **procenta přístupů do mezipaměti** a **mezipaměti použít procento**. Konfigurace Azure grafu metriky zobrazíte tyto dvě metriky.

![Metriky mezipaměti](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Přístupů do mezipaměti a použít procento
Matice níže popisuje scénáře na základě hodnot metriky mezipaměti:

|                                | **Procento úspěšnosti vysokou mezipaměti** | **Procenta přístupů do mezipaměti s nízkou** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Vysoké procento použitá mezipaměť** |          Scénář 1           |          Scénář 2          |
| **Použita mezipaměť nízké procentuální**  |          Scénář 3           |          Scénář 4          |

**Scénář 1:** Používání mezipaměti optimální. [Řešení potíží s](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) další oblasti, které mohou být zpomalení vaše dotazy.

**Scénář 2:** Aktuální pracovní sady dat nelze umístit do mezipaměti, což způsobí, že s nízkým procento z důvodu fyzických čtení přístupů do mezipaměti. Zvažte možnost škálování nahoru úroveň výkonu a znovu spusťte úlohy k naplnění mezipaměti.

**Scénář 3:** Je pravděpodobné, že váš dotaz běží pomalu z nějakého důvodu nesouvisející do mezipaměti. [Řešení potíží s](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) další oblasti, které mohou být zpomalení vaše dotazy. Můžete taky zvážit možnost [škálování instance](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) snížit velikost mezipaměti pro úsporu nákladů. 

**Scénář 4:** Jste měli studenou mezipaměť, která může být důvod, proč bylo pomalé v sítích vašich dotazů. Vezměte v úvahu opětovné spuštění dotazu jako datovou sadu pracovních by měl nyní v mezipaměti. 

**Důležité: Pokud procento úspěšnosti mezipaměti nebo mezipaměť používaná procento není aktualizaci po opětným spuštěním úlohy, můžete pracovní sada už načtených do paměti. Mějte na paměti pouze Clusterované columnstore, které tabulek jsou uložené v mezipaměti.**

## <a name="next-steps"></a>Další postup
Další informace o ladění výkonu obecný dotaz, naleznete v tématu [monitorování provádění dotazů](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
