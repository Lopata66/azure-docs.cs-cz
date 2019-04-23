---
title: Plány údržby Azure (preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat události nezbytné plánované údržby, které služba Azure SQL Data Warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790854"
---
# <a name="change-a-maintenance-schedule"></a>Změnit plán údržby 

## <a name="portal"></a>Portál
Plán údržby můžete aktualizovat nebo kdykoli změnit. Pokud je vybraná instance prostřednictvím active údržby cyklu, nastavení se uloží. Po budete své aktivaci během dalšího období údržby identifikovaní. [Další informace](https://docs.microsoft.com/azure/service-health/resource-health-overview) o monitorování služby data warehouse během události údržby aktivní. 

Použití plány údržby, musíte vybrat dvě okna údržby v období 7 dnů. Každé časové období údržby, může být tři až osm hodin. Údržba může probíhat kdykoli během časového období údržby, ale nedojde mimo časová okna bez předchozího upozornění. Budete také zaznamenáte stručný ke ztrátě připojení jako službu na váš datový sklad nasadí nový kód. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Určení primárního a sekundárního systému windows

Primární a sekundární windows musí mít samostatné rozsahů. Příkladem je okno úterý – čtvrtek na primární a sekundární okna sobota – neděle.

Chcete-li změnit plán údržby pro datový sklad, proveďte následující kroky:
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete aktualizovat. Stránka se otevře v okně přehledu. 
3.  Otevřete stránku pro nastavení plánu údržby tak, že vyberete **plán údržby (preview) Souhrn** odkaz na kartě s přehledem. Nebo vyberte **plán údržby** možnost v nabídce na levé straně prostředku.  

    ![Přehled okno Možnosti](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Určete rozsah Upřednostňovaný den pro okno údržby primární pomocí možností v horní části stránky. Tento výběr určuje, pokud primární okno dojde na jeden den v týdnu nebo přes víkend pokazil. Váš výběr se aktualizujte hodnoty rozevíracího seznamu. Ve verzi preview, nemusí některé oblasti zatím nepodporují úplnou sadu dostupných **den** možnosti.

   ![Okno nastavení údržby](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Vyberte váš upřednostňovaný primární a sekundární správu a údržbu pomocí rozevírací seznamy:
   - **Den**: Upřednostňovaný den provádění údržby během vybraného časového období.
   - **Počáteční čas**: Upřednostňované počáteční čas pro časové období údržby.
   - **Časový interval**: Upřednostňované doba trvání časového intervalu.

   **Souhrn plánu** oblast v dolní části okna je aktualizována na základě hodnot, které jste vybrali. 
  
6. Vyberte **Uložit**. Zobrazí se zpráva s potvrzením, že nový plán je nyní aktivní. 

   Pokud ukládáte plánu v oblasti, která nepodporuje plánování údržby, zobrazí se následující zpráva. Nastavení se uloží a aktivuje, jakmile tato funkce je k dispozici ve vybrané oblasti.    

   ![Zpráva o dostupnosti oblast](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Další postup
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.


