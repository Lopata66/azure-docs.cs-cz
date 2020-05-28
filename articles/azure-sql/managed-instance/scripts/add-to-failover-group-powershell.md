---
title: 'PowerShell: Přidání instance do skupiny s automatickým převzetím služeb při selhání'
titleSuffix: Azure SQL Managed Instance
description: Azure PowerShell ukázkový skript pro vytvoření spravované instance Azure SQL, přidání do skupiny s automatickým převzetím služeb při selhání a testovací převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 0dbd6fbefababd36d87047a91d3ec320dc795e65
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053979"
---
# <a name="use-powershell-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Použití PowerShellu k přidání spravované instance Azure SQL do skupiny převzetí služeb při selhání 
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Tento ukázkový skript PowerShellu vytvoří dvě spravované instance SQL, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance SQL do sekundární spravované instance SQL. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. Skupinu prostředků budete muset odebrat dvakrát. Při prvním odebrání skupiny prostředků se odeberou spravované instance SQL a virtuální clustery, ale tato chybová zpráva se pak nezdaří `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` . Spusťte příkaz Remove-AzResourceGroup a podruhé odstraňte všechny zbývající prostředky i skupinu prostředků.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Načte skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o spravované instanci SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu Virtual Network. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání spravované instance SQL Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Načte nebo vypíše skupiny převzetí služeb při selhání spravované instance SQL.| 
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání pro skupinu převzetí služeb při selhání spravované instance SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu spravované instance SQL najdete v [skriptech PowerShell spravované instance Azure SQL](../../database/powershell-script-content-guide.md).
