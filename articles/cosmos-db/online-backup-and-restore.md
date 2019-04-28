---
title: Automatické, online zálohování a dat na vyžádání obnovení ve službě Azure Cosmos DB
description: Tento článek popisuje, jak automatické, online zálohování a dat na vyžádání obnovení ve službě Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930234"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Zálohování online a na vyžádání dat obnovení ve službě Azure Cosmos DB

Azure Cosmos DB automaticky provede zálohování dat v pravidelných intervalech. Automatické zálohy jsou prováděny bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy jsou uloženy zvlášť ve službě úložiště a tyto zálohy jsou globálně replikuje odolnosti proti místní havárií. Automatické zálohování jsou užitečné v situacích, pokud omylem odstraníte nebo aktualizovat účet služby Azure Cosmos, databáze nebo kontejneru a později vyžadují obnovení dat.

## <a name="automatic-and-online-backups"></a>Automatické a online zálohování

Pomocí služby Azure Cosmos DB jsou pouze data, ale také zálohy dat vysoce redundantní a odolná proti selháním regionální havárií. Následující kroky ukazují, jak službu Azure Cosmos DB provádí zálohování dat:

* Azure Cosmos DB automaticky převezme zálohu vaší databáze každé 4 hodiny a v libovolném okamžiku dobu pouze nejnovější 2 zálohy jsou uložené. Pokud však odstranění kontejneru nebo databáze Azure Cosmos DB zůstane existující snímky daného kontejneru nebo databáze po dobu 30 dnů.

* Azure Cosmos DB ukládá tyto zálohy v úložišti objektů Blob v Azure, že jsou skutečná data uložená místně v rámci služby Azure Cosmos DB.

*  Pokud chcete zajistit s nízkou latencí, snímek zálohy je uložený v úložišti objektů Blob v Azure ve stejné oblasti jako aktuální oblasti pro zápis (nebo jednu z oblastí zápisu v případě, že máte konfiguraci s několika hlavními databázemi) účet databáze Azure Cosmos. Což zvyšuje odolnost vůči regionálního jednotlivých snímků zálohování dat v úložišti objektů Blob v Azure znovu replikovat do jiné oblasti pomocí geograficky redundantního úložiště (GRS). Oblast, do které se replikují zálohování je na základě zdrojové oblasti a pár oblastí přidružené zdrojové oblasti. Další informace najdete v tématu [seznam dvojic geograficky redundantní oblastí Azure](../best-practices-availability-paired-regions.md) článku. Tuto zálohu nelze přistupovat přímo. Azure Cosmos DB bude používat tato záloha pouze v případě, že zahájila obnovení zálohy.

* Zálohy jsou prováděny bez vlivu na výkon nebo dostupnost vaší aplikace. Azure Cosmos DB provede zálohování dat na pozadí bez použití jakékoli další zřízená propustnost (ru) nebo které mají vliv výkon a dostupnost vaší databáze.

* Pokud jste omylem odstraněn nebo poškozen vaše data, měli byste požádat [podpory Azure](https://azure.microsoft.com/support/options/) do 8 hodin tak, aby tým služby Azure Cosmos DB může pomoci obnovíte data ze zálohy.

Následující obrázek ukazuje, jak je kontejner služby Azure Cosmos se všechny tři primární fyzické oddíly v oblasti západní USA zazálohovaný ve vzdálené účtu Azure Blob Storage v oblasti západní USA a pak replikují do USA – východ:

![Pravidelné úplné zálohy všechny entity Cosmos DB v geograficky Redundantního úložiště Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Možnosti Správa vlastních záloh

S účty SQL API služby Azure Cosmos DB můžete také spravovat vlastní zálohy pomocí jedné z následujících postupů:

* Použití [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pravidelně přesun dat do úložiště podle vašeho výběru.

* Pomocí služby Azure Cosmos DB [kanálu změn](change-feed.md) pravidelně čtení dat pro úplné zálohování, stejně jako na přírůstkové změny a uložte ho ve svém vlastním úložišti.

## <a name="backup-retention-period"></a>Období uchování zálohy

Azure Cosmos DB trvá snímky dat každé čtyři hodiny. V každém okamžiku pouze posledních dvou snímky, zůstanou zachovány. Pokud však odstranění kontejneru nebo databáze Azure Cosmos DB zůstane existující snímky daného kontejneru nebo databáze po dobu 30 dnů.

## <a name="restoring-data-from-online-backups"></a>Obnovení dat z online záloh

Náhodnému odstranění nebo změna dat může stát v jednom z následujících scénářů:  

* Odstraní celý účet Azure Cosmos

* Jeden nebo více databází Azure Cosmos se odstraní.

* Jeden nebo více kontejnerů Azure Cosmos se odstraní.

* Azure Cosmos položek (například dokumenty) v rámci kontejneru jsou odstraněna nebo upravena. Tento konkrétní případ se obvykle označuje jako "poškození dat".

* Nabídka sdílené databáze nebo kontejnery v rámci nabídky sdílené databáze odstraněn nebo poškozen

Azure Cosmos DB můžete obnovit data ve všech výše uvedených scénářích. Proces obnovení vždycky vytvoří nový účet Azure Cosmos pro obnovená data. Název nového účtu, pokud není zadán, bude mít formát `<Azure_Cosmos_account_original_name>-restored1`. Poslední číslice se zvýší, pokud více obnoví nedochází k pokusům o. Nelze obnovit data do předem vytvořeném účtu Azure Cosmos.

Při odstranění účtu Azure Cosmos, jsme obnovit data do účtu se stejným názvem, za předpokladu, že název účtu není používán. V takových případech se doporučuje není znovu vytvořte účet po odstranění, protože nejen brání obnovená data používat se stejným názvem, ale rovněž provede zjišťování pravý účet obnovit z obtížnější. 

Při odstranění databáze Azure Cosmos je možné obnovit celé databáze nebo podmnožinu kontejnery v rámci této databáze. Je také možné vybrat kontejnery v databázích a obnovit je a všechno, co se obnovená data nachází v novém účtu Azure Cosmos.

Při jedné nebo více položek v rámci kontejneru jsou náhodném odstranění nebo změně (případ poškození dat), je potřeba zadat čas obnovení. Čas je essence pro tento případ. Protože kontejneru je v provozu, zálohování je stále spuštěna, tak pokud čekáte po uplynutí doby uchování (výchozí hodnota je 8 hodin) zálohy budou přepsána. V případě odstranění, vaše data jsou už uložená protože nesmí být přepsána zálohování cyklu. Zálohy pro odstraněné databáze nebo kontejnery jsou uložena po dobu 30 dnů.

Pokud zřizujete propustnosti na úrovni databáze (to znamená, kde sadu kontejnerů sdílí zřízená propustnost), proces zálohování a obnovení v tomto případě dochází na úrovni celé databáze a ne na úrovni jednotlivých kontejnerů. V takových případech výběrem podmnožiny kontejnery obnovení není možné.

## <a name="migrating-data-to-the-original-account"></a>Migrace dat do původního účtu

Primární cíl obnovení dat je poskytnout způsob, jak obnovit všechna data, která Nemazat ani neupravovat omylem. Proto doporučujeme nejprve zkontrolovat obsah obnovená data k zajištění, že obsahuje, co jste očekávali. Pak práce na migraci dat zpět do primárního účtu. I když je možné použít obnovený účet jako účet za provozu, není doporučená možnost máte úlohy v produkčním prostředí.  

Následují různé způsoby, jak migrovat data zpět do původního účtu Azure Cosmos:

* Pomocí [nástroj pro migraci dat Cosmos DB](import-data.md)
* Pomocí [služby Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Pomocí [kanálu změn](change-feed.md) ve službě Azure Cosmos DB 
* Psát vlastní kód

Odstranit obnovenou účty ihned poté, co dokončíte migraci, protože se bude zbytečně nenabíhaly poplatky.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak obnovit data z účtu služby Azure Cosmos, nebo zjistěte, jak migrovat data do účtu Azure Cosmos

* Chcete-li obnovení žádostí, obraťte se na podporu Azure, [lístek na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Postup obnovení dat z účtu služby Azure Cosmos](how-to-backup-and-restore.md)
* [Cosmos DB pomocí kanálu změn](change-feed.md) pro přesun dat do služby Azure Cosmos DB.
* [Použití Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pro přesun dat do služby Azure Cosmos DB.

