---
title: Vytvořit zobrazení, která analyzuje data protokolů ve službě Azure Monitor | Dokumentace Microsoftu
description: Pomocí návrháře zobrazení ve službě Azure Monitor můžete vytvořit vlastní zobrazení, které se zobrazují na webu Azure Portal a obsahovat nejrůznější vizualizace dat v pracovním prostoru Log Analytics. Tento článek obsahuje základní informace o návrháři zobrazení a uvede postupy pro vytváření a úpravu vlastní zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: f07fc2f03ad72e7ee0fd408782b8fe845c88e780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61342011"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Vytváření vlastních zobrazení pomocí návrháře zobrazení ve službě Azure Monitor
Pomocí návrháře zobrazení ve službě Azure Monitor můžete vytvořit řadu vlastních zobrazení na webu Azure Portal, který může pomoci vám vizualizovat data ve vašem pracovním prostoru Log Analytics. Tento článek obsahuje přehled nástroje Návrhář zobrazení a postupy pro vytváření a úpravu vlastní zobrazení.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Další informace o návrháři zobrazení najdete v tématu:

* [Dlaždice odkaz](view-designer-tiles.md): Poskytuje referenční příručka k nastavení všech dlaždic dostupných do vlastních zobrazení.
* [Referenční informace k části vizualizace](view-designer-parts.md): Poskytuje referenční příručka k nastavení části vizualizace, které jsou k dispozici do vlastních zobrazení.


## <a name="concepts"></a>Koncepty
Zobrazení se zobrazí ve službě Azure Monitor **přehled** stránky na webu Azure Portal. Otevřete tuto stránku z **Azure Monitor** nabídky kliknutím **Další** pod **Insights** oddílu. Dlaždice v každém vlastní zobrazení se zobrazují podle abecedy a dlaždic pro řešení monitorování jsou nainstalovány stejného pracovního prostoru.

![Stránka s přehledem](media/view-designer/overview-page.png)

Zobrazení, které vytvoříte pomocí návrháře zobrazení obsahovat prvky, které jsou popsány v následující tabulce:

| Část | Popis |
|:--- |:--- |
| Dlaždice | Jsou zobrazeny v Azure Monitor **přehled** stránky. Každé dlaždici se zobrazí vizuální přehled, který představuje vlastní zobrazení. Každý typ dlaždice obsahuje vizualizaci různých záznamů. Můžete vybrat příslušnou dlaždici a vlastní zobrazení. |
| Vlastní zobrazení | Zobrazí, když vyberete dlaždici. Každé zobrazení obsahuje jednu nebo více částí vizualizace. |
| Části vizualizace | K dispozici vizualizace dat v pracovním prostoru Log Analytics na základě jedné nebo více [protokolu dotazy](../log-query/log-query-overview.md). Většina části patří hlavičky, která poskytuje vizualizaci vysoké úrovně, a seznam, který zobrazí nejlepší výsledky. Každý typ části poskytuje různé vizualizace záznamů v pracovním prostoru Log Analytics. Vybrat elementy v části o provedení dotazu protokolu, která poskytuje podrobné záznamy. |

## <a name="required-permissions"></a>Požadovaná oprávnění
Budete potřebovat alespoň [oprávnění na úrovni Přispěvatel](manage-access.md#manage-accounts-and-users) v pracovním prostoru Log Analytics, vytvoření nebo úprava zobrazení. Pokud toto oprávnění nemáte, nebude možnost Návrhář zobrazení zobrazí v nabídce.


## <a name="work-with-an-existing-view"></a>Práce s existující zobrazení
Zobrazení, které byly vytvořeny pomocí návrháře zobrazení zobrazí následující možnosti:

![Přehled nabídky](media/view-designer/overview-menu.png)

Tyto možnosti jsou popsány v následující tabulce:

| Možnost | Popis |
|:--|:--|
| Obnovení   | Aktualizuje zobrazení s nejnovější data. | 
| Protokoly      | Otevře [Log Analytics](../log-query/portals.md) k analýze dat pomocí dotazů na protokoly. |
| Upravit       | Otevře se zobrazení v Návrháři zobrazení k úpravě jeho obsah a konfiguraci.  |
| Klon      | Vytvoří nové zobrazení a otevře v zobrazení návrhu. Název nového zobrazení je stejný jako původní název, ale s *kopírování* připojí k němu. |
| Rozsah dat | Nastavte filtr rozsahu data a času pro data, která je zahrnutá v zobrazení. Tento rozsah je použit před všechny rozsahy kalendářních dat nastavit v dotazech v zobrazení.  |
| +          | Definujte vlastní filtr, který je definován pro zobrazení. |


## <a name="create-a-new-view"></a>Vytvoření nového zobrazení
Můžete vytvořit nové zobrazení v Návrháři zobrazení tak, že vyberete **Návrhář zobrazení** v nabídce pracovního prostoru Log Analytics.

![Dlaždici Návrhář zobrazení](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Práce s Návrhář zobrazení
Návrhář zobrazení použít k vytvoření nového zobrazení nebo upravovat stávající. 

Návrhář zobrazení má tři podokna: 
* **Návrh**: Obsahuje vlastní zobrazení, které jste vytvoření nebo úpravy. 
* **Ovládací prvky**: Obsahuje dlaždice a části, které přidáte **návrhu** podokně. 
* **Vlastnosti**: Zobrazí vlastnosti dlaždice nebo vybraných částí.

![Návrhář zobrazení](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Nakonfigurujte dlaždici zobrazení
Vlastní zobrazení může mít pouze jednu dlaždici. Chcete-li zobrazit aktuální dlaždice nebo vyberte některý z alternativních, vyberte **dlaždici** kartu **ovládací prvek** podokně. **Vlastnosti** podokně se zobrazí vlastnosti aktuální dlaždice. 

Můžete nakonfigurovat vlastnosti dlaždice podle informací v [dlaždici odkaz](view-designer-tiles.md) a potom klikněte na tlačítko **použít** a uložte změny.

### <a name="configure-the-visualization-parts"></a>Konfigurovat části vizualizace
Zobrazení může obsahovat libovolný počet části vizualizace. Chcete-li přidat součásti do zobrazení, vyberte **zobrazení** kartu a pak vyberte části vizualizace. **Vlastnosti** podokně se zobrazí vlastnosti vybrané části. 

Můžete nakonfigurovat vlastnosti zobrazení podle informací v [referenční informace k části vizualizace](view-designer-parts.md) a potom klikněte na tlačítko **použít** a uložte změny.

Zobrazení je k dispozici pouze jeden řádek z vizualizace částí. Existující části lze uspořádat přetažením do nového umístění.

Části vizualizace můžete odebrat ze zobrazení tak, že vyberete **X** v horní části přímo z část.


### <a name="menu-options"></a>Možnosti nabídky
Možnosti práce se zobrazeními v režimu úprav jsou popsány v následující tabulce.

![Upravit nabídku](media/view-designer/edit-menu.png)

| Možnost | Popis |
|:--|:--|
| Uložení        | Uloží změny a zavře zobrazení. |
| Zrušit      | Zruší změny a zavře zobrazení. |
| Odstranit zobrazení | Odstraní zobrazení. |
| Export      | Exportuje zobrazení tak, aby [šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) , který můžete importovat do jiného pracovního prostoru. Název souboru je název zobrazení a nemá *omsview* rozšíření. |
| Import      | Importuje *omsview* soubor, který jste vyexportovali z jiného pracovního prostoru. Tato akce přepíše konfiguraci stávající zobrazení. |
| Klon       | Vytvoří nové zobrazení a otevře v zobrazení návrhu. Název nového zobrazení je stejný jako původní název, ale s *kopírování* připojí k němu. |

## <a name="next-steps"></a>Další postup
* Přidat [dlaždice](view-designer-tiles.md) do vlastních zobrazení.
* Přidat [části vizualizace](view-designer-parts.md) do vlastních zobrazení.
