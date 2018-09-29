---
title: 'Kurz: Návrh první databáze Azure SQL Database pomocí SSMS | Microsoft Docs'
description: Zjistěte, jak navrhnout první databázi SQL Azure pomocí aplikace SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: 2eebc083c4595a9190c60a60b8375b3a46296584
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162759"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Kurz: Návrh první databáze Azure SQL Database pomocí SSMS

Azure SQL Database je relační databáze jako služba (DBaaS) v cloudu Microsoftu (Azure). V tomto kurzu se naučíte používat web Azure Portal a aplikaci [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS), když chcete: 

> [!div class="checklist"]
> * Vytvořit databázi na portálu Azure Portal*
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojit se k databázi pomocí SSMS
> * Vytvářet tabulky pomocí SSMS
> * Hromadně načítat data pomocí BCP
> * Zadávat na tato data dotazy pomocí SSMS

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

   >[!NOTE]
   > Pro účely tohoto kurzu používáme [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), ale můžete si zvolit i [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). 

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:
- Nejnovější verze aplikace [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
- Nejnovější verze [BCP a SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433)

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL

Databáze SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků a prostředků úložiště](sql-database-service-tiers-dtu.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru Azure SQL Database](sql-database-features.md). 

Pomocí následujících kroků vytvořte prázdnou databázi SQL. 

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

3. Vyplňte formulář databáze SQL pomocí následujících informací, jak je vidět na předchozím obrázku:   

   | Nastavení       | Navrhovaná hodnota | Popis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Název databáze** | mySampleDatabase | Platné názvy databází najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Předplatné** | Vaše předplatné  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Klikněte na **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace: 

   | Nastavení       | Navrhovaná hodnota | Popis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
   | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

   ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klikněte na **Vybrat**.

6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Prozkoumejte možnosti počtu DTU nebo virtuálních jader a velikosti úložiště, které máte k dispozici na jednotlivých úrovních služby. Pro účely tohoto kurzu používáme [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), ale můžete si zvolit i [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). 

7. Pro účely tohoto kurzu vyberte úroveň služby **Standard** a potom pomocí posuvníku vyberte **100 DTU (S3)** a **400** GB úložiště.

   ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Přijměte podmínky verze Preview pro použití možnosti **Doplňkové úložiště**. 

   > [!IMPORTANT]
   > Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou následujících: Velká Británie – sever, USA – středozápad, Velká Británie – jih 2, Čína – východ, USDoD – střed, Německo – střed, USDoD – východ, US Gov – jihozápad, US Gov (střed) – jih, Německo – severovýchod, Čína – sever, USGov – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

9. Po výběru úrovně služby, počtu DTU a velikosti úložiště klikněte na **Použít**.  

10. Vyberte **kolaci** pro prázdnou databázi (pro účely tohoto kurzu použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](https://docs.microsoft.com/sql/t-sql/statements/collations).

11. Po vyplnění formuláře pro SQL Database klikněte na **Vytvořit** a databázi zřiďte. Zřizování trvá několik minut. 

12. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.
    
     ![oznámení](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Database vytvoří bránu firewall na úrovni serveru, aby zabránila externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru, pokud není vytvořené pravidlo brány firewall k otevření brány firewall pro konkrétní IP adresy. Postupujte podle těchto kroků a vytvořte [pravidlo brány firewall na úrovni serveru služby SQL Database](sql-database-firewall-configure.md) pro vaši IP adresu klienta a umožněte externí připojení přes bránu firewall služby SQL Database pouze pro vaši IP adresu. 

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

1. Po dokončení nasazení klikněte na **Databáze SQL** z nabídky na levé straně a klikněte na **mySampleDatabase** na stránce **Databáze SQL**. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20170824.database.windows.net**) a možnosti pro další konfiguraci. 

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následných kurzech a rychlých startech. 

   ![název serveru](./media/sql-database-get-started-portal/server-name.png) 

3. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Klikněte na **Přidat IP adresu klienta** na panelu nástrojů a přidejte svoji aktuální IP adresu do nového pravidla brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Nyní se můžete z této IP adresy připojit k serveru SQL Database a jeho databázím pomocí aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru použitím účtu správce serveru vytvořeného dříve.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

## <a name="sql-server-connection-information"></a>Informace o připojení k SQL serveru

Na webu Azure Portal získejte plně kvalifikovaný název serveru služby Azure SQL Database. Plně kvalifikovaný název serveru použijete k připojení k serveru pomocí aplikace SQL Server Management Studio.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **Databáze SQL** a na stránce **Databáze SQL** klikněte na vaši databázi. 
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**.

   ![informace o připojení](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS

Pomocí aplikace [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) navažte připojení ke svému serveru Azure SQL Database.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Název by měl vypadat přibližně takto: **mynewserver20170824.database.windows.net**. |
   | Authentication | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme v tomto kurzu nakonfigurovali. |
   | Přihlásit | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |

   ![Připojení k serveru](./media/sql-database-connect-query-ssms/connect.png)

3. Klikněte na **Možnosti** v dialogovém okně **Připojit k serveru**. V části **Připojit k databázi** zadejte **mySampleDatabase**, abyste se připojili k této databázi.

   ![připojení k databázi na serveru](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Klikněte na **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

5. V Průzkumníku objektů zobrazte objekty v ukázkové databázi rozbalením **Databáze** a potom **mySampleDatabase**.

   ![databázové objekty](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi 

Vytvořte schéma databáze se čtyřmi tabulkami, které modelují systém správy studentů univerzity, pomocí [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Person (Osoba)
- Course (Kurz)
- Student
- Credit (Kredit) – tabulka, která modeluje systém správy studentů pro univerzity

Následující diagram znázorňuje, jak spolu tyto tabulky vzájemně souvisejí. Některé z těchto tabulek odkazují na sloupce v jiných tabulkách. Například tabulka Student odkazuje na sloupec **PersonId** v tabulce **Person**. Prohlédněte si diagram, abyste pochopili, jak spolu tabulky v tomto kurzu souvisejí. Podrobný rozbor toho, jak vytvářet efektivní databázové tabulky, najdete v tématu [Vytváření efektivních databázových tabulek](https://msdn.microsoft.com/library/cc505842.aspx). Informace o výběru datových typů najdete v tématu [Datové typy](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tabulky můžete také vytvářet a navrhovat pomocí [Návrháře tabulky v aplikaci SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools). 

![Vztahy mezi tabulkami](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. V Průzkumníku objektů klikněte pravým tlačítkem na **mySampleDatabase** a potom klikněte na **Nový dotaz**. Otevře se prázdné okno dotazu připojené k vaší databázi.

2. V okně dotazu vytvořte v databázi čtyři tabulky spuštěním následujícího dotazu: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Vytvoření tabulek](./media/sql-database-design-first-database/create-tables.png)

3. Rozbalením uzlu „tables“ v Průzkumníku objektů aplikace SQL Server Management Studio zobrazíte tabulky, které jste vytvořili.

   ![Vytvořené tabulky aplikace SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

1. Ve složce Downloads vytvořte složku s názvem **SampleTableData**, kam se budou ukládat ukázková data vaší databáze. 

2. Klikněte pravým tlačítkem myši na následující odkazy a uložte je do složky **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otevřete okno příkazového řádku a přejděte do složky SampleTableData.

4. Spuštěním následujících příkazů vložte ukázková data do tabulek a zároveň nahraďte hodnoty položek **ServerName** (Název serveru), **DatabaseName** (Název databáze), **UserName** (Uživatelské jméno) a **Password** (Heslo) hodnotami pro vaše prostředí.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Teď jste načetli ukázková data do tabulek, které jste předtím vytvořili.

## <a name="query-data"></a>Dotazování dat

Informace z databázových tabulek můžete načíst spuštěním následujících dotazů. Další informace o psaní dotazů SQL najdete v tématu [Psaní dotazů SQL](https://technet.microsoft.com/library/bb264565.aspx). První dotaz spojí všechny čtyři tabulky a vyhledá studenty, jejichž učitelem je „Dominick Pope“ a kteří mají v jeho třídě známku vyšší než 75 %. Druhý dotaz spojí všechny čtyři tabulky a vyhledá všechny kurzy, do kterých byl někdy zapsaný „Noe Coleman“.

1. V okně dotazu aplikace SQL Server Management Studio spusťte následující dotaz:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. V okně dotazu aplikace SQL Server Management Studio spusťte následující dotaz:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Další kroky 
V tomto kurzu jste se naučili základním úkonům při práci s databází, jako je vytvoření databáze a tabulek, načtení a dotazování dat a obnovení databáze k určitému bodu v čase. Naučili jste se tyto postupy:
> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí aplikace [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Vytvoření tabulek
> * Hromadné načtení dat
> * Dotazování těchto dat

V následujícím kurzu se dozvíte, jak navrhnout databázi pomocí sady Visual Studio a jazyka C#.

> [!div class="nextstepaction"]
>[Návrh databáze SQL Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
