---
title: Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí webu Azure portal
description: Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí webu Azure portal
author: mbolz
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: bf34b99ca1d328a2de47aade0dcdff5b584ff07c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793534"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla virtuální sítě pomocí webu Azure portal
Koncové body služeb virtuální sítě (VNet) a pravidla rozšiřují privátní adresní prostor virtuální sítě pro váš server Azure Database for PostgreSQL. Přehled služby Azure Database pro koncové body služby virtuální sítě PostgreSQL, včetně omezení, naleznete v tématu [– Azure Database for koncové body služby virtuální sítě serveru PostgreSQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastí pro službu Azure Database for PostgreSQL.

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.
> V případě VNet peering, pokud provoz se přenášejí prostřednictvím společnou bránu virtuální sítě s koncovými body služby a má tok a partnerským zařízením, vytvořte prosím pravidlo seznamu ACL/virtuální síť umožňuje službě Azure Virtual Machines ve virtuální síti Brána pro přístup k Azure Database for PostgreSQL server.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvořit pravidlo virtuální sítě a povolit koncové body služby na webu Azure Portal

1. Na stránce v části Nastavení serveru PostgreSQL záhlaví, klikněte na tlačítko **zabezpečení připojení** a otevřete tak podokno zabezpečení připojení pro službu Azure Database for PostgreSQL. 

2. Ujistěte se, zda povolit přístup do služby Azure správy nastaven na **OFF**.

> [!Important]
> Pokud necháte ovládacího prvku na hodnotu ON, váš server Azure Database for postgresql – přijímá sdělení jakoukoli podsíť. Opuštění ovládacího prvku na hodnotu ON, může být nadměrné přístupu z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network ve spolupráci s funkcí pravidlo virtuální sítě – Azure Database for PostgreSQL, můžete snížit společně zabezpečení plochy.

3. Pak klikněte na **+ přidání existující virtuální sítě**. Pokud nemáte existující virtuální síť můžete kliknout na **+ vytvořit novou virtuální síť** k jejímu vytvoření. Zobrazit [rychlý start: Vytvoření virtuální sítě pomocí webu Azure portal](../virtual-network/quick-create-portal.md)

   ![Portál Azure – zabezpečení připojení klikněte na](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na tlačítko **povolit**. To umožňuje automaticky koncové body služby virtuální sítě na podsíť pomocí **Microsoft.SQL** značka služby.

   ![Azure portal – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

    Koncové body služby může být ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě.
    
    Svázat prostředky služeb Azure k virtuální síti, uživatel musí mít pro přidávané podsítě oprávnění "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/". Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
    Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud virtuální síť a prostředky služeb Azure v různých předplatných, by měl být prostředky ve stejném tenantovi Active Directory (AD).

   > [!IMPORTANT]
   > Důrazně doporučujeme v tomto článku o konfigurace koncového bodu služby a důležité informace před konfigurací koncových bodů služby. **Koncový bod pro služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. Koncové body služeb virtuální sítě použít název typu služby **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě konfiguruje provoz koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Servery Azure Database for MySQL v podsíti. 
   > 

5. Jakmile povolíte službu, klikněte na tlačítko **OK** a uvidíte, že jsou povolené koncové body služby virtuální sítě spolu s pravidlo virtuální sítě.

   ![Povolené koncové body služeb virtuální sítě a vytvoří pravidlo virtuální sítě](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další postup
- Podobně můžete používat skripty pro [koncové body služby povolit virtuální sítě a vytvořit pravidlo virtuální sítě pro službu Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Pomoc při připojování k serveru Azure Database for PostgreSQL, naleznete v tématu [připojení knihoven pro službu Azure Database for PostgreSQL](./concepts-connection-libraries.md)
