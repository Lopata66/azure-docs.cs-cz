---
title: Vysvětlení slevách na využití platformy Azure rezervace | Dokumentace Microsoftu
description: Zjistěte, jak sleva za rezervaci se použije ke spuštění databáze SQL.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: ff31b9be0e927cca7d272e5deaa5ecf48a52580d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845449"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Vysvětlení, používání sleva za rezervaci Azure do databáze SQL

Po nákupu Azure SQL Database vyhrazené kapacity sleva za rezervaci se automaticky využije na databáze SQL, které odpovídají atributy a množství rezervace. Rezervace pokrývá náklady na výpočty pro SQL Database. Software, úložiště a sítě vám budeme účtovat za běžné sazby. Může zahrnovat náklady na licencování pro SQL Database s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Rezervované instance virtuálních počítačů najdete v části [slevy pochopit Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Sleva za rezervaci u databází SQL

 Sleva za rezervované kapacity databáze SQL se využije ke spouštění databází SQL po hodinách. Rezervace, které můžete zakoupit odpovídá využití služby compute, protože ho vygeneroval spuštěné databáze SQL. V případě služeb SQL Database, které neběží celou hodinu, se rezervace automaticky využije pro ostatní služby SQL Database odpovídající atributům rezervace. Sleva můžete použít SQL Database. ty jsou spuštěny souběžně. Pokud nemáte k dispozici SQL Database. ty spustit pro celou hodinu odpovídající atributy rezervace, neobdržíte všech výhod sleva za rezervaci pro určitou hodinu.

Následující příklady ukazují, jak platí sleva záložní kapacitu databáze SQL v závislosti na počtu jader jste koupili, a pokud máte spuštěnou.

- Scénář 1: Můžete si koupit kapacitu databáze SQL vyhrazený pro 8 jader SQL Database. Spuštění 16 jader SQL Database, která odpovídají rest atributy rezervace. Platíte průběžnými platbami cena za 8jádrový využití služby compute SQL Database. Získáte slevy na rezervaci za jednu hodinu využití služby compute databáze SQL 8 jader.

Zbytek těchto příkladech se předpokládá, že je databáze SQL vyhrazený kapacitu, kterou si koupit za 16 jader SQL Database a zbytek atributy rezervace shodovat s provozní databází SQL.

- Scénář 2: Spustíte dvě databáze SQL s 8 jádry je jedna hodina. Sleva za rezervaci 16 jader se použije k výpočtu využití pro obě 8 jader databází SQL.
- Scénář 3: Spusťte jeden 16jádrový procesor, SQL Database od 13: 00 do 1:30 odp. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Obě jsou zahrnuté do sleva za rezervaci.
- Scénář 4: Spusťte jeden 16jádrový procesor, SQL Database od 13: 00 do 1:45 pm. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Vám budou účtovány průběžných platbách za překryv 15 minut. Sleva za rezervaci se vztahuje na výpočetní využití pro zbytek času.

K pochopení a zobrazení aplikace vaše rezervace Azure na fakturaci využití sestav, naleznete v tématu [pochopit Azure rezervace využití](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
