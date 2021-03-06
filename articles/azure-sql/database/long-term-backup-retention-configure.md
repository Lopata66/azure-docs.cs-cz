---
title: 'Azure SQL Database: Správa dlouhodobého uchovávání záloh'
description: Naučte se ukládat a obnovovat automatizované zálohy pro Azure SQL Database v Azure Storage (po dobu až 10 let) pomocí Azure Portal a PowerShellu.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: 49dfed7faac1e55a40bc7b7ddd5e9555519350a2
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617302"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Správa Azure SQL Database dlouhodobého uchovávání záloh
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V Azure SQL Database můžete nakonfigurovat databázi s dlouhodobou zásadou [uchovávání záloh](long-term-retention-overview.md) (LTR) pro automatické uchovávání záloh databáze v samostatných kontejnerech úložiště objektů BLOB v Azure po dobu až 10 let. Pak můžete databázi pomocí těchto záloh obnovit pomocí Azure Portal nebo PowerShellu. Můžete také nakonfigurovat dlouhodobé uchovávání pro [Azure SQL Managed instance](../managed-instance/long-term-backup-retention-configure.md) , ale v tuto chvíli je omezená verze Public Preview.

## <a name="using-the-azure-portal"></a>Použití webu Azure Portal

V následujících částech se dozvíte, jak použít Azure Portal ke konfiguraci dlouhodobého uchovávání, zobrazení záloh v dlouhodobém uchovávání a obnovení zálohy z dlouhodobého uchovávání.

### <a name="configure-long-term-retention-policies"></a>Konfigurace dlouhodobých zásad uchovávání informací

Můžete nakonfigurovat SQL Database pro [uchovávání automatizovaných záloh](long-term-retention-overview.md) po dobu delší, než je doba uchování pro vaši úroveň služeb.

1. V Azure Portal vyberte svou instanci SQL Server a pak klikněte na **Spravovat zálohy**. Na kartě **Konfigurovat zásady** zaškrtněte políčko pro databázi, ve které chcete nastavit nebo upravit dlouhodobé zásady uchovávání záloh. Pokud není zaškrtnuté políčko vedle databáze, změny zásad se na tuto databázi nevztahují.  

   ![odkaz Správa zálohování](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. V podokně **Konfigurovat zásady** vyberte, pokud chcete uchovávat týdenní, měsíční nebo roční zálohy a určete dobu uchování pro každou z nich.

   ![Konfigurace zásad](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Po dokončení klikněte na **použít**.

> [!IMPORTANT]
> Pokud povolíte dlouhodobé zásady uchovávání záloh, může trvat až 7 dní, než se první záloha zobrazí a bude dostupná pro obnovení. Podrobnosti o Cadance zálohování LTR najdete v tématu [dlouhodobé uchovávání záloh](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Zobrazení záloh a obnovení ze zálohy

Prohlédněte si zálohy, které jsou uchovány pro konkrétní databázi se zásadou LTR, a obnovte je z těchto záloh.

1. V Azure Portal vyberte svůj server a pak klikněte na **Spravovat zálohy**. Na kartě **dostupné zálohy** vyberte databázi, pro kterou chcete zobrazit dostupné zálohy.

   ![vybrat databázi](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. V podokně **dostupné zálohy** zkontrolujte dostupné zálohy.

   ![Zobrazit zálohy](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Vyberte zálohu, ze které chcete obnovit, a pak zadejte název nové databáze.

   ![Obnovení](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Kliknutím na **OK** obnovte databázi ze zálohy ve službě Azure Storage do nové databáze.

1. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Po dokončení úlohy obnovení otevřete stránku **databáze SQL** a zobrazte nově obnovenou databázi.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

V následujících částech se dozvíte, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy ve službě Azure Storage a obnovit je ze zálohy ve službě Azure Storage.

### <a name="azure-roles-to-manage-long-term-retention"></a>Role Azure pro správu dlouhodobého uchovávání

Pro **Get-AzSqlDatabaseLongTermRetentionBackup** a **Restore-AzSqlDatabase** budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- SQL Server role přispěvatele nebo
- Vlastní role s následujícími oprávněními:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

V případě funkce **Remove-AzSqlDatabaseLongTermRetentionBackup** budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Vlastní role s následujícím oprávněním:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/DELETE

> [!NOTE]
> Role Přispěvatel SQL Server nemá oprávnění odstraňovat zálohy LTR.

Oprávnění Azure RBAC se mohla udělit buď v rámci *předplatného* , nebo v oboru *skupiny prostředků* . Pro přístup k zálohám LTR, které patří k vyřazenému serveru, ale musí být oprávnění uděleno v oboru *předplatného* daného serveru.

- Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/DELETE

### <a name="create-an-ltr-policy"></a>Vytvoření zásady LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Zobrazit zásady LTR

Tento příklad ukazuje, jak zobrazit seznam zásad LTR v rámci serveru.

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Vymazat zásadu LTR

Tento příklad ukazuje, jak vymazat zásadu LTR z databáze.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Zobrazit zálohy LTR

Tento příklad ukazuje, jak zobrazit seznam záloh LTR v rámci serveru.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranit zálohy LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Pokud chcete odstranit zálohu LTR po odstranění serveru, musíte mít oprávnění k oboru předplatného. Můžete nastavit oznámení o každém odstranění v Azure Monitor filtrováním pro operaci odstraní zálohu pro dlouhodobé uchovávání. Protokol aktivit obsahuje informace o tom, kdo a kdy žádost odeslal. Podrobné pokyny najdete v tématu [vytvoření výstrah protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md) .

### <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy LTR

Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr ID prostředku teď vyžaduje ID záložního prostředku LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Pro obnovení ze zálohy LTR po odstranění serveru musíte mít oprávnění oboru pro předplatné serveru a předplatné musí být aktivní. Je také nutné vynechat volitelný parametr-ResourceGroupName.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze. Viz [Obnovení bodu v čase](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Omezení
- Při obnovení ze zálohy LTR je vlastnost škálování pro čtení zakázaná. Pokud chcete povolit, přečtěte si v obnovené databázi škálování, aktualizujte databázi po jejím vytvoření.
- Při obnovení ze zálohy LTR, která byla vytvořena při obnovení databáze v elastickém fondu, je třeba zadat cílový cíl na úrovni služby. 

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](automated-backups-overview.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](long-term-retention-overview.md)
