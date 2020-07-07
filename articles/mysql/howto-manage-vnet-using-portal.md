---
title: Správa koncových bodů virtuální sítě – Azure Portal-Azure Database for MySQL
description: Vytváření a správa koncových bodů a pravidel služby VNet Azure Database for MySQL pomocí Azure Portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba63c65c2b0d7abda089c3dfd315bfe499632cc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82509223"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytváření a správa koncových bodů služby VNet Azure Database for MySQL a pravidel virtuální sítě pomocí Azure Portal
Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MySQL. Přehled koncových bodů služby virtuální sítě Azure Database for MySQL, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for MySQL serveru](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for MySQL.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.
> V případě partnerského vztahu virtuálních sítí platí, že pokud přenos prochází přes společnou bránu virtuální sítě s koncovými body služby a měl by se přesměrovat na partnera, vytvořte prosím pravidlo seznamu ACL/virtuální sítě, které povolí službě Azure Virtual Machines ve virtuální síti brány pro přístup k serveru Azure Database for MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvoření pravidla virtuální sítě a povolení koncových bodů služby v Azure Portal

1. Na stránce serveru MySQL klikněte v záhlaví nastavení na **zabezpečení připojení** a otevřete podokno zabezpečení připojení pro Azure Database for MySQL. 

2. Zajistěte, aby byl ovládací prvek povolení přístupu ke službám Azure nastavený na **vypnuto**.

> [!Important]
> Pokud ovládací prvek necháte nastavený na ZAPNUTo, databázový server Azure MySQL přijme komunikaci ze všech podsítí. Ponechání ovládacího prvku na ZAPNUTo může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network, v koordinaci s funkcí pravidla virtuální sítě Azure Database for MySQL, společně může snížit oblast zabezpečení.

3. Potom klikněte na **+ Přidat existující virtuální síť**. Pokud nemáte existující virtuální síť, můžete ji vytvořit kliknutím na **+ vytvořit novou virtuální síť** . Další informace najdete v tématu [rychlý Start: vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md)

   ![Azure Portal – kliknutí na zabezpečení připojení](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na **Povolit**. Tím se automaticky povolí koncové body služby virtuální sítě v podsíti s použitím značky služby **Microsoft. SQL** .

   ![Azure Portal – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

   Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.
    
   Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
   Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

   > [!IMPORTANT]
   > Před konfigurací koncových bodů služby se důrazně doporučuje přečíst si tento článek o konfiguracích koncových bodů a požadavcích služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database služby Azure Database for PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL serverů v podsíti. 
   > 

5. Po povolení klikněte na **OK** a uvidíte, že koncové body služby virtuální sítě jsou povolené spolu s pravidlem virtuální sítě.

   ![Povolené koncové body služby virtuální sítě a vytvořené pravidlo virtuální sítě](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další kroky
- Podobně můžete skript pro [Povolení koncových bodů služby virtuální sítě a vytvoření pravidla virtuální sítě pro Azure Database for MySQL pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Nápovědu k připojení k serveru Azure Database for MySQL najdete v tématu [knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md