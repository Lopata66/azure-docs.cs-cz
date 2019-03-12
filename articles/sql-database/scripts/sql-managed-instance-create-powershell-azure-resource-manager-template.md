---
title: Příklad Powershellu – vytvoření ve službě Azure SQL Database managed instance | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu pro vytvoření spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: 416a989f672aadcec2f1871bcea635500d1d0a93
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575694"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Vytvoření spravované instance Azure SQL Database pomocí prostředí PowerShell pomocí šablony Azure Resource Manageru

Azure SQL Database Managed Instance můžete vytvořit pomocí Azure Powershellu knihovny a šablony Azure Resource Manageru.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, v tomto kurzu vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Nasazení pomocí předdefinované šablony Azure Resource Manageru můžete spustit příkazy Azure Powershellu. V šabloně můžete zadat následující vlastnosti:

- Název instance
- Uživatelské jméno správce SQL a heslo.
- Velikost instance (počet jader a maximální velikost úložiště).
- Virtuální síť a podsíť, kde budou umístěné na instanci.
- Serverové kolace instance (Preview).

Název instance, uživatelské jméno správce SQL, virtuální sítě nebo podsítě a kolace není možné později změnit. Další instanci vlastnosti lze změnit.

## <a name="prerequisites"></a>Požadavky

Tento příklad předpokládá, že máte [vytvoření prostředí s platnou sítí](../sql-database-managed-instance-create-vnet-subnet.md) nebo [upravit existující virtuální síť](../sql-database-managed-instance-configure-vnet-subnet.md) pro Managed Instance. Ukázka používá rutiny [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) proto se ujistěte, zda je nainstalována následující moduly prostředí PowerShell:

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Následující obsah musí být umístěné ve soubor, který představuje, který se použije k vytvoření instance šablony:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Předpokladem je, že už existuje virtuální sítě Azure s podsítí správně nakonfigurovaný. Pokud nemáte podsíť správně nakonfigurovaný, Příprava síťového prostředí na použití samostatných [spravovaných prostředků Azure šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) , který můžete spustit nezávisle nebo obsažená v šabloně.

Uložte obsah tohoto souboru jako soubor .json, cesta k souboru vložte následující skript prostředí PowerShell a změnit názvy objektů ve skriptu:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Po úspěšném spuštění skriptu bude služba SQL Database přístupná ze všech služeb Azure a nakonfigurovaných IP adres.

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
