---
title: Optimalizace mezipaměti Gen2
description: Naučte se monitorovat mezipaměť Gen2 pomocí Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 257b33802ea95138ef8149ef9302b14ca379e7ac
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195540"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorovat mezipaměť Gen2

Tento článek popisuje, jak monitorovat a řešit potíže s pomalým výkonem dotazů tím, že určíte, jestli vaše zatížení optimálně využívá Gen2 cache.

Architektura úložiště Gen2 automaticky vychází z nejčastěji dotazovaných segmentů columnstore v mezipaměti, která je umístěná v NVMe na SSD, která je určená pro Gen2 datové sklady. Vyšší výkon je realizován, když dotazy načítají segmenty, které jsou umístěny v mezipaměti.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí Azure Portal

Pomocí Azure Monitor můžete zobrazit metriky mezipaměti Gen2 a řešit potíže s výkonem dotazů. Nejprve přejděte na Azure Portal a klikněte na **monitorování**, **metriky** a **Vyberte rozsah**:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-0.png)

K vyhledání datového skladu použijte panely hledání a rozevírací seznam. Pak vyberte použít.

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache-1.png)

Klíčovou metrikou pro řešení potíží s mezipamětí Gen2 je **Procento přístupů do mezipaměti** a **procento využití mezipaměti**. Vyberte **Procento přístupů do mezipaměti** a potom pomocí tlačítka **Přidat metriku** přidejte **procento využité mezipaměti**. 

![Metriky mezipaměti](./media/sql-data-warehouse-cache-portal/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Počet přístupů do mezipaměti a použité procento

Následující matice popisuje scénáře v závislosti na hodnotách metrik mezipaměti:

|                                | **Procento přístupů do vysoké mezipaměti** | **Procento přístupů do mezipaměti v nízkém rozsahu** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procento využité vysoké mezipaměti** |          Scénář 1           |          Scénář 2          |
| **Procento využité mezipaměti nízké úrovně**  |          Scénář 3           |          Scénář 4          |

**Scénář 1:** Používáte svou mezipaměť optimálně. [Řešení potíží s](sql-data-warehouse-manage-monitor.md) ostatními oblastmi, které mohou zpomalovat vaše dotazy.

**Scénář 2:** Vaše aktuální pracovní sada dat se nemůže vejít do mezipaměti, která způsobuje nedostatečné procento přístupů do mezipaměti z důvodu fyzických čtení. Zvažte možnost škálovat úroveň výkonu a znovu spustit úlohu, abyste mohli naplnit mezipaměť.

**Scénář 3:** Je možné, že dotaz běží pomalu z důvodů, které nesouvisí s mezipamětí. [Řešení potíží s](sql-data-warehouse-manage-monitor.md) ostatními oblastmi, které mohou zpomalovat vaše dotazy. Můžete také zvážit horizontální snížení kapacity [instance](sql-data-warehouse-manage-monitor.md) , aby se snížila velikost mezipaměti, aby se ušetřily náklady. 

**Scénář 4:** Měli byste mít studenou mezipaměť, což by mohlo být důvod, proč byl dotaz pomalý. Zkuste dotaz znovu spustit, protože pracovní sada dat by měla být teď v mezipaměti. 

> [!IMPORTANT]
> Pokud procento přístupů do mezipaměti nebo procento využití mezipaměti není po reběhu úlohy aktualizováno, vaše pracovní sada už může být umístěná v paměti. Pouze clusterované tabulky columnstore jsou uloženy v mezipaměti.

## <a name="next-steps"></a>Další kroky
Další informace o obecném ladění výkonu dotazů najdete v tématu [monitorování provádění dotazů](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution).
