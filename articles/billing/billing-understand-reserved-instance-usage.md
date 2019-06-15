---
title: Vysvětlení využití Azure rezervace pro předplatné s průběžnými platbami | Dokumentace Microsoftu
description: Zjistěte, jak číst využití, abyste pochopili, jak použít Azure rezervace pro vaše předplatné s průběžnými platbami.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 14bd7b61038bf938f7d370eaf6e16d71b5da43ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370316"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Vysvětlení využití Azure rezervace pro vaše předplatné s průběžnými platbami

Použít ReservationId z [rezervované stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a používání souboru z [portálu účtů Azure](https://account.azure.com) vyhodnotit využití rezervace.

Pokud jste zákazník se smlouvou Enterprise, přečtěte si téma [porozumět používání rezervaci u prováděcí smlouvy Enterprise.](billing-understand-reserved-instance-usage-ea.md).

Tento článek předpokládá, že se má rezervace použít na jedno předplatné. Pokud se má rezervace použít na více než jedno předplatné, může vaše výhoda rezervací uložena ve více souborech použití sdíleného svazku clusteru.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Využití pro rezervované instance virtuálních počítačů

V dalších částech předpokládají, že používáte virtuální počítač Windows Standard_DS1_v2 oblasti USA – východ a vaše rezervovaných virtuálních počítačů instance informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Množství |1|
|Skladová jednotka (SKU) | Standard_DS1_v2|
|Oblast | eastus |

Část hardwaru virtuálního počítače se vztahuje, protože nasazený virtuální počítač odpovídající rezervaci atributy. Jaký software Windows není předmětem rezervované instance virtuálního počítače najdete v tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Příkaz části souboru .csv pro virtuální počítače

Tato část souboru CSV se zobrazí celkové využití vaší rezervace. Použít filtr na **podkategorie měřiče** pole s údajem o **"Rezervace-"** . Zobrazit něco jako na následujícím snímku obrazovky:

![Snímek obrazovky filtrované rezervace informací o využití a nákladů](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**Rezervované základní virtuální počítač** řádek obsahuje celkový počet hodin, které jsou předmětem rezervace. Tento řádek je 0,00 Kč, protože rezervace zvládne. **Počítače rezervace Windows serverem (1 jádro)** řádku zahrnují náklady na Windows software.

### <a name="daily-usage-section-of-csv-file"></a>Části denní využívání ze souboru CSV

Filtrovat podle **Další informace o** a zadejte vaše **ID rezervace**. Následující snímek obrazovky ukazuje pole související s rezervace.

![Snímek obrazovky s informací o denním využití a poplatky za](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** v **Další informace o** pole je rezervace, které se použije k virtuálnímu počítači.
2. **ConsumptionMeter** je ID měřiče pro virtuální počítač.
3. **Rezervované základní virtuální počítač** **podkategorie měřiče** řádek představuje náklady hodnotou 0 USD v části prohlášení. Náklady na provozování tento virtuální počítač je už zaplaceno rezervace.
4. **ID měřiče** je ID měřiče pro rezervaci. Náklady na tento měřič je hodnotou 0 USD. Toto id měřiče se zobrazí pro jakýkoli virtuální počítač, který může využít sleva za rezervaci.
5. Standard_DS1_v2 je jeden virtuální procesor virtuálního počítače a virtuálního počítače se nasazují bez programu Azure Hybrid Benefit. Tedy tento měřič zahrnuje korunu navíc softwaru Windows. Měření odpovídající 1 jádro virtuálních počítačů řady D series najdete v tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure](billing-reserved-instance-windows-software-costs.md). Pokud máte na program Azure Hybrid Benefit se nepoužije tento další poplatek.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Využití pro SQL Database a Cosmos DB vyhrazené kapacity rezervace

V dalších částech používat Azure SQL Database jako příklad k popisu sestavu využití. Stejný postup můžete použít také zobrazíte využití služby Azure Cosmos DB.

Předpokládejme, že používáte SQL Database Gen 4 oblasti USA – východ a vaší rezervace informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Množství |2|
|Product| SQL Database Gen 4 (2 jádra)|
|Oblast | eastus |

### <a name="statement-section-of-csv-file"></a>Příkaz části ze souboru CSV

Filtrovat podle **využití rezervovaných instancí** měřit název a zvolte požadovaný **kategorie měřiče** – Azure SQL database nebo Azure Cosmos DB. Zobrazit něco jako na následujícím snímku obrazovky:

![Soubor CSV pro službu SQL Database rezervované kapacity](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

**Využití rezervovaných instancí** řádek obsahuje celkový počet předmětem rezervace – základní hodiny. Tato míra je hodnotou 0 USD pro tento řádek jako zahrnuté náklady na rezervaci.

### <a name="detail-section-of-csv-file"></a>Tělo ze souboru CSV

Filtrovat podle **Další informace o** a zadejte vaše **ID rezervace**. Následující snímek obrazovky ukazuje pole související s rezervaci kapacity databáze SQL vyhrazený.

![Soubor CSV pro službu SQL Database rezervované kapacity](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** v **Další informace o** pole je rezervace SQL Database vyhrazené kapacity, který se použije pro prostředek databáze SQL.
2. **ConsumptionMeter** je ID měřiče pro prostředek databáze SQL.
3. **Id měřiče** je měřič rezervace. Náklady na tento měřič je hodnotou 0 USD. Všechny prostředky databáze SQL, které jsou způsobilé pro sleva za rezervaci se zobrazí toto ID měřiče v souboru CSV.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)