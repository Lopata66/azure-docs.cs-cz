---
title: Hromadné kopírování z databáze pomocí řídicí tabulky
description: Naučte se používat šablonu řešení ke kopírování hromadných dat z databáze pomocí externí tabulky ovládacích prvků k uložení seznamu oddílů zdrojových tabulek pomocí Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439923"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Hromadné kopírování z databáze pomocí řídicí tabulky

Chcete-li kopírovat data z datového skladu v Oracle serveru, Netezza, Teradata nebo SQL Server Azure SQL Data Warehouse, je nutné načíst obrovský objem dat z více tabulek. Data musí být obvykle rozdělená do oddílů v každé tabulce, takže můžete načíst řádky s více vlákny paralelně z jedné tabulky. Tento článek popisuje šablonu, která se má použít v těchto scénářích.

 >! Poznámka: Pokud chcete kopírovat data z malého počtu tabulek s relativně malým objemem dat do SQL Data Warehouse, je efektivnější použít [nástroj Azure Data Factory kopírování dat](copy-data-tool.md). Šablona popsaná v tomto článku je více, než kolik jich v tomto scénáři potřebujete.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona načte seznam oddílů zdrojové databáze pro kopírování z externí tabulky ovládacích prvků. Pak provede iteraci na všech oddílech zdrojové databáze a zkopíruje data do cílového umístění.

Šablona obsahuje tři aktivity:
- Při **vyhledávání** se načte seznam, ve kterém se zajišťují oddíly databáze z externí tabulky ovládacích prvků.
- **Foreach** získá seznam oddílů z aktivity vyhledávání a projde každý oddíl aktivitou kopírování.
- **Kopírovat** zkopíruje všechny oddíly ze zdrojového úložiště databáze do cílového úložiště.

Šablona definuje následující parametry:
- *Control_Table_Name* je vaše externí tabulka ovládacího prvku, která ukládá seznam oddílů pro zdrojovou databázi.
- *Control_Table_Schema_PartitionID* je název sloupce v tabulce externích ovládacích prvků, ve kterém jsou uložena ID jednotlivých oddílů. Ujistěte se, že je ID oddílu jedinečné pro každý oddíl ve zdrojové databázi.
- *Control_Table_Schema_SourceTableName* je vaše externí řídicí tabulka, která ukládá názvy jednotlivých tabulek ze zdrojové databáze.
- *Control_Table_Schema_FilterQuery* je název sloupce v tabulce externích ovládacích prvků, ve kterém je uložený dotaz filtru, který získá data z každého oddílu zdrojové databáze. Pokud jste například děleni data po rocích, dotaz uložený v každém řádku může být podobný příkazu SELECT * FROM DataSource, kde LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' a LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* je cesta, kam se zkopírují data do cílového úložiště (k dispozici, pokud je zvolený cíl "File System" nebo "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* je cesta ke kořenové složce, do které se zkopírují data do cílového úložiště. 
- *Data_Destination_Directory* je cesta k adresáři v kořenovém adresáři, kam se zkopírují data do cílového úložiště. 

Poslední tři parametry, které definují cestu v cílovém úložišti, jsou viditelné pouze v případě, že zvolený cíl je úložiště založené na souborech. Pokud jako cílové úložiště zvolíte Azure synapse Analytics (dřív SQL DW), tyto parametry se nevyžadují. Názvy tabulek a schématu v SQL Data Warehouse musí být stejné jako ty ve zdrojové databázi.

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Umožňuje vytvořit v SQL Server nebo Azure SQL Database tabulku ovládacího prvku pro uložení seznamu oddílů zdrojové databáze pro hromadnou kopii. V následujícím příkladu je ve zdrojové databázi pět oddílů. Tři oddíly jsou pro *datasource_table*a dva jsou pro *project_table*. Sloupec *LastModifytime* se používá k vytvoření oddílů dat v tabulce *datasource_table* ze zdrojové databáze. Dotaz, který se používá ke čtení prvního oddílu, je SELECT * FROM datasource_table, kde LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' a LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' '. Podobný dotaz můžete použít ke čtení dat z jiných oddílů.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Přejít na šablonu **databáze hromadného kopírování** . Vytvořte **nové** připojení k tabulce externích ovládacích prvků, kterou jste vytvořili v kroku 1.

    ![Vytvoří nové připojení k řídicí tabulce.](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Vytvořte **nové** připojení ke zdrojové databázi, ze které kopírujete data.

    ![Vytvoří nové připojení ke zdrojové databázi.](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Vytvořte **nové** připojení k cílovému úložišti dat, do kterého kopírujete data.

    ![Vytvoří nové připojení k cílovému úložišti.](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Vyberte **Použít tuto šablonu**.

6. Zobrazí se kanál, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Vyberte **ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na * * ladit * *.](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zobrazí se výsledky podobné následujícímu příkladu:

    ![Kontrola výsledku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Volitelné Pokud jste jako cíl dat vybrali Azure synapse Analytics (dřív SQL DW), musíte pro přípravu zadat připojení k úložišti objektů BLOB v Azure, jak to vyžaduje SQL Data Warehouse báze. Šablona automaticky vygeneruje cestu kontejneru pro úložiště objektů BLOB. Ověřte, zda byl kontejner vytvořen po spuštění kanálu.
    
    ![Základní nastavení](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
