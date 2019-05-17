---
title: Nejčastější dotazy k Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tento článek uvádí si nejčastější dotazy ohledně Azure SQL Data Warehouse od zákazníků a vývojářů
services: sql-data-warehouse
author: happynicolle
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: nicw
ms.reviewer: igorstan
ms.openlocfilehash: c16d95ea15fc358cb81b17b42570cb35f2e8c52d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795554"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse nejčastější dotazy

## <a name="general"></a>Obecné

Otázka: Co SQL data Warehouse nabízí zabezpečení dat?

A. SQL data Warehouse nabízí několik řešení pro ochranu dat, jako je transparentní šifrování dat a auditování. Další informace najdete v tématu [zabezpečení].

Otázka: Kde najdu jaké standardy právní nebo business se SQL data Warehouse kompatibilní s?

A. Přejděte [Microsoft Compliance] stránky pro různé nabídky dodržování předpisů podle produktu, jako je například SOC a ISO. Nejprve zvolte podle názvu dodržování předpisů a potom rozbalte Azure v části služby Microsoft cloud v oboru na pravé straně stránky a zjistit, jaké služby jsou Azure services jsou kompatibilní.

Otázka: Můžete připojit k Power BI?

A. Ano! Když Power BI podporuje přímých dotazů s SQL data Warehouse, není určena pro velký počet uživatelů nebo dat v reálném čase. Pro produkční použití Power BI doporučujeme používat Power BI nad Azure Analysis Services nebo analýzy služby IaaS. 

Otázka: Jaké jsou limity kapacity datového skladu SQL?

A. V tématu naše aktuální [limity kapacity] stránky. 

Otázka: Proč se Moje Škálovací/pozastavit/obnovit trvá tak dlouho?

A. Různé faktory mohou mít vliv na dobu výpočetní operace správy. Společný malá a velká pro dlouho běžící operací je transakční vrácení zpět. Při zahájení operace škálování nebo pozastavit, jsou blokovány všechny příchozí relace a jsou Vyprázdněné dotazy. Pokud chcete nechat systém stabilní, musí být transakce vráceny zpět před započetím operace. Větší číslo a větší velikost protokolu transakcí, tím déle operace bude být zastaven a proces obnovení systému do stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

Otázka: Mám žádost o funkci, kde ji odeslat?

A. Pokud máte žádost o funkci, odeslat ho na naše [UserVoice] stránky

Otázka: Jak to mohu provést x?

A. Pro pomoc při vývoji s využitím SQL Data Warehouse, můžete klást dotazy na naše [Stack Overflow] stránky. 

Otázka: Jak odeslat lístek podpory?

A. [Lístky žádostí o podporu] podává prostřednictvím webu Azure portal.

## <a name="sql-languagefeature-support"></a>Podpora jazyka nebo funkci SQL 

Otázka: Jaké datové typy SQL Data Warehouse podporuje?

A. SQL Data Warehouse viz [datové typy].

Otázka: Jaké funkce tabulky podporujete?

A. I když SQL Data Warehouse podporuje mnoho funkcí, některé nejsou podporovány a jsou popsány v [Nepodporovaná tabulka funkce].

## <a name="tooling-and-administration"></a>Nástroje a Správa

Otázka: Podporujete databázové projekty v sadě Visual Studio.

A. Aktuálně nepodporujeme databázové projekty v sadě Visual Studio pro službu SQL Data Warehouse. Pokud chcete přetypovat hlas získat tuto funkci, navštivte naši User Voice [Žádost o funkci databázové projekty].

Otázka: SQL Data Warehouse podporuje rozhraní REST API?

A. Ano. Co nejvíce funkcí REST, který lze použít s SQL Database je také dostupná v SQL Data Warehouse. Můžete najít informace o rozhraní API v rámci stránky dokumentace k REST nebo [MSDN].


## <a name="loading"></a>Načítání

Otázka: Jaké ovladače klienta podporujete?

A. Podpora ovladačů pro datový Sklad můžete najít na [připojovací řetězce] stránky

Otázka: Které formáty souborů jsou podporovány službou SQL Data Warehouse polybase?

Odpověď: Orc, RC, Parquet nebo bez stromové struktury text s oddělovači

Otázka: Co můžu připojit k z SQL data Warehouse pomocí PolyBase? 

Odpověď: [Azure Data Lake Store] a [Azure Storage Blobs]

Otázka: Při připojování ke službě Azure Storage Blobs nebo ADLS, je výpočet přenosu směrem dolů je to možné? 

Odpověď: SQL data Warehouse PolyBase Ne, pracuje pouze komponenty úložiště. 

Otázka: Můžu připojit ke službě Hdinsight?

Odpověď: HDI můžete použít WASB nebo ADLS jako vrstva HDFS. Pokud máte jako vrstva HDFS, můžete načíst data do SQL data Warehouse. Však nelze generovat výpočtu přenosu směrem dolů k instanci HDI. 

## <a name="next-steps"></a>Další postup
Další informace o SQL Data Warehouse jako celek, najdete v našich [přehled] stránky.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Připojovací řetězce]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Lístky žádostí o podporu]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Zabezpečení]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limity kapacity]: ./sql-data-warehouse-service-capacity-limits.md
[datové typy]: ./sql-data-warehouse-tables-data-types.md
[Nepodporovaná tabulka funkce]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Žádost o funkci databázové projekty]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Přehled]: ./sql-data-warehouse-overview-faq.md
