---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08/29/2019
ms.openlocfilehash: 29cf947d1e9d26460dc34a6417e76b68bb75e9dc
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005498"
---
Po potvrzení služby na rezervované instance virtuálního počítače Azure můžete ušetřit peníze. Sleva rezervace se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají oboru a atributům rezervace. K získání slevy není nutné přiřadit rezervaci k virtuálnímu počítači. Zakoupení rezervované instance pokrývá jenom výpočetní část využití virtuálního počítače. V případě virtuálních počítačů s Windows je měřič využití rozdělen do dvou samostatných měřičů. K dispozici je výpočetní měřič, který je stejný jako měřič pro Linux, a měřič IP adres systému Windows. Poplatky, které se zobrazí při nákupu, budou platit jenom za výpočetní náklady. Poplatky nezahrnují náklady na software systému Windows. Další informace o nákladech na software najdete v části [náklady na software nejsou součástí Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Určení správné velikosti virtuálních počítačů před nákupem

Před nákupem rezervace byste měli určit velikost virtuálního počítače, který potřebujete. Následující části vám pomůžou určit správnou velikost virtuálního počítače.

### <a name="use-reservation-recommendations"></a>Použití doporučení rezervací

K určení rezervací, které byste měli koupit, můžete použít doporučení rezervací.

- Doporučení nákupu a doporučené množství se zobrazí při nákupu rezervované instance virtuálního počítače v Azure Portal.
- Azure Advisor poskytuje doporučení pro nákup pro jednotlivá předplatná.  
- Rozhraní API můžete použít k získání doporučení pro nákup jak pro sdílený obor, tak pro obor jednoho předplatného. Další informace najdete v tématu [rozhraní API pro doporučení pro nákup rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pro zákazníky smlouva Enterprise (EA) a Microsoft Customer Agreement (MCA) jsou k dispozici doporučení pro sdílení a jednotné rozsahy, které jsou k dispozici v [balíčku obsahu Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Služby, které získávají slevy za rezervaci virtuálních počítačů

Rezervace virtuálních počítačů se můžou vztahovat na využití virtuálních počítačů z několika služeb, nejen na vaše nasazení virtuálních počítačů. To, které prostředky získávají slevy za rezervované instance, se liší v závislosti na nastavení flexibility velikosti instance.

#### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance určuje, které služby mají získat slevy za rezervované instance.

Když má položka *ConsumedService* hodnotu `Microsoft.Compute`, slevy za rezervace se automaticky uplatňují na veškeré využití vyhovujících virtuálních počítačů bez ohledu na to, jestli je nastavení zapnuté, nebo vypnuté. Proto si u hodnoty *ConsumedService* kontrolujte údaje o využití. Možné příklady:

- Virtual Machines
- Škálovací sady virtuálních počítačů
- Služba kontejneru
- Nasazení služby Azure Batch (v režimu předplatných uživatelů)
- Azure Kubernetes Service (AKS)
- Service Fabric

Když je nastavení zapnuté a položka *ConsumedService* má některou z následujících hodnot, na veškeré využití vyhovujících virtuálních počítačů se automaticky uplatňují slevy za rezervace:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

V datech využití si zkontrolujte hodnotu *ConsumedService*, abyste zjistili, jestli má dané využití nárok na slevy za rezervace.

Další informace o flexibilitě velikosti instance najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analýza informací o použití
Analýza informací o použití vám pomůže určit, které rezervace byste měli koupit.

Data o využití jsou k dispozici v souboru využití a v rozhraních API. Použijte je společně k určení rezervace k nákupu. Zkontrolujte, jestli jsou instance virtuálních počítačů, které mají každodenní vysoké využití, na denní úrovni a určete množství rezervací, které se mají koupit.

Vyhněte se `Meter` podkategorií a `Product` polí v datech o využití. Nerozlišují se mezi velikosti virtuálních počítačů, které používají službu Premium Storage. Pokud používáte tato pole k určení velikosti virtuálního počítače pro nákup rezervace, můžete si koupit špatnou velikost. Pak nezískáte zlevněnou rezervaci, kterou očekáváte. Místo toho použijte k určení správné velikosti virtuálního počítače pole `AdditionalInfo` v souboru využití nebo rozhraní API využití.

### <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Rezervované instance virtuálních počítačů jsou k dispozici pro většinu velikostí virtuálních počítačů s několika výjimkami. Pro následující virtuální počítače se neplatí žádné slevy za rezervované:

- **Řady virtuálních počítačů** – řady a-Series, Av2-Series nebo G-series.

- **Verze Preview nebo propagační virtuální počítače** – jakákoli řada virtuálních počítačů nebo velikost, která je ve verzi Preview nebo používá propagační měření.

- **Cloudy** – rezervace nejsou k dispozici pro nákup v oblastech Německo a Čína.

- **Nedostatečná kvóta** – rezervace, která je vymezená v rámci jednoho předplatného, musí mít v předplatném k dispozici vCPUou kvótu pro nové rezervované instance. Pokud má například cílové předplatné omezení kvóty 10 vCPU pro řady D-Series, nemůžete koupit rezervaci pro 11 Standard_D1 instance. Kontroly kvóty pro rezervace zahrnují virtuální počítače, které jsou už v předplatném nasazené. Pokud má například předplatné kvótu 10 vCPU pro řady D a má nasazené dvě instance standard_D1, můžete si koupit rezervaci pro 10 standard_D1 instancí v tomto předplatném. Chcete-li vyřešit tento problém, můžete [vytvořit žádost o zvýšení poptávky](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Omezení kapacity** – Azure omezuje nákup nových rezervací pro podmnožinu velikostí virtuálních počítačů z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reserved-vm-instance"></a>Zakoupení rezervované instance virtuálního počítače

Rezervovanou instanci virtuálního počítače můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../articles/billing/billing-monthly-payments-reservations.md).

Tyto požadavky se vztahují k zakoupení rezervované instance virtuálního počítače:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.
- Pro předplatná EA musí být na [portálu EA](https://ea.azure.com/)povolená možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA pro předplatné.
- Pro program poskytovatele Cloud Solution Provider (CSP) mohou koupit rezervace pouze agenti správce nebo prodejní agenti.

Zakoupení instance:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Vyberte **Všechny služby** > **Rezervace**.
1. Vyberte **Přidat** a Zakupte novou rezervaci a pak klikněte na **virtuální počítač**.
1. Zadejte požadovaná pole. Pokud chcete získat slevu rezervace, spusťte instance virtuálních počítačů, které odpovídají vybraným atributům. Skutečný počet instancí virtuálních počítačů, které obdrží slevu, závisí na zvoleném rozsahu a množství.

| Pole      | Popis|
|------------|--------------|
|Předplatné|Předplatné použité pro platbu za rezervaci. Platební metodou předplatného se účtují náklady na rezervaci. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P), zákaznická smlouva Microsoftu nebo jednotlivá předplatná s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se odečtou z bilance peněžního závazku, pokud je k dispozici, nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v rámci předplatného.|    
|Rozsah       |Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <ul><li>Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.</li><li>Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.</li><li>Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U jednotlivých předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>|
|Region (Oblast)    |Oblast Azure, která je pokrytá rezervací.|    
|Velikost virtuálního počítače     |Velikost instancí virtuálních počítačů.|
|Optimalizovat pro     |Ve výchozím nastavení je vybraná flexibilita velikosti instancí virtuálních počítačů. Klikněte na **Upřesnit nastavení** a změňte hodnotu flexibilita velikosti instance tak, aby se na jiné virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)použili sleva rezervace. Při použití priority kapacity se pro vaše nasazení upřednostňuje kapacita datového centra. Nabízí další důvěru ve vaší schopnosti spouštět instance virtuálních počítačů, když je potřebujete. Priorita kapacity je dostupná jenom v případě, že obor rezervací je jedno předplatné. |
|Označení        |Jeden rok nebo tři roky.|
|Množství    |Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných instancí virtuálních počítačů, které můžou získat fakturační slevu. Pokud například používáte 10 Standard_D2 virtuálních počítačů v Východní USA, pak zadáte množství jako 10 pro maximalizaci výhod všech spuštěných virtuálních počítačů. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Využití a data využití rezervací

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. Můžete zjistit, která instance virtuálního počítače obdržela slevu za rezervaci pro každou rezervaci.

Další informace o tom, jak se v datech využití zobrazují slevy, najdete v tématu [vysvětlení využívání rezervací Azure pro vaši podnikovou registraci](../articles/billing/billing-understand-reserved-instance-usage-ea.md) , pokud jste zákazníkem EA. Pokud máte individuální předplatné, přečtěte si téma [vysvětlení využití rezervace Azure pro předplatné](../articles/billing/billing-understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po nákupu

V zakoupené rezervaci můžete provádět následující typy změn:

- Aktualizace rozsahu rezervace
- Flexibilita velikosti instance (Pokud je k dispozici)
- Jejíž

Rezervaci můžete také rozdělit do menších bloků dat a sloučení už rozdělené rezervace. Žádná z těchto změn nezpůsobí novou komerční transakci ani nemění koncové datum rezervace.

Po nákupu nemůžete provést následující typy změn přímo:

- Existující oblast rezervace
- Skladová položka
- Množství
- Doba trvání

Můžete ale *vyměnit* rezervaci, pokud chcete provést změny.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Co jsou rezervace Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Vysvětlení způsobu uplatnění slevy za rezervaci](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software pro Windows nezahrnuté v rezervacích](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
