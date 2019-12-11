---
title: 'Kurz: použití Azure Database Migration Service k provedení online migrace SQL Server do spravované instance Azure SQL Database | Microsoft Docs'
description: Naučte se provádět online migraci z SQL Server místně do Azure SQL Database spravované instance pomocí Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/04/2019
ms.openlocfilehash: 53c0601be29c5cac9bddc37158d705f07349323d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975019"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Kurz: migrace SQL Server do Azure SQL Database spravované instance online pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z místní instance SQL Server do [Azure SQL Database spravované instance](../sql-database/sql-database-managed-instance.md) s minimálními výpadky. Další metody, které mohou vyžadovat určité ruční úsilí, najdete v článku [migrace instance SQL Server do Azure SQL Database spravované instance](../sql-database/sql-database-managed-instance-migrate.md).

V tomto kurzu migrujete databázi **Adventureworks2012** z místní instance SQL Server do SQL Database spravované instance s minimálními výpadky pomocí Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace a spusťte online migraci pomocí Azure Database Migration Service.
> * Monitorování migrace
> * Až budete připraveni, proveďte migraci přímou migraci.

> [!IMPORTANT]
> Pro online migrace z SQL Server do SQL Database spravované instance pomocí Azure Database Migration Service musíte poskytnout úplnou zálohu databáze a následné zálohy protokolu ve sdílené síťové složce SMB, kterou může služba použít k migraci vašich databází. Azure Database Migration Service neiniciují žádné zálohy a místo toho používá existující zálohy, které už možná máte v rámci plánu zotavení po havárii pro migraci.
> Ujistěte se, že provádíte [zálohování pomocí možnosti s kontrolním součtem](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Také se ujistěte, že nechcete do jediného záložního média připojit více záloh (tj. Full a t-log). proveďte každou zálohu samostatného záložního souboru. Nakonec můžete použít komprimované zálohy a snížit tak pravděpodobnost výskytu potenciálních problémů spojených s migrací velkých záloh.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace doporučuje Microsoft vytvořit instanci Azure Database Migration Service ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z SQL Server do spravované instance SQL Database. Offline migraci najdete v článku [migrace SQL Server do SQL Database spravované instance offline pomocí DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

* Vytvořte Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Naučte se síťové topologie pro migrace Azure SQL Database spravované instance pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Další informace o vytvoření virtuální sítě najdete v dokumentaci k [Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.
    >
    >Pokud nemáte připojení Site-to-site mezi místní sítí a Azure nebo pokud dojde k omezené šířce pásma připojení mezi lokalitami, zvažte použití Azure Database Migration Service v hybridním režimu (Preview). Hybridní režim využívá místní pracovní proces migrace společně s instancí Azure Database Migration Service spuštěnou v cloudu. Chcete-li vytvořit instanci Azure Database Migration Service v hybridním režimu, přečtěte si článek [vytvoření instance Azure Database Migration Service v hybridním režimu pomocí Azure Portal](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > V souvislosti s účtem úložiště použitým v rámci migrace je nutné buď:
    > * Vyberte, pokud chcete, aby přístup k účtu úložiště umožňovala všechna síť.
    > * Nastavte seznamy řízení přístupu (ACL) pro virtuální síť. Další informace najdete v článku [konfigurace Azure Storage bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security).

* Zajistěte, aby pravidla skupiny zabezpečení sítě VNet neblokovala následující příchozí komunikační porty Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů Azure VNet NSG najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall, čímž povolíte Azure Database Migration Service přístup ke zdrojovému SQL Server, který je ve výchozím nastavení port TCP 1433.
* Pokud používáte více instancí s názvem SQL Server s použitím dynamických portů, možná budete chtít povolit službu SQL Browser a povolit přístup k portu UDP 1434 přes brány firewall, aby se Azure Database Migration Service mohli připojit k pojmenované instanci na vašem zdroji. WebServer.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci, stejně jako souborům přes SMB port 445.
* Vytvořte SQL Database spravovanou instanci podle podrobných informací v článku [vytvoření Azure SQL Database spravované instance v Azure Portal](https://aka.ms/sqldbmi).
* Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
* Poskytněte sdílenou síťovou složku protokolu SMB, která obsahuje všechny soubory zálohy databáze úplné databáze a následné záložní soubory transakčního protokolu, které Azure Database Migration Service můžou použít k migraci databáze.
* Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
* Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Azure Database Migration Service zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
* Vytvořte Azure Active Directory ID aplikace, které generuje klíč ID aplikace, který Azure Database Migration Service může použít k připojení k cílové spravované instanci Azure Database a kontejneru Azure Storage. Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Azure Database Migration Service vyžaduje oprávnění přispěvatele u předplatného pro zadané ID aplikace. Případně můžete vytvořit vlastní role, které udělují specifická oprávnění, která Azure Database Migration Service vyžaduje. Podrobné pokyny k používání vlastních rolí najdete v článku [vlastní role pro SQL Server SQL Database online migrace spravované instance](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Vytvořte nebo si poznamenejte **úroveň výkonu Standard** účtu úložiště Azure, do kterého může služba DMS nahrát soubory záloh databází a který může použít k migraci databází.  Ujistěte se, že jste vytvořili účet Azure Storage ve stejné oblasti, ve které je vytvořena instance Azure Database Migration Service.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílové SQL Database spravované instanci.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro migraci Azure SQL Database spravované instance pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte SKU z cenové úrovně Premium.

    > [!NOTE]
    > Online migrace se podporují jenom v případě, že používáte úroveň Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **SQL Server**, v textovém poli **typ cílového serveru** vyberte možnost **Azure SQL Database spravovaná instance**a pak u **Možnosti zvolit typ aktivity**vyberte možnost **migrace online dat**.

   ![Vytvořit Azure Database Migration Service projekt](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Vyberte **Save** (Uložit).

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS), služba DMS v současné době nepodporuje migraci databáze katalogu pro vaše projekty SSIS a balíčky (SSISDB) z SQL Server na Azure SQL Database spravovanou instanci. Můžete ale zřídit SSIS v Azure Data Factory (ADF) a znovu nasadit SSIS projekty/balíčky do cílového SSISDBu hostovaného Azure SQL Database Managed instance. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Save** (Uložit).

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte **ID aplikace** a **klíč** , který může instance DMS použít pro připojení k cílové instanci Azure SQL Database spravované instanci a účet Azure Storage.

    Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Vyberte **odběr** , který obsahuje cílovou instanci Azure SQL Database Managed instance, a pak vyberte cílovou instanci.

    Pokud jste ještě Azure SQL Database spravovanou instanci nezřídili, vyberte [odkaz](https://aka.ms/SQLDBMI) , který vám pomůžete zřídit instanci. Když je spravovaná instance Azure SQL Database připravená, vraťte se do tohoto konkrétního projektu a spusťte migraci.

3. Zadejte **uživatele** a **heslo** SQL pro připojení k Azure SQL Database Managed instance.

    ![Výběr cíle](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Vyberte **Save** (Uložit).

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Vyberte **Save** (Uložit).

## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace

1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Sdílená složka SMB v síťovém umístění** | Místní sdílená síťová složka SMB nebo sdílená složka Azure, která obsahuje soubory zálohy úplné databáze a záložní soubory protokolu transakcí, které Azure Database Migration Service můžou použít k migraci. Účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, musí pro tuto sdílenou síťovou složku mít oprávnění ke čtení i zápisu. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Azure Database Migration Service zosobní přihlašovací údaje uživatele pro nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. Pokud používáte sdílenou složku Azure, použijte jako uživatelské jméno název účtu úložiště označené jako nedokončené s AZURE. |
    |**Heslo** | Heslo pro tohoto uživatele. Pokud používáte službu Azure File Share, jako heslo použijte klíč účtu úložiště. |
    |**Předplatné účtu úložiště Azure** | Vyberte předplatné obsahující účet úložiště Azure. |
    |**Účet úložiště Azure** | Vyberte účet úložiště Azure, do kterého může DMS nahrát soubory záloh ze sdílené síťové složky SMB a který může použít k migraci databází.  Pro zajištění optimálního výkonu nahrávání souborů doporučujeme vybrat účet úložiště ve stejné oblasti, jako je služba DMS. |

    ![Konfigurace nastavení migrace](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)


> [!NOTE]
  > Pokud se Azure Database Migration Service zobrazuje chyba "Systémová chyba 53" nebo "Systémová chyba 57", může dojít k tomu, že by výsledkem byla neschopnost Azure Database Migration Service přistupovat ke sdílené složce Azure. Pokud narazíte na jednu z těchto chyb, udělte [vám pokyny k](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)účtu úložiště z virtuální sítě.


2. Vyberte **Save** (Uložit).

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

    ![Shrnutí projektu migrace](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Spuštění a monitorování migrace

1. Vyberte **Spustit migraci**.

2. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Provedení přímé migrace

Po obnovení úplné zálohy databáze v cílové instanci SQL Database Managed instance je databáze k dispozici pro provedení migrace přímou migraci.

1. Jakmile budete připraveni dokončit online migraci databází, vyberte **Spustit přímou migraci**.

2. Zastavte veškerý příchozí provoz do zdrojových databází.

3. Proveďte [zálohování protokolu poškozené databáze], zpřístupněte soubor zálohy ve sdílené síťové složce SMB a pak počkejte na obnovení této konečné zálohy transakčních protokolů.

    V tuto chvíli se zobrazí hodnota **Probíhající změny** nastavená na 0.

4. Vyberte **Potvrdit** a pak **Použít**.

    ![Příprava na dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Jakmile se zobrazí stav migrace databáze **dokončeno**, připojte aplikace k nové cílové instanci Azure SQL Database spravované instance.

    ![Dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Další kroky

* Kurz, ve kterém se dozvíte, jak migrovat databázi do spravované instance pomocí příkazu T-SQL Restore, najdete v tématu [obnovení zálohy do spravované instance pomocí příkazu RESTORE](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Informace o spravované instanci najdete v tématu [co je spravovaná instance](../sql-database/sql-database-managed-instance.md).
* Informace o připojení aplikací ke spravované instanci najdete v tématu [připojení aplikací](../sql-database/sql-database-managed-instance-connect-app.md).
