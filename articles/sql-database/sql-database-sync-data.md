---
title: Synchronizace dat Azure SQL | Dokumentace Microsoftu
description: Tento přehled zavádí synchronizace dat SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a887c79a51c7a239e7057171e51e67a53af2f84b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580222"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizace dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL

Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instance systému SQL Server.

> [!IMPORTANT]
> Azure SQL Data synchronizace provádí vložení změn **není** v tuto chvíli podporován Azure SQL Database Managed Instance.

## <a name="when-to-use-data-sync"></a>Kdy použít synchronizace dat

Synchronizace dat je užitečné v případech, kdy data musí být pořád aktuální napříč několika databázemi Azure SQL nebo databáze systému SQL Server. Tady jsou hlavní svědectví pro synchronizaci dat:

- **Hybridní synchronizace dat:** Pomocí synchronizace dat můžete zachovat data synchronizovat mezi vaším místním databázím a Azure SQL Database umožňuje hybridní aplikace. Tato funkce může odvolat zákazníkům, kteří jsou zvažujete Přesun do cloudu a chcete poskládali z jejich aplikace v Azure.
- **Distribuované aplikace:** V mnoha případech je užitečné oddělit různé úlohy napříč různými databázemi. Například pokud máte velké provozní databáze, ale budete potřebovat ke spuštění úlohy vytváření sestav, nebo analýzy těchto dat, je užitečné mít druhý databáze pro tento další úlohy. Tento přístup minimalizuje dopad na výkon na vaše produkční úlohy. Synchronizace dat můžete ponechat tyto dvě databáze synchronizované.
- **Globálně distribuované aplikace:** Řada podniků zahrnují několik oblastí a dokonce i v několika zemích. Kvůli minimalizaci latence sítě, je nejvhodnější pro vaše data v oblasti blízko vás. Pomocí synchronizace dat se snadnou vejdou databází v oblastech po celém světě synchronizované.

Synchronizace dat není preferovaným řešením v následujících scénářích:

| Scénář | Některé doporučená řešení. |
|----------|----------------------------|
| Zotavení po havárii | [Geograficky redundantní zálohy Azure](sql-database-automated-backups.md) |
| Škálování pro čtení | [Použít repliky jen pro čtení k načtení vyrovnávat zatížení dotazu jen pro čtení (preview)](sql-database-read-scale-out.md) |
| ETL (OLTP na OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) nebo [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrace z místního SQL serveru do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Přehled synchronizace dat SQL

Synchronizace dat vychází kolem koncepce skupině synchronizace. Skupina synchronizace se skupinou databází, které se mají synchronizovat.

Synchronizace dat používá k synchronizaci dat hvězdicové topologii. Definujte jedné z databází ve skupině synchronizace jako databáze centra. Zbytek databáze je databází člena. Synchronizace nastávají jenom mezi centra a jednotlivé členy.

- **Databáze centra** musí být Azure SQL Database.
- **Členské databáze** může být databáze SQL, databáze SQL serveru v místním nebo instance systému SQL Server na virtuálních počítačích Azure.
- **Databáze synchronizace** pro synchronizaci dat obsahuje metadata a protokolu. Synchronizace databáze musí být, že Azure SQL Database umístěný ve stejné oblasti jako databáze centra. Synchronizace databáze je zákazník vytvořil a vlastněné zákazníkem.

> [!NOTE]
> Pokud používáte databázi aplikace v místním prostředí jako databázi člena, budete muset [instalace a konfigurace agenta místní synchronizace](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizace dat mezi databázemi](media/sql-database-sync-data/sync-data-overview.png)

Skupina synchronizace má následující vlastnosti:

- **Schématu synchronizace** popisuje, jaká data se synchronizuje.
- **Směr synchronizace** může být obousměrné, nebo můžete pouze v jednom směru toku. To znamená, může být nastaven směr synchronizace *centra na člen*, nebo *člen k rozbočovači*, nebo obojí.
- **Interval synchronizace** popisuje, jak často dochází k synchronizaci.
- **Zásada řešení konfliktů** je úroveň zásad skupiny, které mohou být *centra wins* nebo *člen wins*.

## <a name="how-does-data-sync-work"></a>Jak funguje synchronizace dat

- **Sledování změn dat:** Synchronizace dat sleduje změny pomocí vložení, aktualizace a odstranění aktivační události. Změny se zaznamenávají v postranní tabulky v databázi uživatelů. Všimněte si, že BULK INSERT se neaktivuje aktivačních událostí ve výchozím nastavení. Pokud není zadán požadováno, spustit žádné aktivační události insert. Přidáte možnost požadováno tak synchronizaci dat můžete sledovat tyto operace vložení. 
- **Synchronizace dat:** Synchronizace dat je navržená v hvězdicové modelu. Centrum synchronizuje s každý člen jednotlivě. Změny z centra se stáhnou do člena a pak se nahrají změny od člena k rozbočovači.
- **Řešení konfliktů:** Poskytuje dvě možnosti pro řešení konfliktů synchronizace dat *centra wins* nebo *člen wins*.
  - Pokud vyberete *centra wins*, změny v centru vždy přepsat změny provedené v členu.
  - Pokud vyberete *člen wins*, změny v změn přepsání člena v centru. Pokud existuje více než jednoho člena, konečná hodnota závisí na člen, který synchronizuje poprvé.

## <a name="compare-data-sync-with-transactional-replication"></a>Porovnání synchronizace dat s využitím transakční replikace

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | – Podpora aktivní aktivní<br/>Obousměrné mezi místními a Azure SQL Database | -Nižší latence<br/>-Transakční konzistence<br/>-Znovu použít existující topologie po migraci |
| Nevýhody | -5 minut nebo další latence<br/>-Žádné transakční konzistence<br/>-Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database izolovanou databázi nebo databázi ve fondu<br/>-Vysoké náklady na údržbu |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Začínáme se synchronizací dat SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Nastavení synchronizace dat na webu Azure Portal

- [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)
- Agent – synchronizace dat [Data synchronizovat Agent pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Nastavení synchronizace dat s využitím Powershellu

- [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Přečtěte si osvědčené postupy pro synchronizaci dat

- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Něco zvrtne

- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzistencí a výkonem

#### <a name="eventual-consistency"></a>Konečná konzistence

Protože synchronizace dat na základě aktivační události, není zaručeno, že transakční konzistence. Microsoft zaručuje, že jsou všechny změny provedené nakonec a synchronizaci dat není způsobit ztrátu dat.

#### <a name="performance-impact"></a>Dopad na výkon

Synchronizace dat se používají vložení, aktualizace a odstranění aktivační události ke sledování změn. Vytvoří na straně tabulky v databázi uživatelů pro řešení change tracking. Tyto aktivity sledování změn mít vliv na vaše databázové úlohy. Vyhodnocení vaší úrovně služby a upgradovat v případě potřeby.

Zřizování a rušení zřízení během vytváření skupiny synchronizace, aktualizace a odstranění může mít také vliv na výkon databáze. 

## <a name="sync-req-lim"></a> Požadavky a omezení

### <a name="general-requirements"></a>Obecné požadavky

- Každá tabulka musí obsahovat primární klíč. Neměňte hodnotu primární klíč do libovolného řádku. Pokud budete muset změnit hodnotu primárního klíče, řádek odstranit a znovu ho vytvořte s novou hodnotu primárního klíče. 
- Musí být povolena izolace snímku. Další informace najdete v tématu [izolaci snímku v systému SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

- Tabulka nemůže obsahovat sloupec identity, který není primární klíč.
- Primární klíč nemůže mít následující typy dat: sql_variant, binary, varbinary, image, xml. 
- Buďte opatrní při použití těchto datových typů jako primární klíč, protože podporované přesnost je jenom na druhý: čas, datum a čas, datetime2, datetimeoffset.
- Názvy objektů (databáze, tabulek a sloupců) nesmí obsahovat tisknutelná znaků tečkou (.), zbývající hranaté závorky ([), nebo právo hranatá závorka (]).
- Ověřování pomocí Azure Active Directory se nepodporuje.
- Tabulky se stejným názvem, ale jiné schéma (například dbo.customers a sales.customers) nejsou podporovány.

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

- FileStream
- SQL/CLR UDT
- Kolekci XMLSchemaCollection (XML podporováno)
- Kurzor, RowVersion, časové razítko, Hierarchyid

#### <a name="unsupported-column-types"></a>Typy nepodporovanému sloupci

Synchronizace dat nelze synchronizovat jen pro čtení nebo systémem generované sloupce. Příklad:

- Vypočítané sloupce.
- Systémem generovaných sloupce pro dočasné tabulky.

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení týkající se služby a databáze dimenze

| **Dimenze**                                                      | **Limit**              | **Alternativní řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace všech databází může patřit do.       | 5                      |                             |
| Maximální počet koncových bodů ve skupině jedním synchronizačním              | 30                     |                             |
| Maximální počet místní koncové body ve skupině jedním synchronizačním. | 5                      | Vytvořte více skupin synchronizace |
| Názvy databáze, tabulky, schéma a sloupce                       | 50 znaky na název |                             |
| Tabulky ve skupině synchronizace                                          | 500                    | Vytvořte více skupin synchronizace |
| Sloupce v tabulce v skupinu synchronizace                              | 1000                   |                             |
| Velikost dat řádku v tabulce                                        | 24 Mb                  |                             |
| Interval synchronizace minimální                                           | 5 minut              |                             |
|||
> [!NOTE]
> Může existovat maximálně 30 koncové body ve skupině jedním synchronizačním dojde pouze jednu skupinu synchronizace. Pokud existuje více než jednu skupinu synchronizace, celkový počet koncových bodů ve všech skupinách synchronizace může mít maximálně 30. Pokud databáze náleží do několika skupin synchronizace, počítá se jako několik koncových bodů, není jednou.

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy týkající se synchronizace dat SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kolik stojí služby synchronizace dat SQL

Neexistuje žádné poplatky za samotnou službu synchronizace dat SQL.  Ale můžete pořád kumulovat poplatky za přenos dat pro přesun dat do a z vaší instanci služby SQL Database. Další informace najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat

Synchronizace dat SQL je k dispozici ve všech oblastech.

### <a name="is-a-sql-database-account-required"></a>Je nutné účet pro SQL Database

Ano. Musíte mít účet databáze SQL k hostování databáze centra.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Můžete používat synchronizaci dat pro synchronizaci mezi pouze místní databáze SQL serveru

Ne přímo. Můžete synchronizovat mezi databází systému SQL Server v místním nepřímo, ale vytváří se Centrum databáze v Azure, a následným přidáním místních databází do skupiny synchronizace.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Můžete používat synchronizaci dat pro synchronizaci mezi databází SQL, který patří do různých předplatných

Ano. Můžete synchronizovat mezi databázemi SQL, který patří do skupiny prostředků, které jsou vlastníkem různých předplatných.

- Pokud předplatná patří do stejného tenanta a máte oprávnění pro všechna předplatná, můžete nakonfigurovat skupinu synchronizace na portálu Azure portal.
- V opačném případě budete muset pomocí prostředí PowerShell můžete přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Můžete používat synchronizaci dat pro synchronizaci mezi databází SQL, do které patří do různých cloudů (např. veřejném cloudu Azure nebo Azure China)

Ano. Můžete synchronizovat mezi databázemi SQL, do které patří do různých cloudů, je nutné použít prostředí PowerShell přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Můžu používat synchronizaci dat k dosazení hodnot data z produkční databáze pro prázdnou databázi a synchronizovat je se

Ano. Vytvoření schématu ručně v nové databázi pomocí skriptů z původní. Po vytvoření schématu tabulky přidáte do skupiny synchronizace pro kopírování dat a zachování jejich synchronizované.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>By měl používat synchronizaci dat SQL k zálohování a obnovení databází.

Není doporučeno používat synchronizaci dat SQL vytvořte zálohu vašich dat. Nemůžete zálohovat a obnovení k určitému bodu v čase, protože synchronizace dat SQL synchronizace nejsou označené verzí. Kromě toho synchronizace dat SQL nezálohuje jiné objekty SQL, jako jsou uložené procedury a rychle nenabízí ekvivalent operaci obnovení.

Jeden doporučuje zálohování techniku, najdete v části [kopírovat databázi Azure SQL](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Můžete synchronizovat Data Sync šifrované tabulky a sloupce

- Pokud databáze používá funkce Always Encrypted, můžete synchronizovat pouze tabulky a sloupce, které jsou *není* šifrované. Šifrované sloupce nelze synchronizovat, protože synchronizace dat nemůže data dešifrovat.
- Pokud se sloupec používá šifrování sloupců (Vymazat), můžete synchronizovat sloupce, velikost řádku je menší než maximální velikost 24 Mb. Synchronizace dat zpracuje sloupec jako normální binární data zašifrovaná pomocí klíče (Vymazat). Dešifrovat data na ostatní členy synchronizace, musíte mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Je podporované kolace v synchronizaci dat SQL

Ano. Synchronizace dat SQL podporuje kolaci v následujících scénářích:

- Pokud schéma tabulky vybrané synchronizace není již ve vašich databázích rozbočovač nebo člen, pak když nasadíte skupinu synchronizace, služba automaticky vytvoří odpovídající tabulky a sloupce s nastavení řazení vybrali v prázdné cílové databáze.
- Pokud tabulky, které chcete synchronizovat, už existují v centra a členské databáze, synchronizaci dat SQL vyžaduje, primární klíč sloupců stejnou kolaci mezi databázemi centra a člen úspěšně nasadit skupinu synchronizace. Neexistují žádná omezení řazení pro sloupce kromě sloupce primárních klíčů.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je federace podporuje synchronizaci dat SQL

Kořenová databáze federace je možné ve službě synchronizace dat SQL bez omezení. Koncový bod federované databázi nelze přidat do aktuální verze synchronizace dat SQL.

## <a name="next-steps"></a>Další postup

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizovat schéma synchronizace databáze

Budete muset aktualizovat schéma databáze ve skupině synchronizace? Změny ve schématu nejsou replikovány automaticky. Některá řešení najdete v následujících článcích:

- [Automatizace replikace změn schématu synchronizace dat SQL Azure](sql-database-update-sync-schema.md)
- [Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí Powershellu](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Provádí synchronizaci dat SQL dle očekávání? Monitorování a řešení potíží, najdete v následujících článcích:

- [Monitorování synchronizace dat SQL Azure s protokoly Azure monitoru](sql-database-sync-monitor-oms.md)
- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Další informace o Azure SQL Database

Další informace o službě SQL Database najdete v následujících článcích:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
