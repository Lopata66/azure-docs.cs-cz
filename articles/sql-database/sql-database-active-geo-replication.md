---
title: Aktivní geografická replikace – Azure SQL Database | Dokumentace Microsoftu
description: Pomocí aktivní geografické replikace ve stejném nebo jiném datovém centru (oblast) vytvoříte čitelné sekundární databáze jednotlivých databází.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 07/09/2019
ms.openlocfilehash: 4b525c3cbea600859106062ed34dc6df9622dec5
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807304"
---
# <a name="creating-and-using-active-geo-replication"></a>Vytváření a používání aktivní geografické replikace

Aktivní geografická replikace je funkce Azure SQL Database, která vám umožní vytvořit čitelné sekundární databáze jednotlivých databází na serveru služby SQL Database ve stejném nebo jiném datovém centru (oblast).

> [!NOTE]
> Aktivní geografická replikace není podporována spravované instance. Geografické převzetí služeb při selhání spravované instance, použijte [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md).

Aktivní geografická replikace je navržená jako řešení obchodní kontinuity podnikových procesů, které umožňuje aplikaci provést zotavení po havárii rychle jednotlivé databáze v případě regionálních havárie nebo odstávky velkého rozsahu. Pokud je povolené geografickou replikaci, aplikaci můžete spustit převzetí služeb při selhání do sekundární databáze v jiné oblasti Azure. V jedné nebo několika oblastech se podporují až čtyři sekundární databáze a sekundárních replik lze také pro dotazy přístup jen pro čtení. Převzetí služeb při selhání musí ručně zahájit uživatel nebo aplikace. Po převzetí služeb při selhání má nový primární koncový bod jiné připojení. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí aktivní geografické replikace.

![Aktivní geografická replikace](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database podporuje také skupiny automatické převzetí služeb při selhání. Další informace najdete v tématu pomocí [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md). Navíc aktivní geografická replikace není podporována pro databáze vytvořené v rámci Managed Instance. Zvažte použití [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) pomocí spravovaných instancí.

Pokud některý z důvodu vaše primární databáze selže nebo potřebuje pouze do režimu offline, můžete spustit převzetí služeb při selhání pro žádnou z vaší sekundární databází. Při aktivaci převzetí služeb při selhání na jednu sekundární databází, všechny ostatní sekundární repliky jsou automaticky propojení se nový primární.

Může spravovat replikaci a převzetí služeb při selhání jednotlivých databází nebo sadu databází na serveru nebo do elastického fondu pomocí aktivní geografické replikace. Můžete provést, že při použití:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Izolované databáze](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastický fond](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Elastický fond nebo izolovanou databázi](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Izolované databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktivní geografická replikace využívá [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologie SQL serveru na asynchronní potvrzené transakce u primární databáze replikace do sekundární databáze pomocí izolace snímku. Automatické převzetí služeb při selhání skupiny poskytují sémantiku skupiny nad aktivní geografickou replikaci, ale používá stejné mechanismu asynchronní replikace. Když v libovolném časovém okamžiku sekundární databáze může být mírně za primární databáze, je zaručeno, že sekundární data nikdy nemůžete mít částečné transakce. Redundance mezi oblastmi umožňuje aplikacím rychle obnoven k trvalé ztrátě celého datového centra nebo částí datacentra způsobené přírodními katastrofami, katastrofální lidské chyby nebo škodlivý funguje. Konkrétní cíle bodu obnovení dat lze nalézt v [přehled kontinuity](sql-database-business-continuity.md).

> [!NOTE]
> Pokud dojde k chybě sítě mezi dvěma oblastmi, jsme opakovat každých 10 sekund a znovu navázat spojení.
> [!IMPORTANT]
> Pokud chcete zajistit, že důležité změny u primární databáze se replikují do sekundární lokality před převezmete služby při selhání, můžete vynutit synchronizaci k zajištění replikace důležité změny (například aktualizace hesel). Vynucené synchronizace ovlivňuje výkon, protože blokuje volající vlákno, dokud se replikují všechny potvrzené transakce. Podrobnosti najdete v tématu [uložená procedura sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Monitorování je zpoždění replikace mezi primární databází a geo-secondary najdete v tématu [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Následující obrázek znázorňuje příklad aktivní geografickou replikaci nakonfigurovaná s primárním v oblasti střed USA – sever a sekundární v oblasti střed USA – jih.

![relaci geografické replikace](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Protože jsou čitelné sekundární databáze, můžete použít k přesměrování zpracování úlohy jen pro čtení jako úlohy vytváření sestav. Pokud používáte aktivní geografickou replikaci, je možné vytvořit sekundární databáze ve stejné oblasti s primární, ale nezvyšuje katastrofických selhání odolnost aplikace. Pokud používáte skupiny automatické převzetí služeb při selhání, se vždy vytvoří sekundární databáze v jiné oblasti.

Kromě po havárii je možné obnovení aktivní geografickou replikaci v následujících scénářích:

- **Migrace databáze**: Aktivní geografická replikace můžete použít k migraci databáze z jednoho serveru na jiný online s minimálními výpadky.
- **Upgrady aplikací**: Můžete vytvořit další sekundární jako kopii zpětného navrácení služeb po během upgradu aplikace.

Pro dosažení skutečné obchodní kontinuity podnikových procesů, je přidání redundance databáze mezi datacentry pouze část řešení. Obnovení aplikaci (služba) end až do konce po závažnému selhání vyžaduje obnovení všech komponent, které tvoří službu a všechny závislé služby. Příklady těchto komponent: klientský software (například prohlížeč s vlastní JavaScriptu), webových front-endů, úložiště a DNS. Je velmi důležité, že všechny komponenty jsou odolné vůči selhání stejné a budou k dispozici v rámci plánovaná doba obnovení (RTO) vaší aplikace. Proto budete muset Identifikujte všechny závislé služby a pochopit, záruky a možnosti, které poskytují. Poté je nutné provést odpovídající kroky zajistit, aby vaše funkce služeb během převzetí služeb při selhání služby, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [návrhu cloudových řešení pro použití pro zotavení po havárii aktivní geografickou replikaci](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktivní geografická replikace terminologie a možnosti

- **Automatická asynchronní replikace**

  Sekundární databázi lze vytvořit pouze tak, že přidáte do existující databáze. Sekundární lze vytvořit v libovolné serveru Azure SQL Database. Po vytvoření sekundární databáze se vyplní daty zkopírovanými z primární databáze. Tento proces se označuje jako synchronizace replik indexů. Jakmile sekundární databáze se vytvoří a nasadí, aktualizace na primární databázi se asynchronně replikují do sekundární databáze automaticky. Asynchronní replikace znamená, že transakce usilujeme o to u primární databáze předtím, než se replikují do sekundární databáze.

- **Sekundární databáze pro čtení**

  Aplikace můžete přistupovat k sekundární databáze pro operace jen pro čtení pomocí objektů stejného nebo jiného zabezpečení použít pro přístup k primární databáze. Sekundární databáze, pracují v režimu izolace snímku zajistit replikaci aktualizací primární (protokol opětovného přehrání) není opožděná. dotazy se prováděné na sekundární.

> [!NOTE]
> Protokol opětovného přehrání je zpožděno v sekundární databázi, pokud nejsou aktualizace schématu na primárním. Druhá možnost vyžaduje schéma zámek na sekundární databázi.
> [!IMPORTANT]
> Geografická replikace můžete použít k vytvoření sekundární databáze ve stejné oblasti jako primární. Tato sekundární – nástroj pro vyrovnávání zatížení jen pro čtení úlohám, které můžete použít ve stejné oblasti. Sekundární databáze ve stejné oblasti, ale neposkytuje další odolnosti a proto není cílem vhodný převzetí služeb při selhání pro zotavení po havárii. Také nezaručuje avaialability zóny izolace. Použijte pro důležité obchodní informace nebo úrovně Premium s [redundantní konfigurace](sql-database-high-availability.md#zone-redundant-configuration) dosáhnout izolace avaialability zóny.   
>

- **Plánované převzetí služeb při selhání**

  Plánované převzetí služeb při selhání přepínače role primární a sekundární databáze po dokončení úplné synchronizace. Je online operaci, která nemá za následek ztrátu dat. Čas operace závisí na velikosti protokolů transakcí na primární, kterou je potřeba synchronizovat. Plánované převzetí služeb při selhání je určen pro následující scénáře: (a) provést zotavení po Havárii operací v produkčním prostředí po ztrátě dat je nepřijatelné; (b) k přesunutí databáze do jiné oblasti; a (c) Chcete-li databázi vrátit do primární oblasti, až se výpadek zmírnit (navrácení služeb po obnovení).

- **Neplánované převzetí služeb při selhání**

  Vynucené nebo neplánované převzetí služeb při selhání se okamžitě přepne sekundární do primární role bez jakékoli synchronizace s primární. Jakékoli transakce potvrzena na primární, ale nejsou replikovány na sekundární se ztratí. Tato operace je navržen jako způsob obnovení během výpadků, pokud primární není dostupný, ale musíte rychle obnovit databázi dostupnosti. Pokud původní primární je zpátky do online režimu bude automaticky znovu připojit a stát nový sekundární. Všechny nesynchronizované transakce před převzetí služeb při selhání se zachovají v záložním souboru, ale nebudou synchronizovat s novou primární, aby nedocházelo ke konfliktům. Tyto transakce budou muset ručně sloučit s nejnovější verzí primární databáze.
 
- **Více čitelné sekundární databáze**

  Pro každý primární můžete vytvořit až 4 sekundárních databází. Pokud existuje pouze jedna sekundární databáze a selže, aplikace je přístupný vyšší riziko dokud se vytvoří nová sekundární databáze. Existuje více sekundární databází, i nadále aplikace chráněn, i v případě, že jedna sekundární databáze se nezdaří. Lze také použít další sekundární databáze k horizontálnímu navýšení kapacity úloh jen pro čtení

  > [!NOTE]
  > Pokud používáte aktivní geografickou replikaci vytvářet globálně distribuované aplikace a potřebují mít možnost poskytnout přístup jen pro čtení k datům ve více než čtyř oblastí, můžete vytvořit sekundární sekundární databáze (proces označovaný jako řetězení). Tímto způsobem můžete dosáhnout téměř neomezené škálování replikace databáze. Kromě toho řetězení snižuje režijní náklady replikace z primární databáze. Nutný kompromis je prodleva zvýšenou replikace u sekundárních databází nejvíce listů.

- **Geografická replikace databáze v elastickém fondu**

  Každá sekundární databáze můžete samostatně součástí elastického fondu nebo nesmí být v každém elastického fondu vůbec. Volba fond pro každou sekundární databázi je oddělená a nezávisí na konfiguraci všechny sekundární databáze (ať už primární nebo sekundární). Každý elastický fond je obsažen v rámci jedné oblasti, proto může sdílet více druhotných databází na stejném topologie nikdy elastického fondu.


- **Řízené uživatelem převzetí služeb při selhání a navrácení služeb po obnovení**

  Sekundární databáze lze explicitně přepnout do primární role v každém okamžiku aplikace nebo uživatele. Během výpadku skutečné "neplánované" možnost by měla sloužit, která okamžitě podporuje sekundární jako primární. Při selhání primární obnoví a je opět k dispozici, systém automaticky označí jako sekundární obnovené primární a ji používalo nový primární. Protože potřebujeme asynchronní replikaci malé množství dat může dojít ke ztrátě během neplánované převzetí služeb při selhání selže primární před replikuje nejnovější změny do sekundární. Když primární s více sekundárních replik převezme služby při selhání, systém automaticky změní konfiguraci relace replikace a odkazy zbývající sekundární databáze na primární připojovaly bez nutnosti zásahu uživatele. Po vyřešení výpadek, který způsobil převzetí služeb při selhání může být žádoucí aplikace co nejdříve do primární oblasti. K tomuto účelu by měl vyvolat příkaz převzetí služeb při selhání s parametrem "plánovanou".

## <a name="preparing-secondary-database-for-failover"></a>Příprava na převzetí služeb při selhání do sekundární databáze

Aby bylo zajištěno, že vaše aplikace můžete okamžitě přístup k nové primární po převzetí služeb při selhání, zajistěte, aby že požadavky na ověřování pro sekundární server a databáze jsou správně nakonfigurované. Podrobnosti najdete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). Pokud chcete zajistit dodržování předpisů po převzetí služeb při selhání, ujistěte se, že zásady uchovávání záloh na sekundární databázi se shoduje s primárním. Tato nastavení nejsou součástí databáze a nebudou se replikovat. Ve výchozím nastavení se nakonfigurují sekundární s dobou uchování PITR výchozí sedmi dnů. Podrobnosti najdete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).

## <a name="configuring-secondary-database"></a>Konfigurace aktivní sekundární databáze

Primární a sekundární databáze musí mít stejné úrovně služeb. Také důrazně doporučujeme, že se vytvoří sekundární databáze se stejnou velikostí výpočetní prostředky (počet jednotek Dtu nebo virtuálních jader) jako primární. Pokud primární databáze je zápis náročné úlohy, nemusí být schopné udržovat tempo s ním sekundárního objektu s nižší výpočetní velikost. To způsobí zpoždění opakování nedostupnost sekundární a potenciální. U sekundární databáze, která zaostává za primární databází, také hrozí rozsáhlá ztráta dat v případě, že je potřeba provést vynucené převzetí služeb při selhání. Pokud chcete toto riziko omezit, efektivní aktivní geografické replikace bude míra primární protokolu umožňující její sekundární databáze dohnat. Důsledkem imbalanced sekundární konfigurace je, že po převzetí služeb při selhání se výkon vaší aplikace mají z důvodu nedostatečné výpočetní kapacitu nový primární. Je potřeba upgradovat na vyšší výpočetní prostředky potřebné úrovně, které nebude možné, dokud je zmírněna výpadek. 


> [!IMPORTANT]
> Publikované cíle bodu obnovení = 5 s nemůže být zaručena, pokud je nakonfigurovaná sekundární databáze se stejnou velikostí výpočetních jako primární. 


Pokud se rozhodnete vytvořit sekundární s menší velikostí výpočetních, graf procento protokolu vstupně-výstupních operací na portálu Azure portal poskytuje vhodný způsob, jak odhadovat velikost minimální výpočetní sekundární databáze, která je nutná pro udržení zatížení replikace. Například, pokud primární databáze je P6 (1 000 DTU) a jeho protokolem procent vstupně-výstupních operací je sekundární musí být nejméně 50 % P4 (500 DTU). Můžete také načíst data protokolu vstupně-výstupních operací s využitím [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nebo [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databáze zobrazení.  Omezení se hlásí jako HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO stavu čekání [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) databáze zobrazení. 

Další informace o velikostech výpočetních SQL Database najdete v tématu [co jsou úrovně služby SQL Database](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Udržování synchronizace přihlašovacích údajů a pravidla brány firewall

Doporučujeme používat [pravidla firewallu protokolu IP na úrovni databáze](sql-database-firewall-configure.md) pro geograficky replikované databáze, takže tato pravidla se dají replikovat s databází a zkontrolujte všechny sekundární databáze mají stejné pravidla firewallu protokolu IP jako primární. Tento přístup se eliminuje potřeba zákazníkům ručně konfigurovat a spravovat pravidla brány firewall na servery, které hostují jak primární a sekundární databází. Podobně použití [uživatelé databáze s omezením](sql-database-manage-logins.md) dat přístup zajistí primární i sekundární databáze vždy stejné přihlašovací údaje uživatele, takže při selhání, není k dispozici žádné přerušení z důvodu neshody se uživatelská jména a hesla. Přidání [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), zákazníci můžou spravovat přístup uživatelů k primární i sekundární databáze a eliminují ke správě pověření v databázích úplně se vynechá.

## <a name="upgrading-or-downgrading-primary-database"></a>Upgrade nebo při downgradu primární databáze

Můžete upgradovat nebo downgradovat primární databáze do různých výpočetních velikost (v rámci stejné úrovně služeb, ne mezi pro obecné účely a pro důležité obchodní informace) bez odpojení všechny sekundární databáze. Při upgradu, doporučujeme nejprve upgradovat sekundární databáze a pak upgradovat primární. Při downgradu, pořadí: nejprve downgradovat primární a poté downgradovat sekundární. Když upgradujete nebo starší verzi databáze, kterou chcete vrstvu různé služby, se vynucuje toto doporučení.

> [!NOTE]
> Pokud jste vytvořili jako součást konfigurace skupiny převzetí služeb při selhání do sekundární databáze není doporučeno downgradovat sekundární databáze. Tím je zajištěno, že datová vrstva má dostatečnou kapacitu pro zpracování pravidelné zatížení po aktivaci převzetí služeb při selhání.

> [!IMPORTANT]
> Primární databáze ve skupině převzetí služeb při selhání nejde škálovat na vyšší úroveň, není-li sekundární databáze nejprve vertikálně kapacitu na vyšší úroveň. Pokud se pokusíte změnit velikost primární databáze před horizontálním sekundární databáze, může dojít k následující chybě:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

Z důvodu vysoké latenci sítě WAN průběžného kopírování používá mechanismu asynchronní replikace. Asynchronní replikace umožňuje ztrátu některých dat. nevyhnutelné Pokud dojde k chybě. Některé aplikace ale můžou vyžadovat bez ztráty. K ochraně těchto důležitých aktualizací, můžete volat vývojář aplikace [uložená procedura sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systému postup ihned po potvrzení transakce. Volání **uložená procedura sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud se poslední potvrzené transakce bylo přeneseno do sekundární databáze. Ale to nečeká přenášená transakcí, které chcete znovu přehrát a potvrzené na sekundární. **uložená procedura sp_wait_for_database_copy_sync** je vymezen na odkaz na konkrétní průběžného kopírování. Tento postup můžete volat každý uživatel s právy k připojení k primární databáze.

> [!NOTE]
> **uložená procedura sp_wait_for_database_copy_sync** zabrání ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro oprávnění ke čtení. Zpoždění způsobené **uložená procedura sp_wait_for_database_copy_sync** TDS může být významný a závisí na velikosti protokolů transakcí v době volání.

## <a name="monitoring-geo-replication-lag"></a>Monitorování prodleva geografické replikace

K monitorování prodleva s ohledem na cíle bodu obnovení, použijte *replication_lag_sec* sloupec [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) u primární databáze. Zobrazuje prodlevy v sekundách mezi transakcí na primární potvrzena a zachována na sekundární. Například Pokud hodnota je zpoždění je 1 sekunda, znamená to, pokud je v tomto okamžiku vliv výpadku primární a převzetí služeb při selhání se zahájí, 1 sekunda nejnovější přechody se neuloží. 

K měření prodleva s ohledem na změny u primární databáze, které byly použity na sekundárním, tedy k dispozici ke čtení ze sekundárního, porovnat *last_commit* čas na sekundární databázi se stejnou hodnotou na primární databáze.

> [!NOTE]
> Někdy *replication_lag_sec* u primární databáze má hodnotu NULL, což znamená, že primární nezná aktuálně jak daleko je sekundární.   To se obvykle stává po restartování procesu a měli byste být přechodné podmínky. Vezměte v úvahu upozornění aplikace, pokud *replication_lag_sec* vrátí hodnotu NULL pro delší dobu. By mohlo znamenat, že sekundární databáze nemůže komunikovat s primární z důvodu selhání trvalé připojení. Existují také podmínky, které by mohly způsobit rozdíl mezi *last_commit* dobu na sekundárním a v primární databázi, a jsou velké. Například Pokud se potvrzení změn se provádí na primárním po dlouhou dobu žádné změny, rozdíl přeskočí až po velké hodnoty před vrácením rychle na hodnotu 0. Považuje za chybový stav po dlouhou dobu zůstává velký rozdíl mezi těmito dvěma hodnotami.


## <a name="programmatically-managing-active-geo-replication"></a>Programová správa aktivní geografické replikace

Jak je popsáno výše, aktivní geografickou replikaci můžete také spravovat prostřednictvím kódu programu pomocí Azure Powershellu a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici. Aktivní geografická replikace zahrnuje sadu rozhraní API Azure Resource Manageru pro správu, včetně [REST API služby Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) a [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžaduje použití skupin prostředků a podporu zabezpečení na základě role (RBAC). Další informace o tom, jak implementovat přístup rolí, najdete v části [řízení přístupu](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Správa převzetí služeb při selhání jednoho a ve fondu databází

> [!IMPORTANT]
> Tyto příkazy jazyka Transact-SQL pouze platí pro aktivní geografickou replikaci a se nevztahují na skupiny převzetí služeb při selhání. V důsledku toho se také nevztahují na Managed instance, jak podporují pouze skupiny převzetí služeb při selhání.

| Příkaz | Popis |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Přidat sekundární SERVER ON argument použít k vytvoření sekundární databáze pro stávající databáze a spustí replikaci dat. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Pomocí převzetí služeb při selhání nebo FORCE_FAILOVER_ALLOW_DATA_LOSS můžete přepínat sekundární databázi na primární a zahájí tak převzetí služeb při selhání |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Můžete odebrat sekundární SERVER ON ukončí replikaci dat mezi SQL Database a zadanou sekundární databází. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Vrátí informace o veškerá existující propojení replikace pro každou databázi na serveru Azure SQL Database. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Získá čas poslední replikace, prodleva poslední replikace a další informace o propojení replikace pro danou databázi SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zobrazuje stav všech databázových operací včetně stav odkazů replikace. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |způsobí, že aplikace počkejte, dokud všechny potvrzené transakce jsou replikována a projdou potvrzením většinovým aktivní sekundární databáze. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Správa převzetí služeb při selhání jednoho a ve fondu databází

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

| Rutiny | Popis |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
|  | |

> [!IMPORTANT]
> Ukázky skriptů, najdete v části [konfigurace a převzetí služeb při selhání izolované databáze pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) a [konfigurace a převzetí služeb při selhání pomocí aktivní geografické replikace databázi ve fondu](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Správa převzetí služeb při selhání jednoho a ve fondu databází

| rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat databázi (createMode = obnovení)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vytvoří, aktualizuje nebo obnoví primární nebo sekundární databáze. |
| [Získat vytvořit nebo aktualizovat stav databáze](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vrátí stav během operace vytvoření. |
| [Nastavit jako primární (plánované převzetí služeb při selhání) sekundární databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Nastaví, které do sekundární databáze je primární podle přebírání služeb při selhání z aktuální primární databáze. **Tato možnost není podporována pro Managed Instance.**|
| [Nastavit jako primární (neplánované převzetí služeb při selhání) sekundární databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Nastaví, které do sekundární databáze je primární podle přebírání služeb při selhání z aktuální primární databáze. Tato operace může dojít ke ztrátě. **Tato možnost není podporována pro Managed Instance.**|
| [Získání odkazu replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Získá konkrétní replikační připojení pro danou databázi SQL ve spolupráci geografickou replikaci. Načte informace v zobrazení katalogu sys.geo_replication_links viditelné. **Tato možnost není podporována pro Managed Instance.**|
| [Propojení replikace - seznam podle databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Získá všechny odkazy replikace pro danou databázi SQL ve spolupráci geografickou replikaci. Načte informace v zobrazení katalogu sys.geo_replication_links viditelné. |
| [Odstranit propojení replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Odstraní propojení replikace databáze. Nelze provést během převzetí služeb při selhání. |
|  | |

## <a name="next-steps"></a>Další postup

- Ukázky skriptů najdete tady:
  - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database podporuje také skupiny automatické převzetí služeb při selhání. Další informace najdete v tématu pomocí [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md).
- Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md)
- Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
- Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovení databáze ze záloh spouštěných službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování pro novým primárním serverem a databází, naleznete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).
