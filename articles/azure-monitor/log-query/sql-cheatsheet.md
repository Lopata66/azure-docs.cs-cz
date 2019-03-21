---
title: SQL tak, aby tahák pro dotazy protokolů Azure Monitor | Dokumentace Microsoftu
description: Nápověda pro uživatele znáte SQL při psaní dotazů na protokoly ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: a0233774deaffe25a8e59f79511a0031b1535ba4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010995"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL pro monitorování Azure tahák pro dotazy log 

Následující tabulka pomáhá uživatelům, kteří znají SQL učit jazyk dotaz Kusto psaní dotazů protokolu ve službě Azure Monitor. Podíváme se na příkaz T-SQL pro řešení běžných scénářů a ekvivalent v dotazu protokolu Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL Azure monitor

Popis                             |Příkaz jazyka SQL                                                                                          |Azure Monitor dotaz protokolu
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Vyberte všechna data z tabulky            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Vyberte konkrétní sloupce z tabulky    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Vyberte 100 záznamů z tabulky         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Null hodnocení                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Porovnání řetězců: rovnosti             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Porovnání řetězců: dílčí řetězec            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Porovnání řetězců: zástupných znaků             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Datum, porovnání: poslední 1 den             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Datum, porovnání: rozsah kalendářních dat             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Logická porovnání                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Seřadit                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
DISTINCT                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Seskupení, agregace                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Názvy sloupců, rozšíření                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Prvních n záznamů míru                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Sjednocení                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Sjednocení: s podmínkami                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Spojit                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Další postup

- Projděte si lekci [psaní dotazů protokolu ve službě Azure Monitor](get-started-queries.md).
