---
title: Škálování prostředků Azure SQL Database | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak škálovat přidáváním nebo odebíráním přidělené prostředky vaší databáze.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c918abdc635e7a4a831e367e159354bb752e95e6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743109"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamicky škálovat prostředky databáze s minimálními výpadky

Azure SQL Database umožňuje dynamicky přidat další prostředky do databáze s minimálními výpadky.

## <a name="overview"></a>Přehled

Když poptávka po vaší aplikaci roste od několika zařízení a zákazníků do milionů, Azure SQL Database se škáluje v reálném čase s minimálními výpadky. Škálovatelnost je jedním z nejdůležitějších vlastností PaaS, která umožňuje dynamicky přidat další prostředky na vaši službu, v případě potřeby. Azure SQL Database vám umožní snadno měnit prostředky (výkonu procesoru, paměti, vstupně-výstupních operací a úložiště) přidělené vaší databáze.

Můžete zmírnit problémy s výkonem způsobený zvyšováním využití aplikace, které nelze opravit pomocí indexování nebo přepsání metody dotazu. Přidání více zdrojů, můžete rychle reagovat, když narazí na aktuální omezení prostředků a vyžaduje větší výkon pro zpracování příchozích úloh databáze. Azure SQL Database také umožňuje vám vertikální snížení kapacity prostředků že podle potřeby snížit náklady.

Nemusíte se starat o nákup hardwaru a změnou základní infrastruktury. Škálování databáze můžete snadno udělat pomocí webu Azure portal pomocí posuvníku.

![Škálování výkonu databáze](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database nabízí [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-VÝSTUPNÍCH prostředků ve třech úrovních služeb pro podporu nejlehčích k těm nejnáročnějším: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
- [Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) umožňuje výběr počtu virtuálních jader, velikost nebo paměti a množství a rychlosti úložiště. Tento model nákupu nabízí tři úrovně služby: Obecné účely, pro důležité obchodní informace a mírou škálování.

Můžete sestavit svoji první aplikaci na malou izolovanou databází s nízkými náklady za měsíc v rámci úrovně služeb Basic, Standard nebo obecné účely a potom změnit úroveň služby ručně nebo prostřednictvím kódu programu kdykoli do služby úrovně Premium nebo pro důležité obchodní informace pro splnění ne EDS vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

> [!NOTE]
> Dynamická škálovatelnost se liší od automatického škálování. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů.

Izolovaná databáze Azure SQL Database podporuje ruční dynamickou škálovatelnost, nikoli automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází.
Existují však skripty, které můžou usnadnit automatizaci škálovatelnost pro izolovanou databázi SQL Azure. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).

Můžete změnit [jednotek DTU úrovně služeb](sql-database-service-tiers-dtu.md) nebo [vCore charakteristiky](sql-database-vcore-resource-limits-single-databases.md) kdykoli s minimálními prostoji do vaší aplikace (obecně odstávkou čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Pro tento scénář použijete elastický fond s určitým počtem jednotek Edtu, které jsou odkazy sdíleny mezi více databází ve fondu.

![Úvod do služby SQL Database: Počet jednotek Dtu izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Všechny tři typy služby Azure SQL Database nabízí některé schopnost dynamicky škálovat databáze:

- S [izolované databáze](sql-database-single-database-scale.md), můžete použít buď [DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [vCore](sql-database-vcore-resource-limits-single-databases.md) modely, které definují maximální množství prostředků, které se přiřadí každé databázi.
- A [Managed Instance](sql-database-managed-instance.md) používá [virtuálních jader](sql-database-managed-instance.md#vcore-based-purchasing-model) režimu a umožňuje definovat maximální počet jader procesoru a maximální úložiště přidělené k vaší instanci. Všechny databáze v instanci se sdílení prostředků přidělených této instanci.
- [Elastické fondy](sql-database-elastic-pool-scale.md) vám umožňují definovat prostředek maximální limit pro skupinu databází ve fondu.

> [!NOTE]
> Můžete očekávat, že dokončení zalomení krátký připojení při škálování nahoru/vertikální snížení kapacity procesu. Pokud jste implementovali [Logika opakování pro přechodné chyby standardní](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), nebudete si převzetí služeb při selhání.

## <a name="alternative-scale-methods"></a>Škálování alternativní metody

Škálování prostředků je nejjednodušší a nejúčinnější způsob, jak zlepšit výkon vaší databáze beze změny kódu databáze nebo aplikace. V některých případech se nemusí zvládnutí úloh na úspěšné a nákladově efektivní způsob, jak i nejvyšší úrovně služeb, velikostí výpočetních a optimalizace výkonu. V tomto případě máte těchto dalších možnostech škálování databáze:

- [Horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md) je funkce k dispozici ve kterém se zobrazuje jednu repliku pouze pro čtení dat ve kterém můžete spustit vyhovovat i vašim náročným dotazy jen pro čtení, jako je například sestavy. Repliku pouze pro čtení bude zpracovávat úlohy jen pro čtení bez ovlivnění využití prostředků u primární databáze.
- [Horizontální dělení databází](sql-database-elastic-scale-introduction.md) je sadu technik, které vám umožní rozdělit data do několika databází a nezávisle škálovat.

## <a name="next-steps"></a>Další postup

- Informace týkající se vylepšení výkonu databáze změnou kódu databáze najdete v tématu [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md).
- Informace o umožňuje inteligentní funkce integrované database Optimalizujte vaši databázi, naleznete v tématu [automatické ladění](sql-database-automatic-tuning.md).
- Informace o čtení škálování ve službě Azure SQL Database najdete v tématu Jak [pomocí repliky jen pro čtení můžete načíst úlohy dotazu jen pro čtení Zůstatek](sql-database-read-scale-out.md).
- Informace o horizontální dělení databází najdete v tématu [horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md).
