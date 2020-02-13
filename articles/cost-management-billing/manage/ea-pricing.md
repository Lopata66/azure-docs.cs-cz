---
title: Zobrazení a stažení cen Azure pro vaši organizaci
description: Zjistěte, jak zobrazit a stáhnout informace o cenách nebo odhadnout náklady s použitím cen pro vaši organizaci.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 72ad7b13010e1369e3e0e4d31ade089012aaf8f3
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132867"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Zobrazení a stažení cen Azure pro vaši organizaci

Zákazníci Azure se smlouvou Azure Enterprise (EA), smlouvou se zákazníkem Microsoftu (MCA) nebo smlouvou s partnerem Microsoftu (MPA) můžou zobrazit a stáhnout informace o cenách na webu Azure Portal. [Zjistěte, jak zkontrolovat typ vašeho fakturačního účtu](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Stažení informací o cenách pro smlouvu Enterprise

V závislosti na zásadách nastavených pro vaši organizaci podnikovým správcem mají přístup k informacím o cenách EA pro vaši organizaci pouze určité role pro správu. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

1. Jako podnikový správce se přihlaste k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte *Cost Management a fakturace*.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/ea-pricing/portal-cm-billing-search.png)

1. Ve fakturačním účtu vyberte **Využití a poplatky**.

   ![Snímek obrazovky ukazující možnost Využití a poplatky v části Fakturace](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. U požadovaného měsíce vyberte ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/ea-pricing/download-icon.png) **Stáhnout**.

1. V části **Ceník** vyberte **Stáhnout CSV**.

   ![Snímek obrazovky ukazující tlačítko Stáhnout CSV v části Ceník](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Stažení informací o cenách pro účet se smlouvou MCA nebo MPA

Pokud máte smlouvu MCA a chcete zobrazit a stáhnout informace o cenách, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Pokud máte smlouvu MPA a chcete zobrazit a stáhnout informace o cenách, musíte mít roli Globální správce nebo Agent správy v partnerské organizaci.

### <a name="download-price-sheets-for-billed-charges"></a>Stažení ceníků účtovaných poplatků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V tabulce faktur vyhledejte řádek faktury odpovídající ceníku, který chcete stáhnout.
1. Klikněte na tři tečky (`...`) na konci řádku.
![Snímek obrazovky ukazující vybrané tři tečky](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. Pokud chcete zobrazit ceny služeb na vybrané faktuře, vyberte **Ceník faktury**.
1. Pokud chcete zobrazit ceny všech služeb Azure pro dané fakturační období, vyberte **Ceník Azure**.

![Snímek obrazovky s místní nabídkou ceníků](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Stažení ceníků pro aktuální fakturační období

Pokud máte smlouvu MCA, můžete si stáhnout informace o cenách pro aktuální fakturační období.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. V oblasti **Přehled** najdete odkazy ke stažení pod poplatky od začátku měsíce.
1. Vyberte **Ceník Azure**.
![Snímek obrazovky znázorňující stažení v části Přehled](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Odhad nákladů pomocí cenové kalkulačky Azure

Informace o cenách pro vaši organizaci můžete využít také k odhadu nákladů pomocí cenové kalkulačky Azure.

1. Přejděte na [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator).
1. V pravém horním rohu vyberte **Přihlásit se**.
1. V části **Programy a nabídky** > **Licenční program** vyberte **Smlouva Enterprise (EA)** .
1. V části **Programy a nabídky** > **Vybraná smlouva** vyberte **Nic není vybrané**.

    ![Snímek obrazovky ukazující tlačítko Stáhnout CSV v části Ceník](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. Zvolte organizaci.
1. Vyberte **Použít**.
1. Vyhledejte produkty a přidejte je k odhadu.
1. Uvedené odhadované ceny vycházejí z cen pro vybranou organizaci.

## <a name="check-your-billing-account-type"></a>Kontrola typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Další kroky

Pokud jste zákazníkem se smlouvou EA, projděte si tato témata:

- [Správa přístupu k fakturačním údajům smlouvy EA v Azure](manage-billing-access.md)
- [Zobrazení a stažení faktury za Microsoft Azure](../understand/download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích za Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře pro zákazníky se smlouvou Enterprise](../understand/review-enterprise-agreement-bill.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení výrazů v ceníku pro smlouvu se zákazníkem Microsoftu](mca-understand-pricesheet.md)
- [Zobrazení a stažení faktury za Microsoft Azure](../understand/download-azure-invoice.md)
- [Zobrazení a stažení informací o využití a poplatcích Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](../understand/mca-download-tax-document.md)
- [Vysvětlení poplatků na faktuře pro váš fakturační profil](../understand/review-customer-agreement-bill.md)
