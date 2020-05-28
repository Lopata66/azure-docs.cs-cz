---
title: 'PowerShell: aktualizace schématu Synchronizace dat SQL synchronizace'
description: Ukázkový skript Azure PowerShellu pro aktualizaci schématu synchronizace pro Synchronizaci dat SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: fc37ccac5a2a3373907fd7d066fb9aa16ace38b8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053517"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento příklad PowerShellu aktualizuje schéma synchronizace ve stávající skupině synchronizace v Synchronizaci dat SQL. Pokud synchronizujete několik tabulek, tento skript vám pomůže efektivně aktualizovat schéma synchronizace. Tento příklad ukazuje použití skriptu **UpdateSyncSchema**, který je k dispozici na GitHubu jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Synchronizace dat SQL v tuto chvíli nepodporuje spravovanou instanci SQL Azure.

## <a name="examples"></a>Příklady

### <a name="add-all-tables-to-the-sync-schema"></a>Přidat všechny tabulky do schématu synchronizace

Následující příklad aktualizuje schéma databáze a přidá do schématu synchronizace všechny platné tabulky v databázi centra.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Přidání a odebrání tabulek a sloupců

Následující příklad do schématu synchronizace přidá `[dbo].[Table1]` a `[dbo].[Table2].[Column1]` a odebere z něj `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametry skriptu

Skript **UpdateSyncSchema** obsahuje následující parametry:

| Parametr | Poznámky |
|---|---|
| $subscriptionId | Předplatné, ve kterém se skupina synchronizace vytvoří. |
| $resourceGroupName | Skupina prostředků, ve které se skupina synchronizace vytvoří.|
| $serverName | Název serveru s databází centra.|
| $databaseName | Název databáze centra. |
| $syncGroupName | Název skupiny synchronizace. |
| $memberName | Název člena zadejte, pokud chcete schéma databáze načíst z člena synchronizace, a ne z databáze centra. Pokud chcete schéma databáze načíst z centra, ponechte tento parametr prázdný. |
| $timeoutInSeconds | Časový limit aktualizace schématu databáze skriptem. Výchozí hodnota je 900 sekund. |
| $refreshDatabaseSchema | Určuje, jestli má skript aktualizovat schéma databáze. Pokud se schéma vaší databáze od předchozí konfigurace změnilo (například pokud jste přidali novou tabulku nebo nový sloupec), musíte schéma aktualizovat, abyste ho mohli znovu nakonfigurovat. Výchozí hodnota je false. |
| $addAllTables | Pokud je tato hodnota true, přidají se do schématu synchronizace všechny platné tabulky a sloupce. Hodnoty parametrů $TablesAndColumnsToAdd a $TablesAndColumnsToRemove se ignorují. |
| $tablesAndColumnsToAdd | Určuje tabulky nebo sloupce, které se mají přidat do schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |
| $tablesAndColumnsToRemove | Určuje tabulky nebo sloupce, které se mají odebrat ze schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |

## <a name="script-explanation"></a>Vysvětlení skriptu

Skript **UpdateSyncSchema** používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Vrátí informace o skupině synchronizace. |
| [Update – AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualizuje skupinu synchronizace. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Vrátí informace o členovi synchronizace. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Vrátí informace o schématu synchronizace. |
| [Update – AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Aktualizuje schéma synchronizace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro SQL Database najdete v [Azure SQL Database skriptech PowerShellu](../powershell-script-content-guide.md).

Další informace o Synchronizaci dat SQL:

- Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server místním](../sql-data-sync-sql-server-configure.md) prostředím
    - S využitím PowerShellu
        -  [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi databází v Azure SQL Database a databází v instanci SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Agent synchronizace dat – [Agent synchronizace dat pro synchronizace dat SQL v Azure](../sql-data-sync-agent-overview.md)
- Osvědčené postupy – [osvědčené postupy pro synchronizace dat SQL v Azure](../sql-data-sync-best-practices.md)
- Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](../sql-data-sync-monitor-sync.md)
- Řešení potíží – [řešení potíží s synchronizace dat SQL v Azure](../sql-data-sync-troubleshoot.md)
- Aktualizace schématu synchronizace
    - Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v synchronizace dat SQL v Azure](../sql-data-sync-update-sync-schema.md)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](../sql-database-paas-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
