---
title: Analýza hlavní příčiny pro upozornění – Azure | Microsoft Docs
description: V tomto kurzu se naučíte provádět analýzu hlavní příčiny pro upozornění pomocí služby Azure Time Series Insights.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565464"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Kurz: Analýza hlavní příčiny pro upozornění

V tomto kurzu se dozvíte, jak používat akcelerátor řešení pro vzdálené monitorování k diagnostice hlavní příčiny upozornění. Uvidíte, že se upozornění aktivovalo na řídicím panelu řešení pro vzdálené monitorování, a potom použijete průzkumníka služby Azure Time Series Insights a prozkoumáte hlavní příčinu.

Tento kurz používá dvě simulovaná zařízení nákladního vozu, která odesílají polohu, zeměpisnou výšku, rychlost a telemetrická data o teplotě nákladu. Tyto nákladní vozy spravuje organizace Contoso a jsou připojené k akcelerátoru řešení pro vzdálené monitorování. Jako operátor organizace Contoso musíte pochopit, proč jeden z vašich nákladních vozů (delivery-truck-02) zaprotokoloval upozornění na nízkou teplotu.

V tomto kurzu jste:

>[!div class="checklist"]
> * Filtrování zařízení na řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Prozkoumat data v průzkumníku služby Time Series Insights
> * Provést analýzu hlavní příčiny
> * Vytvořit nové pravidlo na základě vašich poznatků

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Výběr zařízení k zobrazení

K výběru připojených zařízení, která se zobrazí na stránce **Řídicí panel**, použijte filtry. Pokud chcete zobrazit pouze zařízení **Nákladní vůz**, v rozevíracím seznamu filtru zvolte předdefinovaný filtr **Nákladní vozy**:

[![Filtr pro nákladní vozidla na palubní desce](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Při použití filtru jsou na mapě a v panelu telemetrie na **řídicím panelu**zobrazena pouze zařízení, která odpovídají podmínkám filtru. Uvidíte, že k akcelerátoru řešení jsou připojené dva nákladní vozy (včetně nákladního vozu **truck-02**).

## <a name="view-real-time-telemetry"></a>Zobrazení telemetrie v reálném čase

Akcelerátor řešení vykresluje telemetrická data v reálném čase do grafu na stránce **Řídicí panel**. Ve výchozím nastavení graf zobrazuje telemetrická data zeměpisné výšky, která jsou v průběhu času různá:

[![Telemetrický graf nadmořské výšky nákladních vozidel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Pokud chcete zobrazit telemetrická data o teplotě nákladních vozů, klikněte na **Temperature** (Teplota) na **panelu telemetrických dat**. Uvidíte, jak se teplota za posledních 15 minut u obou nákladních vozů měnila. V podokně upozornění můžete také vidět, že se pro vůz delivery-truck-02 aktivovalo upozornění na nízkou teplotu.

[![Řídicí panel RM s upozorněním nízké teploty](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Zkoumání dat

Pokud chcete identifikovat příčinu varování týkajícího se nízké teploty, otevřete telemetrická data nákladního vozu v průzkumníku služby Time Series Insights. Na řídicím panelu klikněte na jakýkoli odkaz pro **prozkoumání ve službě Time Series Insights**:

[![Řídicí panel RM se zvýrazněnými odkazy TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Jakmile se průzkumník spustí, uvidíte seznam všech vašich zařízení:

[![Počáteční zobrazení průzkumníka TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Zařízení můžete filtrovat zadáním **dodávkového vozíku** do pole filtru a v levém panelu vyberte **teplotu** jako **měřítko:**

[![Teplota nákladního vozidla TSI Explorer](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Stejný pohled, který jste viděli na řídicím panelu vzdáleného monitorování. Nyní můžete také přiblížit blíže k časovému rámci, ve které byla výstraha spuštěna v rámci:

[![TSI Explorer zoom](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Můžete také přidat další streamy telemetrických dat přicházející z nákladních vozů. Klikněte na tlačítko **Přidat** v levém horním rohu. Zobrazí se nové podokno:

[![Průzkumník TSI s novým podoknem](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Vyberte **nadmořskou výšku** jako **Measure** a **iothub-connection-device-id** jako hodnotu **Rozdělit** podle, chcete-li do zobrazení přidat telemetrii nadmořské výšky:

[![TSI Explorer s teplotou a nadmořskou výškou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnostika upozornění

Když se podíváte na streamy v aktuálním zobrazení, uvidíte, že výškové profily obou nákladních vozidel se liší. Také uvidíte to, že k poklesu teploty ve voze **delivery-truck-02** dojde, když vůz dosáhne vysoké zeměpisné výšky. Tímto zjištěním jste překvapeni, protože vozy měly naplánovanou stejnou trasu.

Abyste si potvrdili domněnku, že každý z vozů jel jinou cestou, přidejte na boční panel další podokno pomocí tlačítka **Add** (Přidat). V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Pokud chcete do zobrazení přidat telemetrická data zeměpisné délky, vyberte **zeměpisnou délku** jako **míru** a **iothub-connection-device-id** jako hodnotu, **podle které se má provádět rozdělení**. Když se podíváte na rozdíly mezi streamy **zeměpisné délky**, uvidíte, že vozy jely různými cestami:

[![TSI Explorer s teplotou, nadmořskou výškou a zeměpisnou výškou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Zatímco trasy nákladních vozidel jsou obvykle předem optimalizovány, uvědomíte si, že dopravní vzorce, počasí a další nepředvídatelné události mohou způsobit zpoždění a nechat rozhodnutí o trase na poslední chvíli řidičům nákladních vozidel na základě jejich nejlepšího úsudku. Vzhledem k tomu, že teplota vašich aktiv uvnitř vozidla je kritická, měli byste vytvořit další pravidlo v řešení vzdáleného monitorování. Toto pravidlo je zajistit, že obdržíte varování, pokud průměrná nadmořská výška v intervalu 1 minuty přesahuje 350 stop:

[![Nastavit pravidlo nadmořské výšky na kartě Pravidla vzdálených monitorování](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Pokud se chcete dozvědět, jak vytvářet a upravovat pravidla, podívejte se na předchozí kurz týkající se [zjišťování problémů se zařízením](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak používat průzkumníka služby Time Series Insights s akcelerátorem řešení pro vzdálené monitorování k diagnostice hlavní příčiny upozornění. Informace o tom, jak pomocí akcelerátoru řešení identifikovat a opravit problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Použití upozornění zařízení k identifikaci a opravě problémů se zařízeními připojenými k řešení pro monitorování](iot-accelerators-remote-monitoring-maintain.md)
