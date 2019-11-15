---
title: Způsob uplatňování slev za rezervaci na izolovaný server Azure Database for PostgreSQL
description: Způsob uplatňování slev za rezervaci na izolovaný server Azure Database for PostgreSQL
author: kummanish
ms.author: manishku
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: bae652e57cbcf141b248b20b0f8385003ba8f000
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608080"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Způsob uplatňování slev za rezervaci na izolovaný server Azure Database for PostgreSQL

Když si zakoupíte rezervovanou kapacitu izolovaného serveru Azure Database for PostgreSQL, na izolované servery PostgreSQL, které vyhovují příslušným atributům a množství rezervace, se automaticky uplatňuje sleva za rezervaci. Rezervace pokrývá pouze náklady na výpočetní prostředky izolovaného serveru Azure Database for PostgreSQL. Poplatky za úložiště a sítě se vám budou účtovat za použití normálních sazeb. 

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se ***nenahrazuje***. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.</br>

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Sleva uplatněná na izolovaný server Azure Database for PostgreSQL

Sleva za rezervovanou kapacitu izolovaného serveru Azure Database for PostgreSQL se na provoz izolovaného serveru PostgreSQL uplatňuje po hodinách. Rezervace, kterou si zakoupíte, se přiřadí k využívání výpočetních prostředků spuštěným izolovaným serverem Azure Database for PostgreSQL. V případě izolovaných serverů PostgreSQL, které neběží celou hodinu, se rezervace automaticky využije pro jiný izolovaný server Azure Database for PostgreSQL vyhovující atributům rezervace. Sleva se může uplatnit i na souběžně spuštěné izolované servery Azure Database for PostgreSQL. Pokud nemáte izolovaný server PostgreSQL, který běží celou hodinu a vyhovuje atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Následující příklady ukazují, jak se sleva za rezervovanou kapacitu izolovaného serveru Azure Database for PostgreSQL uplatňuje v závislosti na počtu zakoupených jader a na tom, kdy jsou tato jádra spuštěná.

* **Příklad 1:** Zakoupíte si rezervovanou kapacitu izolovaného serveru Azure Database for PostgreSQL pro 8 virtuálních jader. Pokud používáte izolovaný server Azure Database for PostgreSQL s 16 virtuálními jádry, který vyhovuje ostatním atributům rezervace, za využití výpočetních prostředků pro 8 virtuálních jader izolovaného serveru PostgreSQL se vám bude účtovat cena při průběžných platbách a na jednu hodinu využití výpočetních prostředků pro izolovaný server PostgreSQL s 8 virtuálními jádry získáte slevu za rezervaci.</br>

U ostatních příkladů předpokládejme, že si zakoupíte rezervovanou kapacitu izolovaného serveru Azure Database for PostgreSQL pro 16 virtuálních jader a ostatní atributy rezervace odpovídají spuštěným izolovaným serverům PostgreSQL.

* **Příklad 2:** Na hodinu spustíte dva izolované servery Azure Database for PostgreSQL, z nichž každý má 8 virtuálních jader. Sleva za rezervaci 16 virtuálních jader se uplatní na využití výpočetních prostředků pro oba izolované servery Azure Database for PostgreSQL s 8 virtuálními jádry.

* **Příklad 3:** Od 13:00 do 13:30 spustíte jeden izolovaný server Azure Database for PostgreSQL s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný izolovaný server Azure Database for PostgreSQL s 16 virtuálními jádry. Na obě se bude vztahovat sleva za rezervaci.

* **Příklad 4:** Od 13:00 do 13:45 spustíte jeden izolovaný server Azure Database for PostgreSQL s 16 virtuálními jádry. Od 13:30 do 14:00 spustíte jiný izolovaný server Azure Database for PostgreSQL s 16 virtuálními jádry. Za 15 minut, kdy se doba chodu obou databází překrývá, zaplatíte průběžnou platbu. Po zbytek doby se na využití výpočetních prostředků uplatňuje sleva za rezervaci.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
