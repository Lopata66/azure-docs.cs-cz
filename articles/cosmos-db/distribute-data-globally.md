---
title: Globální distribuce dat pomocí Azure Cosmos DB
description: Přečtěte si o geografické replikaci globálním, převzetí služeb při selhání a obnovení dat pomocí globálních databází z Azure Cosmos DB globálně distribuovanou databázovou službou pro více modelů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seo-nov-2020
ms.openlocfilehash: 7fedd45585698aef9248318a1b055cb656f25d02
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335124"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Globální distribuce dat pomocí Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

V současné době se od aplikací žádá, aby byly vysoce responzivní a vždy online. Aby bylo možné zajistit nízkou latenci a vysokou dostupnost, instance aplikací je potřeba nasazovat do datových center, která se nachází v blízkosti uživatelů. Tyto aplikace jsou obvykle nasazeny v několika datových centrech a jsou označovány jako globálně distribuované. Globálně distribuované aplikace potřebují globálně distribuovanou databázi, která dokáže transparentně replikovat data po celém světě, aby mohly pracovat s kopií dat, která je blízko uživatelům. 

Azure Cosmos DB je globálně distribuovaný databázový systém, který umožňuje číst a zapisovat data z místních replik databáze. Azure Cosmos DB transparentně replikuje data do všech oblastí přidružených k vašemu účtu Cosmos. Azure Cosmos DB je globálně distribuovaná databázová služba, která je navržená tak, aby poskytovala nízkou latenci, elastickou škálovatelnost propustnosti, jasně definovanou sémantiku pro konzistenci dat a vysokou dostupnost. Krátce, pokud vaše aplikace potřebuje rychlou odezvu kdekoli na světě, pokud je potřeba, aby byla vždycky online, a potřebuje neomezenou a pružnou škálovatelnost propustnosti a úložiště, měli byste sestavit aplikaci na Azure Cosmos DB.

Své databáze můžete nakonfigurovat tak, aby byly globálně distribuované a dostupné v libovolné oblasti Azure. Pokud chcete snížit latenci, umístěte data blízko do místa, kde jsou vaši uživatelé. Výběr požadovaných oblastí závisí na globálním dosahu vaší aplikace a na umístění uživatelů. Cosmos DB transparentně replikuje data do všech oblastí přidružených k vašemu účtu Cosmos. Poskytuje jedinou systémovou bitovou kopii vaší globálně distribuované databáze a kontejnerů Azure Cosmos, které může vaše aplikace číst a zapisovat do místního počítače. 

Pomocí Azure Cosmos DB můžete kdykoli přidat nebo odebrat oblasti přidružené k vašemu účtu. Pro přidání nebo odebrání oblasti není nutné aplikaci pozastavit ani znovu nasadit. I nadále bude mít vysoce dostupný čas v důsledku možností více domovských stránek, které služba nativně poskytuje.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Vysoce dostupná topologie nasazení" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Sestavování globálních aktivních – aktivních aplikací.** Díky novému zápisu do více oblastí zapisuje replikační protokol každá oblast podporuje zápis i čtení. Funkce zápisu ve více oblastech taky umožňuje:

- Neomezený pružný zápis a škálovatelnost pro čtení.
- 99,999% dostupnost čtení a zápisu všech po celém světě.
- Garantuje čtení a zápisy poskytované za méně než 10 milisekund v 99 percentilu.

Pomocí Azure Cosmos DB rozhraní API pro více domovských míst vaše aplikace je vědomá nejbližší oblasti a může odesílat žádosti do této oblasti. Nejbližší oblast je identifikována bez jakýchkoli změn konfigurace. Při přidávání a odebírání oblastí do a z účtu Azure Cosmos není nutné aplikaci znovu nasadit ani pozastavit, stále je vysoce dostupná.

**Vytvářejte vysoce reagující aplikace.** Vaše aplikace může provádět čtení a zápis téměř v reálném čase ve všech oblastech, které jste zvolili pro vaši databázi. Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi a zárukami na úrovni konzistence zvolené úrovně.

**Vytvářejte vysoce dostupné aplikace.** Spuštění databáze ve více oblastech po celém světě zvyšuje dostupnost databáze. Pokud je jedna oblast nedostupná, jiné oblasti automaticky zpracovávají požadavky aplikací. Azure Cosmos DB nabízí 99,999% dostupnost čtení a zápisu pro databáze ve více oblastech.

**Udržujte kontinuitu podnikových aplikací během regionálních výpadků.** Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) při regionálním výpadku. Během regionálního výpadku Azure Cosmos DB nadále udržovat latenci, dostupnost, konzistenci a SLA propustnost. Aby bylo možné zajistit vysokou dostupnost celé aplikace, Cosmos DB nabízí rozhraní API pro ruční převzetí služeb při selhání pro simulaci oblasti výpadku regionu. Pomocí tohoto rozhraní API můžete provádět běžné cvičení provozní kontinuity.

**Globální škálování a propustnost čtení a zápisu** Každé oblasti můžete povolit zapisovatelné a elasticky škálovat čtení a zápisy po celém světě. Propustnost, kterou vaše aplikace nakonfiguruje v databázi Azure Cosmos nebo ve všech oblastech přidružených k vašemu účtu Azure Cosmos, se zřizuje v rámci všech oblastí. Zajištěná propustnost je zaručena podle [finančně zálohovaných SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Vyberte si z několika dobře definovaných modelů konzistence.** Protokol replikace Azure Cosmos DB nabízí pět dobře definovaných, praktických a intuitivních modelů konzistence. Každý model má kompromisy mezi konzistencí a výkonem. Tyto modely konzistence slouží k snadnému sestavování globálně distribuovaných aplikací.

## <a name="next-steps"></a><a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)
* [Jak nakonfigurovat zápisy ve více oblastech v aplikacích](how-to-multi-master.md)
* [Konfigurace klientů pro vícedomé služby](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Přidat nebo odebrat oblasti z Azure Cosmos DB účtu](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvoření vlastních zásad řešení konfliktů pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programovatelné modely konzistence v Cosmos DB](consistency-levels.md)
* [Volba správné úrovně konzistence pro vaši aplikaci](./consistency-levels.md)
* [Úrovně konzistence napříč Azure Cosmos DBmi rozhraními API](./consistency-levels.md)
* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](./consistency-levels.md)