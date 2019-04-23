---
title: Vysvětlení, jak sleva za rezervaci se použije ke službě Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak se sleva za rezervaci použije k zřízená propustnost (RU/s) ve službě Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d5a13e4466234d73bafe8dbe76cae92955cf64bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370743"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Vysvětlení, jak sleva za rezervaci se použije ke službě Azure Cosmos DB

Po nákupu Azure Cosmos DB vyhrazené kapacity sleva za rezervaci se automaticky využije na prostředky Azure Cosmos DB, které odpovídají atributy a množství rezervace. Rezervace se věnuje zajištěné propustnosti pro prostředky Azure Cosmos DB. Nezahrnuje softwaru, sítě, úložiště, nebo předdefinované kontejneru poplatky.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se "*použití – it nebo ztratit – it*". Ano Pokud nemáte odpovídající prostředky pro hodinu, potom ztratíte množství rezervací pro určitou hodinu. Nelze provést vpřed nevyužité vyhrazené hodin.

Při vypínání prostředek sleva za rezervaci automaticky použije další odpovídající prostředek v zadaném oboru. Pokud systém nenašel žádné odpovídající prostředky v zadaném oboru, pak jsou vyhrazené hodin *ztráty*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Sleva za rezervaci použitý k účtům Azure Cosmos DB

Sleva za rezervaci se použije k [zřízená propustnost](../cosmos-db/request-units.md) z hlediska jednotek žádostí za sekundu (RU/s) na základě hodinu hodinách. Sleva za rezervaci se pro prostředky Azure Cosmos DB, která nepoužívají celou hodinu, automaticky využije na další služby Cosmos DB prostředky, které odpovídají atributy rezervace. Sleva můžete použít pro prostředky Azure Cosmos DB, které jsou spuštěny souběžně. Pokud nemáte k dispozici prostředky Cosmos DB, na kterých běží celou hodinu a které se shodují s atributy rezervace, neobdržíte všech výhod sleva za rezervaci pro určitou hodinu.

Proběhne slevy. Rezervace s vyšší jednotkách požadavků poskytují vyšší slevy.

Nákup rezervace se uplatní slevy ke všem oblastem v poměru ekvivalentní místní ceny na vyžádání. Poměry slevu na rezervaci v jednotlivých oblastech, najdete v článku [sleva za rezervaci v jedné oblasti](#reservation-discount-per-region) části tohoto článku.

## <a name="reservation-discount-per-region"></a>Sleva za rezervaci na oblast
Sleva za rezervaci se použije na náklady na propustnost služby Azure Cosmos DB po hodinu hodinách. Použije se na jedno předplatné nebo rozsahu zaregistrované nebo účtu. Sleva za rezervaci se vztahují na měření využití v různých oblastech v poměry následující:

|Popis měřiče  |Oblast |Poměr  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – Asie a Tichomoří – jihovýchod  |  Asie a Tichomoří – jihovýchod    |   1      |
|Azure Cosmos DB – 100 RU/s/hodina – Asie a Tichomoří – východ |   Asie a Tichomoří – východ   |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Evropa – sever|  Evropa – sever       |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Korea – jih|    Korea – jih     |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Evropa – západ|    Evropa – západ     |      1   |
|Azure Cosmos DB – 100 RU/s/hodina – Korea – střed|   Korea – střed    |       1  |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – jih|   Velká Británie – jih      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – západ|   Spojené království – západ      |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – sever |   Velká Británie – sever    |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – jih 2|   Velká Británie – jih 2      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – východ 2|  USA – východ 2     |     1    |
|Azure Cosmos DB – 100 RU/s/hodina - střed USA – sever|   USA (střed) – sever      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ|   USA – západ      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – střed| USA – střed        |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ 2|   USA – západ 2      |      1   |
|Azure Cosmos DB – 100 RU/s/hodina – USA – Středozápad|   USA – středozápad      |       1  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – východ|   USA – východ      |  1       |
|Azure Cosmos DB – 100 RU/s/hodina - Jižní Afrika – sever|     Jižní Afrika – sever    |   1      |
|Azure Cosmos DB – 100 RU/s/hodina - Jižní Afrika – západ |    SA West      |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – jih|    Indie – jih     |    1.0375    |
|Azure Cosmos DB – 100 RU/s/hodina - CA – východ|   Kanada – východ      |    1.1      |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – východ|   Japonsko – východ      |    1.125     |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – západ|     Japonsko – západ    |   1.125       |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – západ|     Indie – západ    |    1.1375     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – střed|    Indie – střed     |  1.1375       |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – východ|     Austrálie – východ    |   1.15       |
|Azure Cosmos DB CA 100 RU/s/hodina – střed|  Kanada – střed       |   1.2       |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – střed|   Francie – střed      |    1.25      |
|Azure Cosmos DB – 100 RU/s/hodina – Brazílie – jih|  Brazílie – jih       |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed|   Austrálie – střed      |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2| Austrálie – střed 2        |    1,5     |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih|    Francie – jih     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scénáře, které ukazují, jak se sleva za rezervaci použije

Vezměte v úvahu následující požadavky na rezervaci:

* Požadované propustnosti: 50 000 RU/s  
* Oblasti použít: 2

V tomto případě jsou vaše celkové náklady na vyžádání pro 500 množství měřiče 100 RU/s v těchto dvou oblastech. Celková spotřeba RU/s je 100 000 za hodinu.

**Scénář 1**

Předpokládejme například, že je třeba nasazení služby Azure Cosmos DB v oblastech USA – sever – střed a USA – západ. Každá oblast má spotřeby propustnost 50 000 RU/s. Nákup rezervace 100 000 RU/s by zcela zajistit rovnováhu mezi poplatky na vyžádání.

Slevy, které pokrývá rezervace se vypočítává jako: využití propustnosti * reservation_discount_ratio_for_that_region. Pro oblasti USA – sever – střed a USA – západ poměr slevu na rezervaci je 1. Zlevněné celkem RU/s, takže jsou 100 000. Tato hodnota se vypočítává jako: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Nemusíte platit žádné další poplatky za běžné sazby průběžných plateb.

|Popis měřiče | Oblast |Využití propustnosti (RU/s) |Sleva za rezervaci u RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina - střed USA – sever  |   USA (střed) – sever  | 50,000  | 50,000  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ  |  USA – západ   |  50,000  |  50,000 |

**Scénář 2**

Předpokládejme například, že je třeba nasazení služby Azure Cosmos DB v oblastech Austrálie – střed 2 a Francie – jih. Každá oblast má spotřeby propustnost 50 000 RU/s. Nákup rezervace 100 000 RU/s by byly použitelné následujícím způsobem (za předpokladu, že využití Austrálie – střed 2 se slevou nejprve):

|Popis měřiče | Oblast |Využití propustnosti (RU/s) |Sleva za rezervaci u RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2  |  Austrálie – střed 2   |  50,000  |  50,000   |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih  |  Francie – jih   |  50,000 |  15,384  |

Využití 50 000 jednotek v oblasti Austrálie – střed 2 odpovídá 75 000 RU/s fakturovatelné využití (nebo normalizované využití). Tato hodnota se vypočítává jako: využití propustnosti * reservation_discount_ratio_for_that_region. Výpočet rovná 75 000 RU/s fakturovatelné nebo normalizované využití. Tato hodnota se vypočítává jako: 50 000 * 1.5 = 75 000 RU/s.

100 000 RU/s nákup rezervace by posun 75 000 RU/s v Austrálie – střed 2. Opuštění 25 000 RU/s oblastí Francie – jih. Ze zbývajících 25 000 RU/s platí sleva za rezervaci 15,384 RU/s pro oblasti Francie – jih. Hodnota slevy se vypočítává jako: 25 000 / 1.625 = 15,384 RU/s. Zbývající 34,616 RU/s v oblasti Francie – jih se účtuje za běžné sazby průběžných plateb.

Přiřadí sníženou sazbou za rezervaci pro první instanci, která je zpracována a, který odpovídá konfiguraci rezervace se fakturačního systému Azure. Například je Austrálie – střed 2 v tomto případě.

K pochopení a zobrazení aplikace Azure rezervace na fakturaci využití sestav, naleznete v tématu [pochopit Azure rezervace využití](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o Azure rezervace, naleznete v následujících článcích:

* [Co jsou rezervace pro Azure](../billing/billing-save-compute-costs-reservations.md)  
* [Předplatíte prostředky Azure Cosmos DB pomocí služby Azure Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)  
* [Správa rezervací pro Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)  
* [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)
