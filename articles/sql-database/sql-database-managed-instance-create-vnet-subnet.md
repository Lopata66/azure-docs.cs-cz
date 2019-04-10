---
title: Vytvoření virtuální sítě pro Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit virtuální síť, kde můžete nasadit Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357847"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Vytvoření virtuální sítě pro Azure SQL Database Managed Instance

Tento článek vysvětluje, jak vytvořit platnou virtuální síť a podsíť, kde můžete nasadit Azure SQL Database Managed Instance.

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální sítě](../virtual-network/virtual-networks-overview.md). Toto nasazení umožňuje následující scénáře:

- Zabezpečené privátní IP adresy
- Připojení k Managed Instance přímo z místní sítě
- Připojení Managed Instance pro odkazovaný server nebo další místní úložiště dat
- Připojování k prostředkům Azure Managed Instance  

> [!Note]
> Měli byste [určit velikost podsítě pro Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md) před nasazením první instance. Po stačí vložit prostředky uvnitř nejde jeho velikost změnit podsíť.
>
> Pokud plánujete použití existující virtuální sítě, budete muset upravit tuto konfiguraci sítě tak, aby vyhovovaly Managed Instance. Další informace najdete v tématu [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nejjednodušší způsob, jak vytvořit a konfigurovat virtuální síť se má použít nasazení šablony Azure Resource Manageru.

1. Přihlaste se k portálu Azure.

2. Vyberte **nasadit do Azure** tlačítka:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí kde můžete nasadit Managed Instance.

   > [!Note]
   > Tuto šablonu Azure Resource Manageru se nasazení virtuální sítě se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances**, je vyhrazený pro Managed Instance a má předem směrovací tabulku. Další podsítě, volá **výchozí**, se používá pro jiné prostředky, které by měl přístup k Managed Instance (například virtuální počítače Azure).

3. Konfigurace síťového prostředí. Na následující formulář můžete nakonfigurovat parametry vaše síťové prostředí:

   ![Šablony Resource Manageru pro konfiguraci sítě Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Můžete změnit názvy virtuální sítě a podsítě a upravit rozsahy IP adres přidružené síťové prostředky. Po výběru **nákupní** tlačítko, vytvoří tento formulář a konfiguraci prostředí. Pokud už nebudete potřebovat dvě podsítě, můžete odstranit výchozí hodnotu.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Další informace o [architektura připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Zjistěte, jak [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvořit Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
