---
title: Azure Cosmos DB konzistence, dostupnost a kompromisů v výkonu
description: Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4de696e2538bf1fa4823aafe30f931b7852535a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82191732"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy mezi konzistencí, dostupností a výkonem

Distribuované databáze, které se kvůli zajištění vysoké dostupnosti, nízké latence nebo obojího spoléhají na replikaci, musí dělat kompromisy. Tyto kompromisy jsou mezi konzistencí čtení a dostupností, latencí a propustností.

Azure Cosmos DB přistupuje k konzistenci dat jako spektrum možností. Tento přístup zahrnuje více možností než dvě extrémní silná a konečná konzistence. Pro spektrum konzistence si můžete vybrat z pěti jasně definovaných úrovní. Od nejsilnějších po nejslabších úrovních jsou tyto úrovně:

- *Silné*
- *Ohraničená neaktuálnost*
- *Relace*
- *Konzistentní předpona*
- *Nahodilé*

Jednotlivé úrovně poskytují kompromisy k dostupnosti a výkonu a jsou zajištěny ucelenou SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Tato latence čtení se zálohuje smlouvou SLA. Průměrná latence čtení v 50. percentilu má obvykle 4 milisekundy nebo méně.

Latence zápisu pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Tato latence zápisu se zálohuje smlouvou SLA. Průměrná latence zápisu na 50. percentilu je obvykle 5 milisekund nebo méně. Účty Azure Cosmos, které jsou v několika oblastech a jsou nakonfigurované se silnými konzistencí, představují výjimku z této záruky.

### <a name="write-latency-and-strong-consistency"></a>Latence zápisu a silná konzistence

U účtů Azure Cosmos nakonfigurovaných se silnou konzistencí s více než jednou oblastí se latence zápisu rovná dvojnásobku doby odezvy (RTT) mezi kteroukoli ze dvou nejvzdálenějších oblastí a 10 milisekundami v 99 percentilu. Vysoká doba odezvy sítě mezi oblastmi se přeloží na vyšší latenci pro Cosmos DB požadavky, protože silná konzistence dokončí operaci jenom poté, co zaručí, že se potvrdila ve všech oblastech v rámci účtu.

Přesná latence RTT je funkce rychlosti a topologie sítě Azure. Azure Networking neposkytuje žádnou latenci SLA pro dobu odezvy mezi dvěma oblastmi Azure. Pro váš účet Azure Cosmos se latence replikace zobrazují v Azure Portal. Pokud chcete monitorovat latence replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos, můžete použít Azure Portal (Přejít do okna metrika a vybrat kartu konzistence).

> [!IMPORTANT]
> Při vysoké latenci zápisu je ve výchozím nastavení zablokovaná silná konzistence pro účty s oblastmi zahrnujícími více než 5000 mil (8000 kilometrů). Pokud chcete tuto funkci povolit, obraťte se prosím na podporu.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnost

- V případě silné a ohraničené neaktuálnosti se čtení provádí se dvěma replikami ve čtyři sadě replik (minoritní kvorum), aby poskytovala záruky konzistence. Relace, konzistentní předpona a konečné čtení pro jednu repliku. Výsledkem je, že pro stejný počet jednotek žádosti je propustnost čtení pro silná a ohraničená neaktuálnost v polovině dalších úrovní konzistence.

- Pro daný typ operace zápisu, například INSERT, Replace, Upsert a DELETE, je propustnost zápisu pro jednotky požadavků stejná pro všechny úrovně konzistence.

|**Úrovně konzistence**|**Čtení kvora**|**Zápisy kvora**|
|--|--|--|
|**Silné**|Místní menšina|Globální většina|
|**Omezená neaktuálnost**|Místní menšina|Místní většina|
|**Relace**|Jedna replika (pomocí tokenu relace)|Místní většina|
|**Konzistentní předpona**|Jedna replika|Místní většina|
|**Nahodilé**|Jedna replika|Místní většina|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Úrovně konzistence a trvanlivost dat

V globálně distribuovaném databázovém prostředí existuje přímý vztah mezi úrovní konzistence a odolností s daty v oblasti výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako **cíl doby obnovení** (**RTO**). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časové období aktualizací, které můžete chtít ztratit, se označuje jako **cíl bodu obnovení** (**RPO**).

Následující tabulka definuje vztah mezi modelem konzistence a odolností dat při výpadku oblasti v rámci sítě. Je důležité si uvědomit, že v distribuovaném systému, a to i se silnou konzistencí, není možné mít distribuovanou databázi s cílem RPO a RTO nula z důvodu věta CAP. Další informace o tom, proč najdete [v tématu úrovně konzistence v Azure Cosmos DB](consistency-levels.md).

|**Oblast (y)**|**Režim replikace**|**Úroveň konzistence**|**Cíl bodu obnovení**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Jedna nebo více hlavních serverů|Jakákoli úroveň konzistence|< 240 minut|<1 týden|
|>1|Jedna hlavní|Relace, konzistentní předpona, případný|< 15 minut|< 15 minut|
|>1|Jedna hlavní|Omezená neaktuálnost|*K*  &  *T*|< 15 minut|
|>1|Jedna hlavní|Silné|0|< 15 minut|
|>1|Vícenásobný hlavní|Relace, konzistentní předpona, případný|< 15 minut|0|
|>1|Vícenásobný hlavní|Omezená neaktuálnost|*K*  &  *T*|0|

*K* = počet verzí *"K"* (tj. aktualizace) položky.

*T* = časový interval *"t"* od poslední aktualizace.

## <a name="strong-consistency-and-multi-master"></a>Silná konzistence a vícenásobný hlavní

Účty Cosmos nakonfigurované pro sadu multi-Master nelze nakonfigurovat pro zajištění vysoké konzistence, protože distribuovaný systém nemůže poskytnout RPO s hodnotou 0 a RTO nula. Navíc neexistují žádné výhody latence zápisu pro použití silné konzistence s více hlavními servery, které by měly být replikovány do jakékoli oblasti a jsou potvrzeny do všech nakonfigurovaných oblastí v rámci daného účtu. Výsledkem je stejná latence zápisu jako jeden hlavní účet.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o globální distribuci a obecných kompromisech konzistence v distribuovaných systémech. Viz následující články:

- [Návrh moderních systémů distribuovaných databází – kompromisy v konzistenci](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Vysoká dostupnost](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
