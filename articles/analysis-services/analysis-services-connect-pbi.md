---
title: Připojení ke službě Azure Analysis Services v Power BI | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k serveru Azure Analysis Services pomocí Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cb93c29ae4cb8d365cd128c52ee726c10c94bc19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023570"
---
# <a name="connect-with-power-bi"></a>Propojení s Power BI

Po vytvoření serveru v Azure a nasadili do ní tabulkový model, uživatelé ve vaší organizaci jsou připraveny k připojení a začít zkoumat data. 

> [!TIP]
> Nezapomeňte použít nejnovější verzi [Power BI Desktopu](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktopu

1. V Power BI Desktopu klikněte na **Získat data** > **Azure** > **Databáze Azure Analysis Services**.

2. V **Server**, zadejte název serveru. Nezapomeňte uvést úplnou adresu URL; například asazure://westcentralus.asazure.windows.net/advworks.

3. V **databáze**, pokud znáte název databázi tabulkového modelu nebo perspektivy, které se chcete připojit, sem. V opačném případě můžete toto pole nechat prázdné a vyberte databázi nebo perspektivy později.

4. Výběr možnosti připojení a potom stiskněte klávesu **připojit**. 

    Obě **připojit živě** a **Import** možnosti jsou podporovány. Doporučujeme však, že používáte živé připojení, protože režim Import mají určitá omezení; zejména server může mít dopad na výkon při importu. Také, pokud je model se aktualizují ve službě Power BI **povolit přístup z Power BI** nastavení platí pouze při výběru **připojit živě**.

5. Pokud se zobrazí výzva, zadejte své přihlašovací údaje. 

6. V **Navigátor**, rozbalte server a pak vyberte model nebo perspektivu, kterou chcete připojit a potom klikněte na **připojit**. Klikněte na modelu nebo perspektivě zobrazíte všechny objekty pro dané zobrazení.

    Model se otevře s prázdnou sestavou v zobrazení sestav v Power BI Desktopu. V seznamu polí zobrazí všechny objekty modelu, že není skrytý. Stav připojení se zobrazuje v pravém dolním rohu.

## <a name="connect-in-power-bi-service"></a>Připojit v Power BI (služba)

1. Vytvořte soubor Power BI Desktopu, která má aktivní připojení k modelu na vašem serveru.
2. V [Power BI](https://powerbi.microsoft.com), klikněte na tlačítko **získat Data** > **soubory**a poté vyhledejte a vyberte soubor .pbix.

## <a name="see-also"></a>Další informace najdete v tématech
[Připojení ke službě Azure Analysis Services](analysis-services-connect.md)   
[Klientské knihovny](analysis-services-data-providers.md)

