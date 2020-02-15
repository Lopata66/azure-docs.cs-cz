---
title: Zobrazit metriky využití Azure Maps API | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit metriky pro volání rozhraní API služby Microsoft Azure Maps v Azure Portal.
author: farah-alyasari
ms.author: v-faalya
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cc9834f4e1df1a68f78230ec587254c72aeabbcf
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208681"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Zobrazení metrik využití rozhraní API služby Azure Maps

V tomto článku se dozvíte, jak zobrazit metriky využití rozhraní API pro účet Azure Maps v [Azure Portal](https://portal.azure.com). Metriky se zobrazují ve vhodném formátu grafu po upravitelnou dobu trvání.

## <a name="view-metric-snapshot"></a>Zobrazit snímek metriky

Některé běžné metriky si můžete prohlédnout na stránce **Přehled** v účtu Maps. V současné době zobrazuje *Celkový počet požadavků*, *Celkový počet chyb*a *dostupnost* v rámci selektivního časového intervalu.

![Přehled Azure Maps metriky využití](media/how-to-view-api-usage/portal-overview.png)

Pokud potřebujete přizpůsobit tyto grafy pro konkrétní analýzu, přejděte k další části.

## <a name="view-detailed-metrics"></a>Zobrazit podrobné metriky

1. Přihlaste se k předplatnému Azure na [portálu](https://portal.azure.com).

2. Klikněte na položku nabídky **všechny prostředky** na levé straně a přejděte na svůj *účet Azure Maps*.

3. Po otevření účtu Maps klikněte na nabídku **metrik** na levé straně.

4. V podokně **metriky** vyberte jednu z následujících možností:

   1. **Dostupnost** – zobrazuje *průměr* dostupnosti rozhraní API za určité časové období.
   2. **Využití** – ukazuje, jak se zobrazuje *počet* použití pro váš účet.

      ![Podokno metriky využití Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. V dalším kroku můžete vybrat *časový rozsah* kliknutím na **posledních 24 hodin (automaticky)** . Ve výchozím nastavení je časový rozsah nastavený na 24 hodin. Po kliknutí se zobrazí všechny vybrané časové rozsahy. Můžete vybrat *časové rozlišení* a zvolit, že se má v jednom rozevíracím seznamu zobrazit čas jako *místní* nebo *GMT* . Klikněte na **Použít**.

    ![Časový rozsah Azure Mapsch metrik](media/how-to-view-api-usage/time-range.png)

6. Po přidání metriky můžete **Přidat filtr** z vlastností, které jsou pro danou metriku relevantní. Pak vyberte hodnotu vlastnosti, kterou chcete zobrazit v grafu.

    ![Filtr metrik Azure Maps využití](media/how-to-view-api-usage/filter.png)

7. V závislosti na zvolené vlastnosti metriky můžete také **použít rozdělení** metriky. Umožňuje rozdělit graf na více grafů, a to pro každou hodnotu této vlastnosti. Na následujícím obrázku odpovídá barva každého grafu hodnotě vlastnosti zobrazené v dolní části grafu.

    ![Rozdělení Azure Mapsch metrik využití](media/how-to-view-api-usage/splitting.png)

8. Můžete také sledovat více metrik ve stejném grafu pouhým kliknutím na tlačítko **Přidat metriku** nahoře.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API Azure Maps, pro která chcete sledovat využití:
> [!div class="nextstepaction"] 
> [Postupy Azure Maps Web SDK](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK postupy](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Dokumentace k Azure Maps REST API](https://docs.microsoft.com/rest/api/maps)
