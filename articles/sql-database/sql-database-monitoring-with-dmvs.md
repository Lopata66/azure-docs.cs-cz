---
title: Monitorování výkonu Azure SQL Database pomocí zobrazení dynamické správy
description: Naučte se detekovat a diagnostikovat běžné problémy s výkonem pomocí zobrazení dynamické správy k monitorování Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 12/19/2018
ms.openlocfilehash: c7eed3fc8e9d0328a3e793e1ff4b3652ab86e2bc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687750"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorování Azure SQL Database výkonu pomocí zobrazení dynamické správy

Microsoft Azure SQL Database umožňuje podmnožinu zobrazení dynamické správy pro diagnostiku problémů s výkonem, což může být způsobeno blokovanými nebo dlouhotrvajícími dotazy, kritickými body prostředků, špatnými plány dotazů atd. Toto téma poskytuje informace o tom, jak zjistit běžné problémy s výkonem pomocí zobrazení dynamické správy.

SQL Database částečně podporuje tři kategorie zobrazení dynamické správy:

- Zobrazení dynamické správy související s databází.
- Zobrazení dynamické správy související s prováděním
- Zobrazení dynamické správy související s transakcemi.

Podrobné informace o zobrazeních dynamické správy najdete v tématu [dynamické zobrazení a funkce pro správu (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) v SQL Server Books Online. 

## <a name="permissions"></a>Oprávnění

Dotaz na zobrazení dynamické správy v SQL Database vyžaduje oprávnění **Zobrazit stav databáze** . Oprávnění **Zobrazit stav databáze** vrací informace o všech objektech v aktuální databázi.
Chcete-li udělit oprávnění k **zobrazení stavu databáze** konkrétnímu uživateli databáze, spusťte následující dotaz:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

V instanci místních SQL Server vrací zobrazení dynamické správy informace o stavu serveru. V SQL Database vrátí pouze informace týkající se aktuální logické databáze.

## <a name="identify-cpu-performance-issues"></a>Identifikace problémů s výkonem procesoru

Pokud je spotřeba procesoru nad 80% po delší dobu, zvažte následující postup řešení potíží:

### <a name="the-cpu-issue-is-occurring-now"></a>Dojde k problému s CPU.

Pokud k problému dochází nyní, existují dva možné scénáře:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Mnoho jednotlivých dotazů, které kumulativně využívají vysoký procesor

K určení horních hodnot hash dotazů použijte následující dotaz:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Dlouho běžící dotazy, které využívají PROCESORy, jsou pořád spuštěné.

K identifikaci těchto dotazů použijte následující dotaz:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>V minulosti došlo k problému s PROCESORem.

Pokud k problému došlo v minulosti a chcete provést analýzu hlavní příčiny, použijte [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Uživatelé s přístupem k databázi můžou použít T-SQL k dotazování na data úložiště dotazů.  Výchozí konfigurace úložiště dotazů používají členitost 1 hodina.  Pomocí následujícího dotazu si můžete prohlédnout aktivity s vysokými nároky na procesor. Tento dotaz vrátí prvních 15 náročných dotazů na procesor.  Nezapomeňte změnit `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Jakmile identifikujete problematické dotazy, je čas optimalizovat tyto dotazy a snížit tak využití procesoru.  Pokud nemáte čas na ladění dotazů, můžete se také rozhodnout pro upgrade objektu SLO databáze, aby tento problém obejít.

## <a name="identify-io-performance-issues"></a>Identifikace potíží s výkonem v/v

Při identifikaci potíží s výkonem v/v jsou hlavní typy čekání spojené s vstupně-výstupními problémy:

- `PAGEIOLATCH_*`

  Pro vstupně-výstupní operace s datovými soubory (včetně `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX``PAGEIOLATCH_UP`).  Pokud má název typu čekání v **/** v, odkazuje na vstupně-výstupní problém. Pokud v poli pro čekání na stránku neexistují žádné **vstupně-výstupní operace** , odkazuje na jiný typ problému (například obsah databáze tempdb).

- `WRITE_LOG`

  V případě problémů s protokolem transakce v/v.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Pokud k problému v/v dojde hned teď

K zobrazení `wait_type` a `wait_time`použijte [Sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nebo [Sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) .

#### <a name="identify-data-and-log-io-usage"></a>Identifikace dat a využití v/v protokolu

Pomocí následujícího dotazu Identifikujte data a použití v/v protokolu. Pokud jsou data nebo vstupně-výstupní operace nad 80%, znamená to, že uživatelé používali dostupnou vstupně-výstupní operaci pro vrstvu služby SQL DB.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Pokud bylo dosaženo limitu v/v, máte dvě možnosti:

- Možnost 1: upgrade výpočetní velikosti nebo úrovně služby
- Možnost 2: identifikujte a optimalizujte dotazy náročné na v/v.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Zobrazení vstupně-výstupních operací souvisejících s vyrovnávací pamětí pomocí úložiště dotazů

V případě možnosti 2 můžete použít následující dotaz na úložiště dotazů na v/v pro zobrazení posledních dvou hodin sledovaných aktivit:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Zobrazit celkový protokol v/v pro WRITELOG čekání

Pokud je typ čekání `WRITELOG`, použijte následující dotaz k zobrazení celkového protokolu v/v – příkaz:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identifikace problémů s výkonem `tempdb`

Při identifikaci problémů s výkonem v/v jsou v horních typech čekání spojených s `tempdb` problémy `PAGELATCH_*` (ne `PAGEIOLATCH_*`). `PAGELATCH_*` čekání ale vždy znamená, že máte `tempdb` spor.  Tato čekací hodnota také může znamenat, že máte k dispozici obsah stránky dat od uživatele, protože souběžné požadavky cílí na stejnou datovou stránku. K další kontrole `tempdb` kolizí použijte [Sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a potvrďte tak, že hodnota wait_resource začíná na `2:x:y` kde 2 je `tempdb` ID databáze, `x` je ID souboru a `y` je ID stránky.  

Pro kolize databáze tempdb je běžnou metodou snížit nebo znovu napsat kód aplikace, který závisí na `tempdb`.  Mezi běžné `tempdb` oblasti využití patří:

- Dočasné tabulky
- Proměnné tabulky
- Parametry s hodnotou tabulky
- Využití úložiště verzí (konkrétně spojené s dlouhými běžícími transakcemi)
- Dotazy, které mají plány dotazů, které používají řazení, spojení s algoritmem hash a zařazování

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Nejčastější dotazy, které používají proměnné tabulky a dočasné tabulky

K identifikaci horních dotazů, které používají proměnné tabulky a dočasné tabulky, použijte následující dotaz:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identifikujte dlouho běžící transakce.

K identifikaci dlouhotrvajících transakcí použijte následující dotaz. Dlouho běžící transakce zabraňují čištění úložiště verzí.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifikace problémů s výkonem při čekání na přidělení paměti

Pokud je váš typ čekání na maximum `RESOURCE_SEMAHPORE` a nemáte problém s vysokým využitím procesoru, může se stát, že máte k dispozici čekající problémy s přidělením paměti.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Určení, zda `RESOURCE_SEMAHPORE` čeká na hlavní čekání

Pomocí následujícího dotazu určete, jestli `RESOURCE_SEMAHPORE` čekat na hlavní čekání.

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Vysoce náročné příkazy paměti pro identitu

Pomocí následujícího dotazu Identifikujte vysoce náročné příkazy paměti:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifikujte prvních 10 grantů aktivních paměti

Pomocí následujícího dotazu Identifikujte prvních 10 přidaných grantů paměti:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Výpočet velikosti databáze a objektů

Následující dotaz vrátí velikost databáze (v megabajtech):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Následující dotaz vrátí velikost jednotlivých objektů (v megabajtech) ve vaší databázi:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorování připojení

Zobrazení [Sys. DM _exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) můžete použít k načtení informací o připojeních navázaných na konkrétní Azure SQL Database Server a podrobnostech každého připojení. Zobrazení [Sys. DM _exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) je navíc užitečné při načítání informací o všech aktivních připojeních uživatelů a vnitřních úlohách.
Následující dotaz načte informace o aktuálním připojení:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Při provádění zobrazení **Sys. DM _exec_requests** a **Sys. DM _exec_sessions**, pokud máte v databázi oprávnění **Zobrazit stav databáze** , zobrazí se všechny spuštěné relace v databázi. v opačném případě se zobrazí pouze aktuální relace.

## <a name="monitor-resource-use"></a>Monitorování využití prostředků

Využití prostředků můžete monitorovat pomocí [SQL Database Query Performance Insight](sql-database-query-performance.md) a [úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx).

Můžete také monitorovat využití pomocí těchto dvou zobrazení:

- [sys. DM _db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys. DM _db_resource_stats

V každé databázi SQL Database můžete použít zobrazení [Sys. DM _db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) . Zobrazení **Sys. DM _db_resource_stats** ukazuje poslední data o využití prostředků vzhledem k úrovni služby. Průměrné procentuální hodnoty pro procesor, data v/v, zápisy protokolů a paměť se zaznamenávají každých 15 sekund a uchovávají se po dobu 1 hodiny.

Vzhledem k tomu, že toto zobrazení poskytuje podrobnější pohled na používání prostředků, použijte nejprve **Sys. DM _db_resource_stats** pro všechny analýzy stavu nebo řešení potíží. Tento dotaz například ukazuje průměrné a maximální využití prostředků aktuální databáze za poslední hodinu:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Další dotazy najdete v příkladech v [tabulce sys. DM _db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresource_stats"></a>sys. resource_stats

Zobrazení [Sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) v **hlavní** databázi obsahuje další informace, které vám pomůžou monitorovat výkon SQL Database na konkrétní úrovni služby a výpočetní velikosti. Data se shromažďují každých 5 minut a uchovávají se po dobu přibližně 14 dnů. Toto zobrazení je užitečné pro dlouhodobé historické analýzy způsobu, jakým vaše databáze SQL používá prostředky.

Následující graf ukazuje využití prostředků procesoru pro databázi Premium s výpočetní velikostí P2 pro každou hodinu v týdnu. Tento graf začíná v pondělí, zobrazuje 5 pracovních dnů a pak zobrazuje víkend, pokud je aplikace mnohem méně nastává.

![Využití prostředků databáze SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z dat má tato databáze v současné době zatížení CPU ve špičce přesně přes 50% využití procesoru vzhledem k výpočetní velikosti P2 (poledne v úterý). Pokud je CPU dominantní faktor v profilu prostředku aplikace, můžete se rozhodnout, že P2 je správná výpočetní velikost, aby bylo zaručeno, že se zatížení bude vždy hodit. Pokud očekáváte, že aplikace bude v průběhu času růst, je vhodné mít dodatečnou vyrovnávací paměť prostředků, aby aplikace nikdy nedosáhla limitu úrovně výkonu. Pokud zvětšíte výpočetní velikost, můžete se vyhnout chybám zobrazeným zákazníkovi, ke kterým může dojít, když databáze nemá dostatek energie na zpracování požadavků, zejména v prostředích, která jsou citlivá na latenci. Příkladem je databáze, která podporuje aplikaci, která vykreslí webové stránky na základě výsledků volání databáze.

Jiné typy aplikací mohou interpretovat stejný graf odlišně. Například pokud se aplikace pokusí zpracovat data mzdy každý den a má stejný graf, tento druh modelu "batch job" může na výpočetní úrovni P1 dělat přesně. Výpočetní velikost P1 má 100 DTU ve srovnání s 200 DTU na výpočetní úrovni P2. Výpočetní velikost P1 poskytuje poloviční výkon výpočetní velikosti P2. Takže 50% využití procesoru v P2 se rovná 100% využití CPU v P1. Pokud aplikace nemá vypršení časových limitů, může to být bez ohledu na to, jestli úloha trvá 2 hodiny nebo 2,5 hodin, pokud se k tomu ještě nejedná. Aplikace v této kategorii pravděpodobně může používat výpočetní velikost P1. Můžete využít výhod faktu, že během dne jsou k dispozici časová období, kdy je využití prostředků nižší, aby se v jednom z nich později v daném dni mohly přebírat jakékoli "velké špičky". Výpočetní velikost P1 může být pro tento druh aplikace dobrá (a ušetřit tak peníze), pokud úlohy můžou být dokončeny včas každý den.

Azure SQL Database zveřejňuje spotřebované informace o zdroji pro každou aktivní databázi v zobrazení **Sys. resource_stats** **hlavní** databáze na každém serveru. Data v tabulce jsou agregována do intervalu 5 minut. V případě úrovní služeb Basic, Standard a Premium může tato data trvat více než 5 minut, než se v tabulce zobrazí, takže tato data jsou užitečnější pro historická analýzy, nikoli téměř v reálném čase. Dotaz na zobrazení **Sys. resource_stats** vám umožní zobrazit poslední historii databáze a ověřit, jestli vámi zvolená rezervace dodala požadovaný výkon v případě potřeby.

> [!NOTE]
> K dotazování **Sys. resource_stats** v následujících příkladech musíte být připojeni k **hlavní** databázi serveru SQL Database.

V tomto příkladu se dozvíte, jak jsou data v tomto zobrazení vystavena:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Zobrazení katalogu sys. resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

Následující příklad ukazuje různé způsoby, jak můžete pomocí zobrazení katalogu **Sys. resource_stats** získat informace o tom, jak vaše databáze SQL používá prostředky:

1. Pokud se chcete podívat na využití prostředků v minulém týdnu pro databázi userdb1, můžete spustit tento dotaz:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Aby bylo možné vyhodnotit, jak dobře vaše zatížení vyhovuje výpočetní velikosti, je nutné přejít k podrobnostem o všech aspektech metriky prostředků: CPU, čtení, zápisy, počet pracovních procesů a počet relací. Tady je upravený dotaz pomocí **Sys. resource_stats** , který oznamuje průměrnou a maximální hodnotu těchto metrik prostředků:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Pomocí těchto informací o průměrných a maximálních hodnotách každé metriky prostředků můžete vyhodnotit, jak dobře se vaše zatížení vejde do vybrané výpočetní velikosti. Průměrné hodnoty z **Sys. resource_stats** obvykle poskytují dobrý základ pro použití s cílovou velikostí. Měl by to být vaše primární měření. Například můžete používat standardní úroveň služby s výpočetní velikostí S2. Průměrné procentuální hodnoty použití pro čtení a čtení v CPU a vstupně-výstupních operacích jsou nižší než 40%, průměrný počet pracovních procesů je pod 50 a průměrný počet relací je pod 200. Vaše úlohy se můžou vejít do výpočetní velikosti S1. Můžete snadno zjistit, jestli vaše databáze odpovídá omezením pracovních procesů a relací. Pokud chcete zjistit, jestli databáze zapadá do nižší výpočetní velikosti s ohledem na PROCESORy, čtení a zápisy, vydělte číslo DTU nižší výpočetní velikosti číslem DTU aktuální velikosti a pak výsledek vynásobte 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Výsledkem je relativní rozdíl mezi dvěma výpočetními velikostmi v procentech. Pokud vaše využití prostředků nepřekračuje tuto hodnotu, vaše zatížení se může vejít do nižší výpočetní velikosti. Je ale potřeba se podívat na všechny rozsahy hodnot využití prostředků a určit procento, jak často by se vaše databázové zatížení vešlo do nižší výpočetní velikosti. Následující dotaz vyprodukuje procento přizpůsobení podle dimenze prostředku na základě prahové hodnoty 40%, kterou jsme vypočítali v tomto příkladu:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Na základě vaší úrovně databázové služby se můžete rozhodnout, jestli vaše zatížení zapadá do nižší výpočetní velikosti. Pokud je váš cíl úlohy databáze 99,9 procent a předchozí dotaz vrátí hodnoty větší než 99,9% pro všechny tři dimenze prostředků, vaše zatížení se nejspíš zahodí k nižší výpočetní velikosti.

    V procentech přizpůsobení získáte také informace o tom, zda byste měli přejít na další vyšší výpočetní velikost, aby vyhovovala vašemu cíli. Například userdb1 zobrazuje následující využití CPU za minulý týden:

   | Průměrné procento využití procesoru | Maximální procento využití procesoru |
   | --- | --- |
   | 24,5 |100,00 |

    Průměrná doba využití procesoru je čtvrtina limitu výpočetní velikosti, která by odpovídala i výpočetní velikosti databáze. Ale maximální hodnota udává, že databáze dosáhne limitu výpočetní velikosti. Potřebujete přejít na další vyšší výpočetní velikost? Podívejte se, kolikrát vaše zatížení dosáhlo 100 procent, a pak ho porovnejte s cílem úlohy vaší databáze.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Pokud tento dotaz vrátí hodnotu menší než 99,9 procent pro jakékoli tři dimenze prostředku, zvažte buď přesunutí na další vyšší výpočetní velikost, nebo použijte techniky pro vyladění aplikace, abyste snížili zatížení SQL Database.

4. Toto cvičení také zvažuje předpokládané zvýšení zatížení v budoucnu.

U elastických fondů můžete monitorovat jednotlivé databáze ve fondu pomocí technik popsaných v této části. Můžete ale také monitorovat fond jako celek. Další informace najdete v tématu [Monitorování a správa elastického fondu](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Maximální počet souběžných žádostí

Pokud chcete zobrazit počet souběžných požadavků, spusťte tento dotaz Transact-SQL ve vaší databázi SQL:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Pokud chcete analyzovat úlohy místní databáze SQL Server, upravte tento dotaz tak, aby se vyfiltroval konkrétní databáze, kterou chcete analyzovat. Například pokud máte místní databázi s názvem MyDatabase, tento dotaz Transact-SQL vrátí počet souběžných žádostí v této databázi:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Toto je pouze snímek v jednom bodě v čase. Abyste lépe pochopili vaše úlohy a požadavky na souběžné požadavky, budete potřebovat shromáždit spoustu ukázek v průběhu času.

### <a name="maximum-concurrent-logins"></a>Maximální počet souběžných přihlášení

Můžete analyzovat vzorce uživatelů a aplikací a získat představu o četnosti přihlášení. V testovacím prostředí můžete také spustit reálné zátěže, abyste se ujistili, že nebudete mít na dosahu žádného nebo jiného omezení, které v tomto článku probereme. Neexistuje žádný dotaz nebo zobrazení dynamické správy (DMV), které vám umožní zobrazit počet souběžných přihlášení nebo historii.

Pokud více klientů používá stejný připojovací řetězec, služba ověřuje každé přihlášení. Pokud se 10 uživatelů současně připojí k databázi pomocí stejného uživatelského jména a hesla, bude k dispozici 10 souběžných přihlášení. Toto omezení se vztahuje pouze na dobu trvání přihlášení a ověření. Pokud se jeden z deseti uživatelů připojí k databázi postupně, počet souběžných přihlášení nikdy nebude větší než 1.

> [!NOTE]
> V současné době toto omezení neplatí pro databáze v elastických fondech.

### <a name="maximum-sessions"></a>Maximální počet relací

Pokud chcete zobrazit počet aktuálních aktivních relací, spusťte tento dotaz Transact-SQL ve vaší databázi SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Pokud analyzujete místní úlohu SQL Server, upravte dotaz tak, aby se zazaměřil na konkrétní databázi. Tento dotaz vám pomůže určit možné potřeby relace pro databázi, Pokud zvažujete jejich přesun do Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Tyto dotazy znovu vrátí počet bodů v čase. Pokud shromáždíte více ukázek v průběhu času, budete mít k dispozici nejvhodnější informace o použití vaší relace.

V případě analýzy SQL Database můžete získat historické statistiky o relacích dotazem na zobrazení [Sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) a kontrolou sloupce **active_session_count** .

## <a name="monitoring-query-performance"></a>Monitorování výkonu dotazů

Pomalé nebo dlouho běžící dotazy můžou využívat významné systémové prostředky. V této části se dozvíte, jak pomocí zobrazení dynamické správy detekovat několik běžných problémů s výkonem dotazů. Starší, ale i stále užitečné informace pro řešení potíží, jsou [problémy s výkonem v SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) článku na webu Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hledání hlavních N dotazů

Následující příklad vrátí informace o pěti nejčastějších dotazech seřazené podle průměrného času procesoru. V tomto příkladu jsou shrnuty dotazy podle jejich hodnoty hash dotazů, takže logicky ekvivalentní dotazy jsou seskupeny podle jejich kumulativní spotřeby prostředků.

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>Monitorování blokovaných dotazů

Pomalé nebo dlouho běžící dotazy můžou přispět k nadměrné spotřebě prostředků a být v důsledku blokovaných dotazů. Příčinou blokování může být špatný návrh aplikace, špatné plány dotazů, chybějící užitečné indexy atd. Pomocí zobrazení sys. DM _tran_locks můžete získat informace o aktuální aktivitě uzamykání v Azure SQL Database. Příklad kódu naleznete v tématu [Sys. DM _tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) v SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorování plánů dotazů

Neefektivní plán dotazů taky může zvýšit spotřebu procesoru. Následující příklad používá zobrazení [Sys. DM _exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) k určení, který dotaz používá nejvíc kumulativní procesor.

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>Viz také

[Úvod do SQL Database](sql-database-technical-overview.md)
