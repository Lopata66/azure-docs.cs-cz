---
title: Začínáme s nástroji pro Elastic Database
description: Základní vysvětlení funkce Elastic Database Tools Azure SQL Database, včetně snadno spouštěné ukázkové aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76987018"
---
# <a name="get-started-with-elastic-database-tools"></a>Začínáme s nástroji pro Elastic Database

Tento dokument vás seznámí s vývojářským prostředím pro [klientskou knihovnu elastické databáze](sql-database-elastic-database-client-library.md) tím, že vám pomůže spustit ukázkovou aplikaci. Ukázková aplikace vytvoří jednoduchou aplikaci horizontálně dělené a prozkoumá klíčové funkce Azure SQL Database funkcí Elastic Database nástrojů. Zaměřuje se na případy použití pro [správu map horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md), [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)a [dotazování na více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md). Klientská knihovna je k dispozici pro rozhraní .NET i Java. 

## <a name="elastic-database-tools-for-java"></a>Elastic Database Tools for Java

### <a name="prerequisites"></a>Požadavky

* Sada Java Developer Kit (JDK), verze 1,8 nebo novější
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database Server v Azure nebo v místní instanci SQL Server

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace

Chcete-li vytvořit soubory JAR a začít s ukázkovým projektem, postupujte následovně: 
1. Naklonujte [úložiště GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) obsahující klientské knihovny společně s ukázkovou aplikací. 

2. Upravte soubor _./Sample/src/Main/Resources/Resource.Properties_ a nastavte následující:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Chcete-li vytvořit ukázkový projekt, v adresáři _./Sample_ spusťte následující příkaz:

    ```
    mvn install
    ```
    
4. Chcete-li spustit vzorový projekt, v adresáři _./Sample_ spusťte následující příkaz: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Chcete-li získat další informace o možnostech klientské knihovny, Experimentujte s různými možnostmi. Nebojte se prozkoumat kód, abyste se dozvěděli o implementaci ukázkové aplikace.

    ![Průběh – Java][5]
    
Blahopřejeme! Úspěšně jste vytvořili a spustili svou první aplikaci horizontálně dělené pomocí nástrojů Elastic Database v Azure SQL Database. Pomocí sady Visual Studio nebo SQL Server Management Studio se připojte ke svojí databázi SQL a Prohlédněte si horizontálních oddílů, kterou ukázka vytvořila. Všimnete si nových ukázkových databází horizontálních oddílů a databáze správce map horizontálních oddílů, kterou vytvořila ukázka. 

Chcete-li přidat knihovnu klienta do vlastního projektu Maven, přidejte do souboru POM následující závislost:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Elastic Database nástroje pro .NET

### <a name="prerequisites"></a>Požadavky

* Visual Studio 2012 nebo novější s C#. Stáhněte si bezplatnou verzi na webu [Stažení sady Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2,7 nebo novější. Pokud chcete získat nejnovější verzi, přečtěte si téma [instalace NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace

Knihovnu nainstalujete tak, že přejdete na [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Knihovna se instaluje s ukázkovou aplikací popsanou v následující části.

Chcete-li stáhnout a spustit ukázku, postupujte podle následujících kroků: 

1. Stáhněte si [ukázku nástrojů elastické databáze pro Azure SQL-Začínáme](https://github.com/Azure/elastic-db-tools). Rozbalte ukázku do zvoleného umístění.

2. Chcete-li vytvořit projekt, otevřete řešení *ElasticScaleStarterKit. sln* z adresáře *jazyka C#* .

3. V řešení pro ukázkový projekt otevřete soubor *App. config* . Potom podle pokynů v souboru přidejte název serveru Azure SQL Database a přihlašovací údaje (uživatelské jméno a heslo).

4. Sestavte a spusťte aplikaci. Po zobrazení výzvy umožněte aplikaci Visual Studio obnovit balíčky NuGet řešení. Tato akce stáhne nejnovější verzi klientské knihovny elastické databáze z NuGetu.

5. Chcete-li získat další informace o možnostech klientské knihovny, Experimentujte s různými možnostmi. Poznamenejte si kroky, které aplikace převezme ve výstupu konzoly, a nebojte se prozkoumat kód na pozadí.
   
    ![Průběh][4]

Blahopřejeme! Úspěšně jste vytvořili a spustili svou první aplikaci horizontálně dělené pomocí nástrojů Elastic Database v SQL Database. Pomocí sady Visual Studio nebo SQL Server Management Studio se připojte ke svojí databázi SQL a Prohlédněte si horizontálních oddílů, kterou ukázka vytvořila. Všimnete si nových ukázkových databází horizontálních oddílů a databáze správce map horizontálních oddílů, kterou vytvořila ukázka.

> [!IMPORTANT]
> Doporučujeme vždy používat nejnovější verzi Management Studio, abyste měli stále synchronizovaný s aktualizacemi Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Klíčové části Ukázky kódu

* **Správa map horizontálních oddílů a horizontálních oddílů**: kód ukazuje, jak pracovat s horizontálních oddílů, rozsahy a mapováními v souboru *ShardManagementUtils.cs* . Další informace najdete v tématu horizontální navýšení [kapacity databází pomocí Správce map horizontálních oddílů](https://go.microsoft.com/?linkid=9862595).  

* **Směrování závislé na datech**: směrování transakcí do pravého horizontálních oddílůu je zobrazeno v souboru *DataDependentRoutingSample.cs* . Další informace najdete v tématu [Směrování závislé na datech](https://go.microsoft.com/?linkid=9862596). 

* **Dotazování na více horizontálních oddílů**: dotazování napříč horizontálních oddílů je znázorněno v souboru *MultiShardQuerySample.cs* . Další informace najdete v tématu [dotazování multi-horizontálních oddílů](https://go.microsoft.com/?linkid=9862597).

* **Přidání prázdných horizontálních oddílů**: iterativní přidání nového prázdného horizontálních oddílů je provedeno kódem v souboru *CreateShardSample.cs* . Další informace najdete v tématu horizontální navýšení [kapacity databází pomocí Správce map horizontálních oddílů](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Jiné operace elastického škálování

* **Rozdělení stávajícího horizontálních oddílů**: schopnost rozdělit horizontálních oddílů je poskytována nástrojem pro dělení na více koncových sloučených. Další informace najdete v tématu [přesun dat mezi vysoce škálovatelnými cloudových databází](sql-database-elastic-scale-overview-split-and-merge.md).

* **Sloučení existujících horizontálních oddílů**: sloučení horizontálních oddílů se také provádí pomocí nástroje pro dělení na více koncových sloučených. Další informace najdete v tématu [přesun dat mezi vysoce škálovatelnými cloudových databází](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Náklady

Knihovna nástrojů Elastic Database je zadarmo. Při použití Elastic Databasech nástrojů se neúčtují žádné další poplatky nad náklady na využití Azure. 

Například ukázková aplikace vytvoří nové databáze. Náklady na tuto schopnost závisí na zvolené edici SQL Database a na využití Azure vaší aplikace.

Informace o cenách najdete v [podrobnostech o cenách SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další kroky

Další informace o Elastic Databasech nástrojích najdete v následujících článcích:

* Ukázky kódu: 
  * Elastic Database nástroje ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database Tools for Azure SQL-Entity Framework Integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elastická horizontálních oddílů v centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [oznámení elastického škálování](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanál 9: [video s přehledem elastického škálování](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskuzní fórum: [Azure SQL Database Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Měření výkonu: [čítače výkonu pro správce map horizontálních oddílů](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

