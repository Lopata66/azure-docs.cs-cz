---
title: 'Rychlý start: Vytvoření a dotazování Azure SQL Data Warehouse – Azure Portal | Dokumentace Microsoftu'
description: Vytvořte a dotazujte datový sklad Azure SQL Data warehouse na webu Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 08/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 053dccb4ba5798fabd4426d5741d9238af579449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732824"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Vytvoření a dotazování datového skladu SQL Azure na webu Azure Portal

Rychle vytvořit a dotazovat službu Azure SQL data warehouse s využitím webu Azure portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba. Další informace najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="before-you-begin"></a>Než začnete

Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Vytvoření datového skladu

Datový sklad SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků](memory-and-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru SQL Azure](../sql-database/sql-database-logical-servers.md). 

Podle následujícího postupu vytvořte datový sklad SQL, který obsahuje ukázková data AdventureWorksDW. 

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** vyberte **Databáze** a v části **Doporučené** na stránce **Nový** vyberte **SQL Data Warehouse**.

    ![vytvoření prázdného datového skladu](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Do formuláře služby SQL Data Warehouse zadejte následující informace:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název databáze** | mySampleDataWarehouse | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). Poznámka: datový sklad je typem databáze.|
    | **Předplatné** | Vaše předplatné | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
    | **Výběr zdroje** | Ukázka | Určuje, že se má načíst ukázková databáze. Poznámka: datový sklad je jedním z typů databáze. |
    | **Výběr ukázky** | AdventureWorksDW | Určuje, že se má načíst ukázková databáze AdventureWorksDW. |
    ||||

    ![vytvoření datového skladu](media/create-data-warehouse-portal/select-sample.png)

4. Klikněte na **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace: 

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |
    ||||

    ![vytvoření databázového serveru](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klikněte na **Vybrat**.

6. Klikněte na tlačítko **úroveň výkonu** určují konfiguraci výkonu pro datový sklad.

7. Pro účely tohoto kurzu vyberte **Gen2**. Posuvník, ve výchozím nastavení, je nastavený na **DW1000c**. Zkuste jeho posouváním hodnotu zvýšit a snížit a podívejte se, jak funguje. 

    ![konfigurace výkonu](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klikněte na tlačítko **Použít**.

9. Teď, když jste dokončili formuláři SQL Data Warehouse, klikněte na tlačítko **vytvořit** a databázi zřiďte. Zřizování trvá několik minut. 

    ![kliknutí na Vytvořit](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.
    
     ![oznámení](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Data Warehouse vytvoří bránu firewall na úrovni serveru, aby zabránila externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres. Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta. 

> [!NOTE]
> SQL Data Warehouse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.

1. Jakmile se nasazení dokončí, klikněte na tlačítko **datové sklady SQL** z nabídky na levé straně a pak klikněte na tlačítko **mySampleDatabase** na **datové sklady SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, zobrazí plně kvalifikovaný název (například **mynewserver-20180430.database.windows.net**) a poskytne vám možnosti další konfigurace. 

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech. Pokud chcete otevřít nastavení serveru, klikněte na název serveru.

   ![vyhledání názvu serveru](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Pokud chcete otevřít nastavení serveru, 
4. klikněte na název serveru.

   ![nastavení serveru](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Klikněte na **Zobrazit nastavení brány firewall**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

   ![pravidlo brány firewall serveru](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Pokud chcete do nového pravidla brány firewall přidat svou aktuální IP adresu, klikněte na **Přidat IP adresu klienta** na panelu nástrojů. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Pomocí této IP adresy se teď můžete připojit k serveru SQL a jeho datovým skladům. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Pokud chcete bránu firewall zakázat pro všechny služby Azure, klikněte na této stránce na **VYPNUTO** pak klikněte na **Uložit**.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **SQL datových skladů** z nabídky na levé straně a klikněte na datový sklad na **SQL datawarehouses** stránky. 
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název mynewserver-20180430.database.windows.net. 

    ![informace o připojení](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Tady je příklad: **mynewserver-20180430.database.windows.net**. |
   | Authentication | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |
   ||||

    ![Připojení k serveru](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Klikněte na **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

    ![databázové objekty](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Spuštění nějakých dotazů

SQL Data Warehouse jako dotazovací jazyk používá T-SQL. Pokud chcete otevřít okno dotazu a spustit nějaké dotazy T-SQL, použijte následující postup:

1. Klikněte pravým tlačítkem na **mySampleDataWarehouse** a vyberte **Nový dotaz**. Otevře se nové okno dotazu.
2. V okně dotazu zadejte následující příkaz, který zobrazí seznam databází.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Klikněte na tlačítko **Spustit**. Výsledky dotazu ukazují dvě databáze: **master** a **mySampleDataWarehouse**.

    ![Dotazování databází](media/create-data-warehouse-portal/query-databases.png)

4. Pokud se chcete podívat na nějaká data, pomocí následujícího příkazu zobrazte počet zákazníků s příjmením Adams, kteří mají tři děti. Seznam výsledků obsahuje šest zákazníků. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Dotaz dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a uložená data v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat pouze úložiště dat. Výpočetní prostředky můžete obnovit, kdykoli budete připraveni s daty pracovat.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na váš datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**. Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, klikněte na **Odstranit**.

4. Pokud chcete odstranit server SQL, který jste vytvořili, klikněte na tlačítko **mynewserver-20180430.database.windows.net** v předchozím obrázku a pak klikněte na tlačítko **odstranit**. S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

Právě jste vytvořili datový sklad a pravidlo brány firewall, připojili jste se ke svému datovému skladu a spustili jste několik dotazů. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
> [Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
