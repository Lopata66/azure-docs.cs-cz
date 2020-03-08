---
title: 'Rychlý Start: škálování COMPUTE – PowerShell '
description: Škálování výpočetní kapacity ve fondu SQL v PowerShellu Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5952f17c83b778e8713488b5c53c9f210c84a146
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390497"
---
# <a name="quickstart-scale-compute-in-in-azure-synapse-analytics-sql-pool-using-azure-powershell"></a>Rychlý Start: škálování výpočetních prostředků ve službě Azure synapse Analytics SQL Pool pomocí Azure PowerShell

Škálujte výpočetní prostředky ve fondu SQL pomocí Azure PowerShell. Můžete [horizontálně navýšit kapacitu výpočetních prostředků](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo ji snížit a dosáhnout tak nižších nákladů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto rychlém startu se předpokládá, že už máte fond SQL, který můžete škálovat. Pokud potřebujete vytvořit fond SQL s názvem **mySampleDataWarehouse**, vytvořte ho pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) .

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud chcete zjistit, které předplatné používáte, spusťte rutinu [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Vyhledání informací o datovém skladu

Vyhledejte název databáze, název serveru a skupinu prostředků pro datový sklad, jehož provoz chcete pozastavit a obnovit.

Informace o umístění vašeho datového skladu vyhledáte pomocí následujících kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V levé navigační stránce Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
3. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete datový sklad.

    ![Název serveru a skupina prostředků](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Poznamenejte si název datového skladu, který se použije jako název databáze. Pamatujte, že datový sklad je jedním z typů databáze. Také poznamenejte si název serveru a skupinu prostředků. V příkazech pozastavit a pokračovat budete používat název serveru a název skupiny prostředků.
5. V rutinách PowerShellu použijte jenom první část názvu serveru. Na předchozím obrázku je úplný název serveru sqlpoolservername.database.windows.net. V rutině PowerShellu používáme jako název serveru **sqlpoolservername** .

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve fondu SQL můžete zvýšit nebo snížit výpočetní prostředky úpravou jednotek datového skladu. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Pokud chcete změnit jednotky datového skladu, použijte rutinu [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) prostředí PowerShell. Následující příklad nastaví jednotky datového skladu tak, aby se DW300c pro databázi **mySampleDataWarehouse**, která je hostovaná ve skupině prostředků **ResourceGroupName** na serveru **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Kontrola stavu datového skladu

Pokud chcete zobrazit aktuální stav datového skladu, použijte rutinu PowerShellu [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) . Tato rutina zobrazuje stav databáze **mySampleDataWarehouse** ve zdroji **ResourceGroupName** a na serveru **sqlpoolservername.Database.Windows.NET**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Výsledek bude přibližně takový:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Ve výstupu vidíte **Stav** databáze. V tomto případě vidíte, že tato databáze je online.  Když spustíte tento příkaz, hodnota v poli Stav by měla být Online, Pozastavování, Obnovování, Škálování, nebo Pozastaveno.

Pokud chcete zobrazit jen samotný stav, použijte následující příkaz:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Další kroky
Nyní jste zjistili, jak škálovat výpočetní prostředky pro fond SQL. Pokud chcete získat další informace o fondu SQL, pokračujte v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md)
