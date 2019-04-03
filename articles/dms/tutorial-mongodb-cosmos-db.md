---
title: 'Kurz: Offline migrace MongoDB API služby Azure Cosmos DB pro MongoDB pomocí Azure Database Migration Service | Dokumentace Microsoftu'
description: Zjistěte, jak migrovat místních MongoDB API služby Azure Cosmos DB pro MongoDB do offline režimu s využitím Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 68cf4561d24a84c5aacefbe5fbd11bcd7e0576aa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878549"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Kurz: Migrace MongoDB API služby Azure Cosmos DB pro MongoDB offline pomocí DMS
Azure Database Migration Service můžete použít k provedení offline migrace (jednorázová) databází z místní nebo cloudové instance MongoDB API služby Azure Cosmos DB pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace

V tomto kurzu migrujete datové sady v databáze MongoDB hostované ve virtuálním počítači Azure do Azure Cosmos DB přes rozhraní API pro MongoDB s využitím Azure Database Migration Service. Pokud nemáte zdroji MongoDB již nastaveny, najdete v článku [nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:
- [Vytvoření rozhraní API služby Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Vytvoření Azure Virtual Network (VNET) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Při nastavení virtuální sítě, pokud používáte ExpressRoute se síť vytvoření partnerského vztahu Microsoftu, přidejte následující službu [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) k podsíti, ve kterém se zřídí služby:
    > - Koncový bod databázového cíl (například koncový bod SQL, koncového bodu služby Cosmos DB a tak dále)
    > - Koncový bod úložiště
    > - Koncový bod služby Service bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

- Ujistěte se, že pravidla skupiny zabezpečení sítě VNET nedošlo k blokování následující porty příchozí komunikace k Azure Database Migration Service: 53, 443, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Otevřete bránu Windows firewall a povolit službu Azure Database Migration Service přístup k serveru MongoDB zdroj, který ve výchozím nastavení je TCP port 27017.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance
1.  Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.
 
    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k instanci MongoDB zdrojového a cílového účtu služby Azure Cosmos DB.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    Pokud potřebujete pomoc s výběrem správné úrovně služby Azure Database Migration Service, podívejte se na doporučení v blogovém příspěvku [tady](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.
 
      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **MongoDB**v **cílový typ serveru**  textového pole, vyberte **služby cosmos DB (rozhraní API MongoDB)** a potom **zvolte typ aktivity**vyberte **migrace Offline dat**. 

    ![Vytvořte projekt Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji
1. Na **zdrojové podrobnosti** obrazovky, zadat podrobnosti připojení pro zdrojový server MongoDB.
    
   Můžete také použít režim připojovacího řetězce a zadejte umístění pro kontejner objektů blob úložiště souboru, ve kterém jste zálohované dat kolekce, kterou chcete migrovat.

   > [!NOTE]
   > Azure Database Migration Service můžete také migrovat bson dokumentů nebo dokumentů json do rozhraní API služby Azure Cosmos DB pro MongoDB kolekce.
    
   V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli
1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl účtu Azure Cosmos DB, který je předem zřízené služby Azure Cosmos DB pro rozhraní API pro účet MongoDB, do kterého budete migrovat MongoDB data.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze
1. Na **mapu, která bude cílová databáze** obrazovky, mapování zdrojové a cílové databáze pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    Pokud řetězec **vytvořit** se zobrazí vedle názvu databáze, znamená to, že Azure Database Migration Service se nepovedlo najít cílovou databázi a službu databáze se vytvoří za vás.

    V tomto okamžiku migrace, můžete [zřídit propustnost](https://docs.microsoft.com/azure/cosmos-db/set-throughput). Propustnost na úrovni databáze nebo jednotlivě pro každou kolekci můžete zřizovat v databázi Cosmos DB. Propustnost se měří v [jednotek žádostí](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru). Další informace o [ceny služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Vyberte **Uložit**.
3. Na **nastavení kolekce** obrazovky rozbalte kolekce výpisu a pak si projděte seznam kolekcí, které se budou migrovat.

    Všimněte si, že služba Azure Database Migration Service automaticky vybere všechny kolekce, které existují v instanci MongoDB zdroje, které neexistují v cílovém účtu služby Azure Cosmos DB. Pokud chcete přenést kolekce, které již obsahují data, musíte explicitně vyberte kolekce, v tomto okně.

    Můžete zadat množství RU, které chcete, aby kolekce k použití. Azure Database Migration Service navrhuje inteligentních výchozích hodnot na základě velikosti kolekce.

    Můžete také zadat klíč horizontálního dělení, abyste mohli využívat [dělení ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro optimální škálovatelnost. Nezapomeňte si přečíst [osvědčené postupy pro výběr klíče horizontálních oddílů nebo oddíl](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Výběr kolekce tabulek](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Shrnutí migrace](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace
- Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **stav** aktivity je **Nezahájeno**.

    ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
- Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

   > [!NOTE]
   > Můžete vybrat aktivit zobrazíte podrobnosti databáze a kolekce – migraci metriky na úrovni.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Ověření dat ve službě Cosmos DB

- Po dokončení migrace můžete zkontrolovat svůj účet služby Azure Cosmos DB k ověření, že se úspěšně migrovaly všechny kolekce.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Další materiály

 * [Informace o službě cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci další scénáře v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).
