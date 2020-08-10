---
title: Správa rezervací Azure
description: Zjistěte, jak spravovat rezervace Azure. Projděte si postup změny rozsahu rezervace, rozdělení rezervace a optimalizace využití rezervace.
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: f3e4a772382606178d6cd5b0dcb92b0d1bc28695
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461812"
---
# <a name="manage-reservations-for-azure-resources"></a>Správa rezervací prostředků Azure

Po nákupu rezervace Azure můžete tuto rezervaci potřebovat použít pro jiné předplatné, změnit osobu, která ji může spravovat, nebo změnit rozsah rezervace. Rezervaci také můžete rozdělit na dvě rezervace, abyste mohli některé ze zakoupených instancí použít pro jiné předplatné.

Když si koupíte rezervované instance virtuálních počítačů Azure, můžete změnit nastavení optimalizace u této rezervace. Slevu na rezervaci lze uplatnit na virtuální počítače ve stejné řadě, nebo si můžete rezervovat kapacitu datového centra pro konkrétní velikost virtuálního počítače. Měli byste se také pokusit rezervace optimalizovat, aby byly plně využívány.

*Oprávnění nutná ke správě rezervace je oddělené od oprávnění k předplatnému.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Objednávka rezervace a rezervace

Při nákupu rezervace se vytvoří dva objekty: **Objednávka rezervace** a **Rezervace**.

V době nákupu má pod sebou objednávka rezervace jednu rezervaci. Při rozdělení, sloučení, částečné refundaci nebo výměně se pod **objednávkou rezervace** vytvářejí nové rezervace.

Objednávku rezervace zobrazíte tak, že přejdete na **Rezervace** > vyberete rezervaci a pak kliknete na **ID objednávky rezervace**.

![Příklad podrobností objednávky rezervace znázorňující ID objednávky rezervace ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Rezervace dědí oprávnění od své objednávky rezervace.

## <a name="change-the-reservation-scope"></a>Změna rozsahu rezervace

 Sleva za rezervaci se uplatňuje na virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky, které odpovídají vaší rezervaci a provozují se v daném rozsahu rezervace. Kontext fakturace závisí na předplatném použitém k zakoupení rezervace.

Rozsah rezervace aktualizujete takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte rezervaci.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změňte rozsah.

Pokud přejdete ze sdíleného rozsahu na jeden rozsah, můžete vybrat jen předplatná, jejichž jste vlastníkem. Je možné vybrat pouze předplatná ve stejném kontextu fakturace jako rezervace.

Rozsah se vztahuje jen na individuální předplatná s průběžnými platbami (nabídky MS-AZR-0003P nebo MS-AZR-0023P), nabídku Enterprise MS-AZR-0017P nebo MS-AZR-0148P nebo předplatná typu CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci

Správu rezervace můžete delegovat přidáním lidí do rolí rezervace nebo objednávky rezervace. Ve výchozím nastavení má roli vlastníka rezervace nebo objednávky rezervace osoba, která vytvořila objednávku rezervace, a správce účtu.

Přístup k objednávkám rezervací a rezervacím můžete spravovat *nezávisle na předplatných*, která obdrží slevu za rezervaci. Když někomu udělíte oprávnění ke správě objednávky rezervace nebo rezervace, neudělíte tím oprávnění ke správě předplatného. Podobně platí, že pokud někomu udělíte oprávnění ke správě předplatného v rozsahu rezervace, neudělíte tím práva ke správě objednávky rezervace nebo rezervace.

Aby bylo možné provést výměnu nebo refundaci, musí mít uživatel přístup k objednávce rezervace. Když někomu udělujete oprávnění, je nejlepší udělit oprávnění k objednávce rezervace, nikoli k rezervaci.

Přístup ke správě rezervace delegujete takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Výběrem **Všechny služby** > **Rezervace** zobrazte seznam rezervací, ke kterým máte přístup.
3. Vyberte rezervaci, ke které chcete delegovat přístup jiným uživatelům.
4. Vyberte **Řízení přístupu (IAM)** .
5. Vyberte **Přidat přiřazení role** > **Role** > **Vlastník**. Pokud chcete udělit omezený přístup, vyberte jinou roli.
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělení jedné rezervace do dvou rezervací

 Po zakoupení více než jedné instance prostředku v rámci rezervace můžete instance v této rezervaci přiřadit k různým předplatným. Všechny instance mají standardně jeden rozsah – buď pro jedno předplatné, nebo sdílený. Koupili jste například 10 instancí rezervace a určili, že rozsahem má být předplatné A. Teď chcete u sedmi rezervací změnit rozsah na předplatné A a u zbývajících tří na předplatné B. Rozdělením rezervace můžete distribuovat instance kvůli lepšímu odstupňování správy rozsahu. Přidělení k předplatným můžete zjednodušit volbou sdíleného rozsahu. Pro účely řízení nákladů nebo rozpočtů ale můžete množství přidělit ke konkrétním předplatným.

 Rezervaci můžete rozdělit do dvou rezervací přes PowerShell, rozhraní příkazového řádku nebo rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervace pomocí PowerShellu

1. Spuštěním následujícího příkazu zjistěte ID objednávky rezervace:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Získejte podrobnosti o rezervaci:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělte rezervaci na dvě a distribuujte tyto instance:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Rozsah můžete aktualizovat spuštěním následujícího příkazu:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Změna nastavení optimalizace rezervovaných instancí virtuálních počítačů

 Při nákupu rezervované instance virtuálního počítače volíte flexibilní velikost instance nebo prioritu kapacity. Při flexibilní velikosti instance se sleva za rezervaci uplatňuje na jiné virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Při použití priority kapacity se pro vaše nasazení upřednostňuje kapacita datového centra. Při použití této možnosti máte větší jistotu, že instance virtuálních počítačů spustíte v době, kdy je potřebujete.

Pokud je rozsah rezervace sdílený, je standardně zapnutá flexibilní velikost instance. Pro nasazení virtuálních počítačů se neupřednostňuje kapacita datového centra.

U rezervací s jedním rozsahem můžete rezervaci optimalizovat pro prioritu kapacity místo flexibilní velikosti instance virtuálního počítače.

Nastavení optimalizace rezervace aktualizujete takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte rezervaci.
4. Vyberte **Nastavení** > **Konfigurace**.  
  ![Příklad znázorňující položku konfigurace](./media/manage-reserved-vm-instance/add-product03.png)
5. Změňte nastavení **Optimalizovat pro**.  
  ![Příklad znázorňující nastavení Optimalizovat pro](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Optimalizace využití rezervace

Úspory z rezervací Azure vyplývají jen z trvalého využívání prostředků. Při nákupu rezervace zaplatíte náklady na prakticky 100% možné využití prostředku v jednoletém nebo tříletém období. Pokuste se svou rezervaci maximalizovat, abyste dosáhli co nejlepšího využití a úspory. Následující části vysvětlují, jak rezervaci monitorovat a optimalizovat její využití.

### <a name="view-reservation-use-in-the-azure-portal"></a>Zobrazení využití rezervace na webu Azure Portal

Jeden ze způsobů, jak zobrazit využití rezervace, představuje Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > [**Rezervace**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a podívejte se u rezervace na **Využití (%)** .  
  ![Obrázek ukazující seznam rezervací](./media/manage-reserved-vm-instance/reservation-list.png)
3. Vyberte některou rezervaci.
4. Podívejte se na trend využití rezervace v průběhu času.  
  ![Obrázek ukazující využití rezervace ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Zobrazení využití rezervace pomocí rozhraní API

Pokud jste zákazníkem se smlouvou Enterprise (EA), můžete využití rezervací ve vaší organizaci zobrazit prostřednictvím programu. Nevyužité rezervace zjistíte prostřednictvím dat o využití. Při prohlížení poplatků za rezervace mějte na paměti, že tato data jsou rozdělena mezi skutečné náklady a amortizované náklady. Skutečné náklady poskytují data pro uvedení do souladu s vaším měsíčním vyúčtováním. Obsahují také náklady na nákup rezervací a podrobnosti k žádostem o rezervace. Amortizované náklady jsou stejné jako skutečné náklady až na to, že platná cena za využití rezervace je poměrná. V datech amortizovaných nákladů se zobrazuje čas nevyužité rezervace. Další informace o datech využití pro zákazníky EA najdete v článku [Získání nákladů na rezervace a jejich využití u smlouvy Enterprise](understand-reserved-instance-usage-ea.md).

Pro jiné typy předplatných použijte rozhraní API [pro souhrny rezervací – seznam podle objednávek rezervace a rezervací](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optimalizace rezervace

Pokud zjistíte, že se rezervace ve vaší organizaci dostatečně nevyužívají:

- Ujistěte se, že virtuální počítače, které vaše organizace vytváří, odpovídají velikosti virtuálních počítačů v dané rezervaci.
- Ověřte, že je zapnutá flexibilní velikost instance. Další informace najdete v článku [Správa rezervací – změna nastavení optimalizace u rezervovaných instancí virtuálních počítačů](#change-optimize-setting-for-reserved-vm-instances).
- Změňte rozsah rezervace na _sdílený_, aby se rezervace uplatnila ve větší šíři. Další informace najdete v článku [Změna rozsahu rezervace](#change-the-reservation-scope).
- Zvažte výměnu nevyužitého množství. Další informace najdete v článku [Zrušení a výměny](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace v Azure?](save-compute-costs-reservations.md)

Zakoupení plánu služby:
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Předplacení prostředků Azure Cosmos DB pomocí rezervované kapacity služby Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Zakoupení plánu softwaru:
- [Předplacení plánů softwaru Red Hat z rezervací Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Principy slevy a využití:
- [Vysvětlení způsobu uplatnění slevy za rezervaci virtuálních počítačů](../manage/understand-vm-reservation-charges.md)
- [Vysvětlení způsobu uplatnění slevy pro plán softwaru Red Hat Enterprise Linux](understand-rhel-reservation-charges.md)
- [Vysvětlení způsobu uplatnění slevy pro plán softwaru SUSE Linux Enterprise](understand-suse-reservation-charges.md)
- [Vysvětlení způsobu uplatnění slev za jiné rezervace](understand-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
