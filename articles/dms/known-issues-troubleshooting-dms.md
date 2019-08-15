---
title: Článek o známých problémech s odstraňováním potíží/chyb souvisejících s používáním Azure Database Migration Service | Microsoft Docs
description: Přečtěte si, jak řešit běžné známé problémy a chyby spojené s používáním Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: a72a0e26c2af34942faaa6a7e62feef86082e196
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034832"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Řešení běžných problémů s Azure Database Migration Service a chyb

Tento článek popisuje některé běžné problémy a chyby, které Azure Database Migration Service uživatelé mohou nacházet v rámci. Článek obsahuje také informace o tom, jak tyto problémy a chyby vyřešit.

## <a name="migration-activity-in-queued-state"></a>Aktivita migrace ve stavu zařazeném do fronty

Když vytvoříte nové aktivity v Azure Database Migration Service projektu, aktivity zůstanou ve stavu zařazeném do fronty.

| Příčina         | Řešení |
| ------------- | ------------- |
| K tomuto problému dochází, když Azure Database Migration Service instance dosáhla maximální kapacity pro probíhající úlohy, které se souběžně spouštějí. Jakékoli nové aktivity jsou zařazené do fronty, dokud nebude kapacita k dispozici. | Ověří, jestli instance služby migrace dat spouští aktivity napříč projekty. Můžete dál vytvářet nové aktivity, které se automaticky přidají do fronty ke spuštění. Jakmile se dokončí jakákoli existující spuštěná činnost, spustí se další aktivita ve frontě a stav se automaticky změní na stav spuštěno. Pro spuštění migrace aktivity zařazené do fronty není nutné provádět žádnou další akci.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximální počet databází vybraných pro migraci

Při vytváření aktivity pro projekt migrace databáze pro přesun do Azure SQL Database nebo spravované instance Azure SQL Database došlo k následující chybě:

* **Chyba:** Chyba ověření nastavení migrace "," errorDetail ":" pro migraci bylo vybráno více než maximální počet objektů ' databáze '. "

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, pokud jste pro jednu migrační aktivitu vybrali více než čtyři databáze. V současnosti jsou jednotlivé aktivity migrace omezené na čtyři databáze. | Vyberte čtyři nebo méně databází na jednu aktivitu migrace. Pokud potřebujete migrovat více než čtyři databáze paralelně, zajistěte další instanci Azure Database Migration Service. V současné době každé předplatné podporuje až dvě instance Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Chyby při migraci MySQL do Azure MySQL s chybami obnovení

Když migrujete z MySQL na Azure Database for MySQL pomocí Azure Database Migration Service, aktivita migrace se nezdařila s následující chybou:

* **Chyba:** Chyba migrace databáze – úkol ' TaskID ' byl pozastaven z důvodu neúspěšného selhání obnovení [n].

| Příčina         | Řešení |
| ------------- | ------------- |
| K této chybě může dojít, pokud uživatel, který provádí migraci, chybí ReplicationAdmin role nebo oprávnění klienta replikace, REPLIKy replikace a SUPER (verze starší než MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Ujistěte se, že jsou požadovaná [oprávnění](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) pro uživatelský účet přesně nakonfigurovaná na instanci Azure Database for MySQL. Pomocí následujících kroků můžete například vytvořit uživatele s názvem "migrateuser" s požadovanými oprávněními:<br>1. VYTVOŘIT uživatele migrateuser @ '% ' identifikovaný ' Secret '; <br>2. Udělte všechna oprávnění na db_name. * do ' migrateuser ' @ '% ' identifikovaného ' tajné '; zopakováním tohoto kroku udělíte přístup k více databázím. <br>3. Udělte replikaci na podřízeném počítači *.* do ' migrateuser ' @ '% ' identifikovaný ' Secret ';<br>4. Udělte klientovi replikace zapnutý *.* do ' migrateuser ' @ '% ' identifikovaný ' Secret ';<br>5. Vyprázdnit oprávnění; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Při pokusu o zastavení Azure Database Migration Service došlo k chybě.

Při zastavování instance Azure Database Migration Service se zobrazí následující chyba:

* **Chyba:** Zastavení služby se nezdařilo. Chyba: {'error':{'code':'InvalidRequest','message':'Aktuálně běží jedna nebo více aktivit. Pokud chcete službu zastavit, počkejte, až se aktivity dokončí, nebo tyto aktivity zastavte ručně a zkuste to znovu.}}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když instance služby, kterou se pokoušíte zastavit, zahrnuje aktivity, které jsou pořád spuštěné nebo přítomné v projektech migrace. <br><br><br><br><br><br> | Zajistěte, aby v instanci Azure Database Migration Service, kterou se pokoušíte zastavit, neběžely žádné aktivity. Před pokusem o zastavení služby můžete také odstranit aktivity nebo projekty. Následující postup ukazuje, jak odebrat projekty pro vyčištění instance služby migrace odstraněním všech spuštěných úloh:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. SELECT-AzureRmSubscription-Subscription "\<subname >" <br> 4. Remove-AzureRmDataMigrationProject-name \<ProjectName >-ResourceGroupName \<RgName >-ServiceName \<ServiceName >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Při pokusu o spuštění Azure Database Migration Service došlo k chybě.

Při spuštění instance Azure Database Migration Service se zobrazí následující chyba:

* **Chyba:** Spuštění služby se nezdařilo. Chyba: {' errorDetail ': službu se nepovedlo spustit, obraťte se prosím na podporu Microsoftu.}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když předchozí instance neproběhla interně. Tato chyba se vyskytuje zřídka a technický tým je o tom vědom. <br> | Odstraňte instanci služby, kterou nemůžete spustit, a pak ji zajistěte, aby ji nahradila nová. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Chyba při obnovování databáze při migraci SQL do spravované instance Azure SQL DB

Když provedete online migraci z SQL Server do spravované instance Azure SQL Database, přímou migraci se nezdařila s následující chybou:

* **Chyba:** Operace obnovení pro ID operace operationId se nezdařila. Kód ' AuthorizationFailed ', zpráva ' klient ' clientId ' s ID objektu ' objectId ' nemá autorizaci k provedení akce ' Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read ' over Scope '/Subscriptions/ subscriptionId.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tato chyba znamená, že instanční objekt, který se používá pro online migraci z SQL Server do spravované instance Azure SQL Database nemá oprávnění přispívat k tomuto předplatnému. Některá volání rozhraní API se spravovanými instancemi v současné době vyžadují toto oprávnění u předplatného pro operaci obnovení. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Pomocí rutiny `-ObjectId` prostředí PowerShell, která je k dispozici z chybové zprávy, zobrazte seznam zobrazovaných názvů používaných ID aplikace. `Get-AzureADServicePrincipal`<br><br> Ověřte oprávnění k této aplikaci a ujistěte se, že má [roli Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na úrovni předplatného. <br><br> Tým Azure Database Migration Service Engineering pracuje na omezení požadovaného přístupu z aktuální role Contribute v předplatném. Pokud máte obchodní požadavek, který nepovoluje použití role přispívání, požádejte o další pomoc podporu Azure. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Při odstraňování síťového rozhraní přidruženého k Azure Database Migration Service došlo k chybě.

Když se pokusíte odstranit síťovou kartu přidruženou k Azure Database Migration Service, pokus o odstranění se nezdaří s touto chybou:

* **Chyba:** Síťovou kartu přidruženou k Azure Database Migration Service nelze odstranit, protože služba DMS využívá síťovou kartu.

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když může být instance Azure Database Migration Service stále přítomná a spotřebovává síťovou kartu. <br><br><br><br><br><br><br><br> | Pokud chcete tuto síťovou kartu odstranit, odstraňte instanci služby DMS, která automaticky odstraní síťovou kartu, kterou používá služba.<br><br> **Důležité**informace: Ujistěte se, že odstraňovaná instance Azure Database Migration Service nemá žádné spuštěné aktivity.<br><br> Po odstranění všech projektů a aktivit přidružených k instanci Azure Database Migration Service můžete instanci služby odstranit. Síťové rozhraní používané instancí služby se automaticky vyčistí jako součást odstranění služby. |

## <a name="connection-error-when-using-expressroute"></a>Chyba připojení při použití ExpressRoute

Pokud se pokoušíte připojit ke zdroji v průvodci projektem služby Azure Database Migration Service a pokud zdroj k připojení využívá ExpressRoute, připojení po uplynutí prodlouženého časového limitu selže.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Při použití [ExpressRoute](https://azure.microsoft.com/services/expressroute/) [vyžaduje](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) Azure Database Migration Service zřízení tří koncových bodů služby v podsíti Virtual Network přidružené ke službě:<br> --Service Bus koncový bod<br> – Koncový bod úložiště<br> --Koncový bod cílové databáze (například koncový bod SQL, koncový bod Cosmos DB)<br><br><br><br><br> | [Povolte](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) pro připojení ExpressRoute mezi zdrojem a Azure Database Migration Service požadované koncové body služby. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Při migraci databáze MySQL do Azure DB pro MySQL se zobrazí chyba časového limitu čekání na uzamčení.

Když migrujete databázi MySQL do instance Azure Database for MySQL přes Azure Database Migration Service, migrace se nezdařila s následující chybou vypršení časového limitu pro čekání na uzamčení:

* **Chyba:** Chyba migrace databáze – nepovedlo se načíst soubor – nepovedlo se spustit proces načtení pro soubor n, RetCode: SQL_ERROR SqlState: HY000 NativeError: Zpráva 1205: [MySQL] [ovladač ODBC] [mysqld] překročení časového limitu čekání na zámek; Zkuste restartovat transakci.

| Příčina         | Řešení    |
| ------------- | ------------- |
| K této chybě dojde v případě, že migrace selhala kvůli vypršení časového limitu čekání na uzamčení. | Zvažte zvýšení hodnoty parametru serveru **' innodb_lock_wait_timeout '** . Nejvyšší povolená hodnota je 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Při připojování ke zdrojovému SQL Server při použití dynamického portu nebo pojmenované instance došlo k chybě.

Když se pokusíte připojit Azure Database Migration Service k SQL Servermu zdroji, který běží na pojmenované instanci nebo na dynamickém portu, připojení se nezdařilo s touto chybou:

* **Chyba**:-1-připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení. zprostředkovatele Síťová rozhraní SQL, chyba: 26 – Chyba při hledání zadaného serveru/instance)

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když je instance zdrojového SQL Server, ke které se Azure Database Migration Service pokusí připojit, buď dynamický port, nebo používá pojmenovanou instanci. Služba SQL Server Browser naslouchá na portu UDP 1434 pro příchozí připojení k pojmenované instanci nebo při použití dynamického portu. Dynamický port se může změnit při každém restartování služby SQL Server. Dynamický port přiřazený k instanci můžete ověřit prostřednictvím konfigurace sítě v SQL Server Configuration Manager.<br><br><br> |Ověřte, že Azure Database Migration Service se může připojit ke zdrojové SQL Server Browser službě na portu UDP 1434 a instanci SQL Server prostřednictvím dynamicky přiřazeného portu TCP. |

## <a name="additional-known-issues"></a>Další známé problémy

* [Známé problémy/omezení migrace pro online migrace Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy/omezení migrace pro online migrace Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy/omezení migrace pro online migrace Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další postup

* Podívejte se na článek [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Seznamte se s [postupem konfigurace parametrů serveru v Azure Database for MySQL pomocí Azure Portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Seznam [požadavků pro použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)najdete v článku Přehled požadavků.
* Přečtěte si [Nejčastější dotazy týkající se použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
