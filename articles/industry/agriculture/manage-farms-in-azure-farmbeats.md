---
title: Spravovat farmy
description: Popisuje, jak spravovat farmy.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271717"
---
# <a name="manage-farms"></a>Správa farem

Své farmy můžete spravovat ve službě Azure FarmBeats. Tento článek poskytuje informace o tom, jak vytvářet farmy, instalovat zařízení, senzory a DRONY zachraňují životy, které vám pomůžou spravovat vaše farmy.

## <a name="create-farms"></a>Vytváření farem

Použijte k tomu následující postup:

1. Přihlaste se k akcelerátoru farmy a zobrazí se stránka **farmy** .
    Stránka **farmy** zobrazuje seznam farem v případě, že již byly vytvořeny v předplatném.

    Tady je ukázka obrázku:

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Vyberte **vytvořit farmu** a zadejte **název**, **plodiny** a **adresu**.
3. V části **definovat hranici farmy**(povinné pole) vyberte buď možnost **označit na mapě** , nebo **vložte kód v oblasti injson**.

Existují dva způsoby, jak definovat hranici farmy:

1. **Označit na mapě**: pomocí nástroje mapového ovládacího prvku nakreslete a označte hranici farmy. Chcete-li označit hranice ![, farma](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) projektu Beats a označte přesné hranice.

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Vložení geografického kódu JSON**: geografická JSON je formát pro kódování geografických datových struktur pomocí JavaScript Object Notation (JSON). Tato možnost zobrazí textové pole, kde lze zadat řetězec typu "text", který označuje hranice farmy. Z GeoJSON.io můžete také vytvořit kód pro injson.
Informace můžete zadat pomocí popisů.

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Vyberte **Odeslat** a vytvořte farmu. Vytvoří se nová farma, která se zobrazí na stránce **farmy** .

## <a name="view-farm"></a>Zobrazit farmu

Na stránce seznam farmy se zobrazí seznam vytvořených farem. Vyberte farmu, pro kterou chcete zobrazit seznam:

 - **Počet zařízení** – zobrazí počet a stav zařízení nasazených v rámci farmy.
 - **Map** – mapování farmy pomocí zařízení nasazených ve farmě
 - **Telemetrie** – zobrazí telemetrii ze senzorů nasazených ve farmě.
 - **Nejnovější mapy přesnosti** – zobrazí nejnovější mapu satelitních indexů (EVI, NDWI), vlhkosti v půdě a mapu umístění senzorů.

## <a name="edit-farm"></a>Upravit farmu

Stránka **farmy** zobrazuje seznam vytvořených farem.

1.  Vyberte farmu, pro kterou chcete farmu zobrazit a upravit.
2.  Vyberte **Upravit farmu** a upravte informace o farmě. V okně **Podrobnosti o farmě** můžete upravit pole **název**, **plodiny**, **adresa**a definovat **hranice farmy** .

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Kliknutím na tlačítko **Odeslat** uložte upravované podrobnosti.

## <a name="delete-farm"></a>Odstranit farmu

Na stránce **farmy** se zobrazí seznam vytvořených Farm. Chcete-li odstranit farmu, použijte následující postup:

1.  Vyberte farmu ze seznamu pro odstranění podrobností o farmě.
2.  Vyberte **Odstranit farmu** , aby se farma odstranila.

    ![Beats farmy projektu](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Když odstraníte farmu, zařízení a mapy přidružené k farmě se neodstraňují. Žádné informace o farmě spojené se zařízením a mapami nebudou relevantní. Můžete dál zobrazovat zařízení, telemetrie a mapy ze služby FarmBeats.


## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili farmu, zjistěte, jak [získat data ze senzorů](get-sensor-data-from-sensor-partner.md) do farmy.
