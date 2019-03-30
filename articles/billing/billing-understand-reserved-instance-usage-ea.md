---
title: Vysvětlení využití Azure rezervace pro podniky | Dokumentace Microsoftu
description: Zjistěte, jak číst využití, abyste pochopili, jak se má rezervace Azure u prováděcí smlouvy Enterprise použít.
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
ms.openlocfilehash: daa7f6a116578fa8d1f2b5bf825a6f4cd48f7f64
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648295"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Vysvětlení využití rezervace Azure u prováděcí smlouvy Enterprise

Použití **ReservationId** z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a používání souboru z [portál EA](https://ea.azure.com) vyhodnotit využití rezervace. Můžete také zobrazit využití rezervace v souhrnné části využití [portál EA](https://ea.azure.com).

Pokud jste si zakoupili rezervace v kontextu s průběžnými platbami fakturace, najdete v článku [porozumět používání rezervace pro vaše předplatné s průběžnými platbami.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Využití instancí rezervovaných virtuálních počítačů

V dalších částech předpokládají, že používáte virtuální počítač Standard_D1_v2 Windows oblasti USA – východ a vaší rezervace informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Množství |1|
|Skladová jednotka (SKU) | Standard_D1|
|Oblast | eastus |

Část hardwaru virtuálního počítače se vztahuje, protože nasazený virtuální počítač odpovídající rezervaci atributy. Pokud chcete zobrazit, jaký software Windows není předmětem rezervace, naleznete v tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Použití v souboru CSV pro rezervované instance virtuálních počítačů

Soubor CSV využití Enterprise si můžete stáhnout z webu Enterprise portal. V souboru CSV, vyfiltrujte **Další informace o** a zadejte vaše **ReservationID**. Následující snímek obrazovky ukazuje pole související s rezervaci:

![Sdílený svazek clusteru Enterprise Agreement (EA) pro Azure rezervace](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** v **Další informace o** pole představuje rezervace, které se použije k virtuálnímu počítači.
2. **ConsumptionMeter** je ID měřiče pro virtuální počítač.
3. **ID měřiče** je měřič rezervace s hodnotou 0 USD náklady. Náklady na spuštěný virtuální počítač je placené rezervované instance virtuálního počítače.
4. Standard_D1 je jeden virtuální procesor virtuálního počítače a virtuálního počítače se nasazují bez programu Azure Hybrid Benefit. Proto tento měřič zahrnuje korunu navíc softwaru Windows. Měření odpovídající 1 jádro virtuálních počítačů řady D series najdete v tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure](billing-reserved-instance-windows-software-costs.md).  Pokud máte na program Azure Hybrid Benefit se nepoužije tento další poplatek.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Využití pro SQL Database a Cosmos DB vyhrazené kapacity rezervace

V dalších částech používat Azure SQL Database jako příklad k popisu sestavu využití. Stejný postup můžete použít také zobrazíte využití služby Azure Cosmos DB.

Předpokládejme, že používáte SQL Database Gen 4 oblasti USA – východ a vaší rezervace informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Množství |2|
|Produkt| SQL Database Gen 4 (2 jádra)|
|Oblast | eastus |

### <a name="usage-in-csv-file"></a>Použití v souboru CSV

Filtrovat podle **Další informace o** a zadejte vaše **ID rezervace**a zvolte požadovaný **kategorie měřiče** – Azure SQL database nebo Azure Cosmos DB. Následující snímek obrazovky ukazuje pole související s rezervace.

![Sdílený svazek clusteru Enterprise Agreement (EA) pro službu SQL Database rezervované kapacity](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** v **Další informace o** pole je rezervace, které se použije pro prostředek databáze SQL.
2. **ConsumptionMeter** je ID měřiče pro prostředek databáze SQL.
3. **ID měřiče** je měřič rezervace s hodnotou 0 USD náklady. Prostředek databáze SQL, která kvalifikuje pro rezervaci ukazuje toto ID měřiče v souboru CSV.

## <a name="usage-summary-page-in-enterprise-portal"></a>Stránka souhrnu využití webu Enterprise Portal

Využití Azure rezervace se také zobrazí v části Souhrn využití webu Enterprise portal: ![Souhrn využití Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Se vám neúčtují poplatky za součást hardwaru virtuálního počítače jako se bude vztahovat rezervace. Pro rezervaci SQL Database, se zobrazí čáry s **název služby** jako Azure SQL Database vyhrazené využití kapacity.
2. V tomto příkladu nemáte zvýhodněné hybridní využití Azure, takže vám budeme účtovat softwaru Windows používá s virtuálním Počítačem.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).


## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)

