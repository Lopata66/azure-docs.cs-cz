---
title: Dotazy horizontálně dělené databáze SQL Azure
description: Spouštějte dotazy napříč horizontálních oddílů pomocí klientské knihovny elastické databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 108da61323f61b009fbfdedac4cd345c6b87a7be
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690180"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Dotazování na více horizontálních oddílů pomocí nástrojů elastické databáze

## <a name="overview"></a>Přehled

Pomocí [nástrojů pro elastic Database](sql-database-elastic-scale-introduction.md)můžete vytvářet databázová řešení horizontálně dělené. **Dotazování na více horizontálních oddílů** se používá pro úlohy, jako je shromažďování dat nebo vytváření sestav, které vyžadují spuštění dotazu, který se roztáhne mezi několik horizontálních oddílů. (To je na rozdíl od na [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md), které provádí veškerou práci na jednom horizontálních oddílů.)

1. Získejte **RangeShardMap** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) nebo **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) pomocí **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [ .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) nebo metoda **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Viz [Vytvoření ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) a [získání RangeShardMap nebo ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Vytvořte objekt **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Vytvořte **MultiShardStatement nebo MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Nastavte **vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) na příkaz T-SQL.
5. Spusťte příkaz voláním metody **ExecuteQueryAsync nebo ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Zobrazte výsledky pomocí třídy **MultiShardResultSet nebo MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Příklad

Následující kód ilustruje použití horizontálních oddílůho dotazování pomocí daného **ShardMap** s názvem *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Klíčovým rozdílem je konstrukce připojení s více horizontálních oddílů. Kde **SqlConnection** funguje v individuální databázi, **MultiShardConnection** jako vstup převezme ***kolekci horizontálních oddílů*** . Naplní kolekci horizontálních oddílů z mapy horizontálních oddílů. Dotaz se pak spustí na kolekci horizontálních oddílů s využitím všech sémantik pro **sjednocení** k sestavení jednoho celkového výsledku. Volitelně můžete název horizontálních oddílů, ze kterého řádek pochází, přidat do výstupu pomocí vlastnosti **ExecutionOptions** příkazu.

Poznamenejte si volání **myShardMap. GetShards ()** . Tato metoda načte všechny horizontálních oddílů z mapy horizontálních oddílů a poskytuje snadný způsob, jak spustit dotaz napříč všemi relevantními databázemi. Kolekce horizontálních oddílů pro dotaz multi-horizontálních oddílů může být dále provedená pomocí dotazu LINQ nad kolekcí vrácenou z volání metody **myShardMap. GetShards ()** . V kombinaci se zásadou částečných výsledků je aktuální schopnost dotazování s více horizontálních oddílůmi navržená tak, aby fungovala dobře až do stovek horizontálních oddílů.

Omezení s horizontálních oddílů dotazování je aktuálně nedostatečné ověřování pro horizontálních oddílů a shardlety, které jsou dotazovány. V případě, že směrování závislé na datech ověřuje, že daná horizontálních oddílů je součástí mapy horizontálních oddílů v době dotazování, neprovádí se u dotazů multi-horizontálních oddílů. To může vést k tomu, aby dotazy horizontálních oddílů běžely na databázích, které byly odebrány z mapy horizontálních oddílů.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Dotazy s více horizontálních oddílů a operace dělení a slučování

Dotazy multi-horizontálních oddílů neověřují, jestli se shardlety v dotazované databázi účastní probíhajících operací dělení na sloučení. (Viz [škálování pomocí nástroje elastic Database pro dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md).) To může vést k nekonzistencím, kde se řádky ze stejného shardletu zobrazení pro více databází nacházejí ve stejném horizontálních oddílů dotazu. Pamatujte na tato omezení a zvažte vyprázdnění probíhajících operací rozděleného na sloučení a změny v mapě horizontálních oddílů při provádění dotazů multi-horizontálních oddílů.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]