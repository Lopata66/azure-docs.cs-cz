---
title: Optimalizovat náklady na prostředky Azure Cosmos DB pomocí vyhrazené kapacity
description: Informace o možnostech nákupu služby Azure Cosmos DB vyhrazené kapacity a Ušetřete na svých výpočetních nákladech.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7944980ec1806d2c8c4ab908c71efd971ee0d7aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968954"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizace nákladů s využitím rezervované kapacity ve službě Azure Cosmos DB

Azure Cosmos DB vyhrazené kapacity je využíván Šetřete peníze optimalizací pro jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. S Azure Cosmos DB vyhrazené kapacity můžete získat a uplatnit tak slevu na zajištěné propustnosti pro prostředky Cosmos DB. Příklady prostředků jsou databáze a kontejnerů (tabulek, kolekcí a grafy).

Azure Cosmos DB vyhrazené kapacity může výrazně snížit náklady na služby Cosmos DB&mdash;až 65 procent u běžných cen se předem k ničemu zavázat jeden rok nebo tři roky. Záložní kapacitu poskytuje fakturace slevy a neovlivní jejich běhový stav vašich prostředků Azure Cosmos DB.

Azure Cosmos DB vyhrazené kapacity se věnuje zajištěné propustnosti pro vaše prostředky. Nezahrnuje úložiště a sítě poplatky. Poté, co můžete koupit rezervaci, propustnost poplatky, které odpovídají rezervace, které atributy jsou už účtovat platit jako budete přejít sazby. Další informace o rezervacích, najdete v článku [Azure rezervace](../billing/billing-save-compute-costs-reservations.md) článku.

Můžete si koupit službu Azure Cosmos DB vyhrazené kapacity z [webu Azure portal](https://portal.azure.com). Koupit rezervované kapacity:

* Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.  
* Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA na předplatné.
* Programu Cloud Solution Provider (CSP) můžete koupit jenom správce agentů nebo prodejní agenty služby Azure Cosmos DB vyhrazené kapacity.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost rezervace by měla vycházet z celkové množství propustnost, které budou používat existující nebo brzy na--nasadit prostředky Azure Cosmos DB. Požadované propustnosti může určit následujícími způsoby:

* Získáte historická data pro celková zřízená propustnost mezi účty Azure Cosmos DB, databáze a kolekce ve všech oblastech. Například můžete vyhodnotit denní průměrný zřízená propustnost stažením váš denní využití výpis z `https://account.azure.com`.

* Pokud jste zákazník se smlouvou Enterprise (EA), si můžete stáhnout soubor využití pro získání podrobností o propustnosti služby Azure Cosmos DB. Odkazovat **typ služby** hodnota v **Další informace o** část souboru využití.

* Průměrná propustnost pro všemi úlohami může sečíst na vaše účty služby Azure Cosmos DB, které očekáváte, že ke spuštění pro další jeden nebo tři roky. Můžete pak toto množství pro rezervaci.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit služby Azure Cosmos DB vyhrazené kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny služby** > **rezervace** > **přidat**.  

3. Z **vybrat typ produktu** podokně zvolte **služby Azure Cosmos DB** > **vyberte** koupit novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png)

   |Pole  |Popis  |
   |---------|---------|
   |Name   |    Název rezervace. Toto pole se automaticky vyplní `CosmosDB_Reservation_<timeStamp>`. Zadejte jiný název při vytváření rezervace. Nebo můžete ji jakkoli přejmenovat po vytvoření rezervace.      |
   |Předplatné  |   Předplatné, které se používá k úhradě služby Azure Cosmos DB rezervované kapacity. Způsob platby u vybraného předplatného se používá v účtování pořizovací náklady. Tento typ předplatného musí být jeden z následujících akcí: <br/><br/>  Smlouvy Enterprise (nabízejí čísla: MS-AZR-0017P or MS-AZR-0148P): Předplatné Enterprise jsou poplatky odečteno od zůstatku peněžním závazkem registrace nebo účtovat jako překročení částky. <br/><br/> Průběžné platby (nabízejí čísla: MS-AZR-0003P or MS-AZR-0023P): Pro předplatné s průběžnými platbami se poplatky účtují kreditní kartou nebo fakturou způsobu platby v rámci předplatného.    |
   |Scope   |   Možnost, která určuje, kolik předplatných fakturační benefit přidružené k rezervaci můžete využít. Také určuje, jak se má rezervace použít na konkrétní předplatné.   <br/><br/>  Pokud vyberete **jedno předplatné**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB ve vybraném předplatném. <br/><br/>  Pokud vyberete **Shared**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB, které běží v kterékoli předplatné v rámci vaší fakturační kontextu. Kontext fakturace je založená na tom, jak jste zaregistrovali službu Azure. Pro podnikové zákazníky sdílený obor je registrace a obsahuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.  <br/><br/> Po nákupu rezervované kapacity můžete změnit obor rezervace.  |
   |Typ rezervované kapacity   |  Jako jednotky žádostí zřízenou propustnost. Můžete si koupit rezervaci zřízená propustnost pro obě nastavení - jedné oblasti zapíše i více oblastí zápisu.|
   |Jednotky rezervované kapacity  |      Množství propustnost, které chcete rezervovat. Tuto hodnotu můžete vypočítat tak, že určíte propustnost potřebné pro všechny vaše služby Cosmos DB prostředky (například databáze nebo kontejnery) v jedné oblasti. Můžete pak vynásobit se podle počtu oblastí, které budete přidružíte k databázi Cosmos DB.  <br/><br/> Příklad: Pokud máte pět oblastí s 1 milion RU/s v každé oblasti, vyberte 5 milionů RU/s pro nákup rezervace kapacitu.    |
   |Termín  |   Jeden nebo tři roky.   |

5. Sleva a cenu za rezervaci v **náklady** oddílu. Tato rezervace ceny platí pro prostředky Azure Cosmos DB s propustností zřízené ve všech oblastech.  

6. Vyberte **Koupit**. Po úspěšné nákupu, získáte na následující stránce:

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png)

Po nákupu rezervace se okamžitě použije na nějaké stávající prostředky Azure Cosmos DB, které odpovídají podmínkám použití rezervace. Pokud nemáte k dispozici nějaké stávající prostředky Azure Cosmos DB, rezervace platit, když nasadíte novou instanci služby Cosmos DB, která odpovídá podmínky rezervace. V obou případech se období rezervace začne hned po úspěšném nákupu.

Když vyprší platnost vaší rezervace, nadále spouštět vaše instance služby Azure Cosmos DB a jsou účtovány za běžné sazby průběžných plateb.

## <a name="cancellation-and-exchanges"></a>Zrušení a výměny

Pomoc při identifikaci přímo rezervované kapacity najdete v tématu [pochopit, jak sleva za rezervaci se použije ke službě Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Pokud budete muset zrušit nebo exchange rezervací služby Azure Cosmos DB, najdete v článku [výměny rezervaci a vrácení peněz](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Další postup

Sleva za rezervaci se automaticky využije na prostředky Azure Cosmos DB, které odpovídají obor rezervace a atributy. Rozsah rezervace prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API můžete aktualizovat.

*  Zjistěte, jak záložní kapacitu slevy na služby Azure Cosmos DB, najdete v článku [pochopit slevy na rezervaci Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Další informace o Azure rezervace, naleznete v následujících článcích:

   * [Co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md)  
   * [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu Partnerské centrum CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
