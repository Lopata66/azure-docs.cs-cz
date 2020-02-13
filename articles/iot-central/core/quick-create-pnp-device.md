---
title: Rychlý Start – přidání simulovaného zařízení do Azure IoT Central
description: V tomto rychlém startu se dozvíte, jak vytvořit šablonu zařízení a přidat do aplikace IoT Central simulované zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169619"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Rychlý Start: Přidání simulovaného zařízení do aplikace IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

Šablona zařízení definuje možnosti zařízení, které se připojuje k vaší IoT Central aplikaci. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje. V rámci šablony zařízení může tvůrce nebo operátor přidat do aplikace skutečná i simulovaná zařízení. Simulovaná zařízení jsou užitečná pro testování chování aplikace IoT Central před propojením reálných zařízení.

V tomto rychlém startu přidáte šablonu zařízení pro desku *MXChip IoT DevKit* (DevKit) a vytvoříte simulované zařízení. K dokončení tohoto rychlého startu nepotřebujete skutečné zařízení, budete pracovat s simulací zařízení. Zařízení DevKit:

* Odesílá telemetrii jako teplotu.
* Oznamuje vlastnosti specifické pro zařízení, jako je úroveň jasu.
* Reaguje na příkazy, jako je zapnutí a vypnutí.
* Sestavy obecných vlastností zařízení, jako je například verze firmwaru a sériové číslo.

## <a name="prerequisites"></a>Předpoklady

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete v aplikaci IoT Central vytvořit a upravit šablony zařízení. Po publikování šablony zařízení můžete vygenerovat simulované zařízení nebo propojit skutečná zařízení se šablonou zařízení. Simulovaná zařízení umožňují otestovat chování aplikace před připojením reálného zařízení.

Chcete-li přidat novou šablonu zařízení do aplikace, vyberte kartu **šablony zařízení** v levém podokně.

![Stránka šablony zařízení](./media/quick-create-pnp-device/devicedefinitions.png)

Šablona zařízení zahrnuje model schopností zařízení, který definuje telemetrii, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje.

### <a name="add-a-device-capability-model"></a>Přidání modelu schopností zařízení

K dispozici je několik možností, jak přidat model schopností zařízení do aplikace IoT Central. Můžete vytvořit nový model od začátku, importovat model ze souboru nebo vybrat zařízení z katalogu zařízení. IoT Central taky podporuje přístup k *prvnímu zařízení* , kde automaticky importuje model z úložiště, když se zařízení poprvé připojí. V tomto rychlém startu zvolíte zařízení z katalogu zařízení pro Import modelu schopností zařízení.

Následující kroky ukazují, jak pomocí katalogu zařízení importovat model schopností pro zařízení **MXChip IoT DevKit** . Tato zařízení odesílají telemetrii, například teplotu, do vaší aplikace:

1. Chcete-li přidat novou šablonu zařízení, vyberte **+** na stránce **šablony zařízení** .

1. Na stránce **Vybrat typ šablony** se posuňte dolů, dokud nenajdete dlaždici **MXChip IoT DevKit** .

1. Vyberte dlaždici **MXChip IoT DevKit** a pak vyberte **Další: přizpůsobit**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

1. Po několika sekundách uvidíte novou šablonu zařízení:

    ![Šablona zařízení IoT DevKit pro MXChip](./media/quick-create-pnp-device/devkit-template.png)

    Model schopností MXChip IoT DevKit zahrnuje rozhraní, jako jsou **mxchip_sensor**, **Mxchip_settings**a **informace o zařízení**. Rozhraní definují možnosti zařízení MXChip IoT DevKit. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti, které zařízení hlásí, a příkazy, na které zařízení reaguje.

### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. K přidání dvou vlastností cloudu do šablony zařízení použijte informace v následující tabulce:

    | Zobrazované jméno      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádná          | Datum   |
    | Název zákazníka     | Žádná          | String |

1. Kliknutím na **Uložit** uložte změny:

    ![Vlastnosti cloudu](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely, které znázorňují zařízení, včetně telemetrie, kterou odesílají.

### <a name="default-views"></a>Výchozí zobrazení

Výchozí zobrazení představují rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Pro šablonu zařízení můžete mít vygenerované až tři výchozí zobrazení:

* Zobrazení **příkazy** umožňuje operátorovi odeslat příkazy do vašeho zařízení.
* Zobrazení **přehledu** používá grafy a metriky k zobrazení telemetrie zařízení.
* V zobrazení **informace** se zobrazí vlastnosti zařízení.

V šabloně zařízení vyberte uzel **zobrazení** . V případě, že jste přidali šablonu, vidíte, že IoT Central vygenerovala **Přehled** a **informace o** zobrazení.

Přidání nového formuláře pro **správu zařízení** , který může operátor použít ke správě zařízení:

1. Vyberte uzel **zobrazení** a vyberte dlaždici **data zařízení a cloudu** a přidejte nové zobrazení.

1. Změňte název formuláře, aby bylo možné **spravovat zařízení**.

1. Vyberte **název zákazníka** a poslední vlastnosti cloudového **data služby** a vlastnost **rychlost ventilátoru** . Pak vyberte **přidat oddíl**:

    ![Vytvořit nový formulář](media/quick-create-pnp-device/new-form.png)

1. Kliknutím na **Uložit** uložte nový formulář.

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moct vytvořit simulované zařízení nebo připojit reálné zařízení, budete muset publikovat šablonu zařízení. I když IoT Central šablonu publikovali při jejím prvním vytvoření, je nutné publikovat aktualizovanou verzi.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** můžete přejít na šablonu zařízení.

1. Vyberte **publikovat**:

    ![Publikovaný model](media/quick-create-pnp-device/publishedmodel.png)

1. V dialogovém okně **Publikovat tuto šablonu zařízení do dialogu aplikace** vyberte **publikovat**. 

Po publikování se šablona zařízení zobrazí na stránce **zařízení** . V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. Můžete ale dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení změn vyberte **publikovat** , aby se tyto změny převedly do vašeho operátoru.

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

K přidání simulovaného zařízení do aplikace použijete šablonu zařízení **MXChip IoT DevKit** , kterou jste vytvořili.

1. Pokud chcete přidat nové zařízení jako operátor v levém podokně vyberte **zařízení** . Karta **zařízení** zobrazuje **všechna zařízení** a šablonu zařízení **MXChip IoT DevKit** . Vyberte **MXChip IoT DevKit**.

1. Pokud chcete přidat simulované zařízení DevKit, vyberte **+** . Použijte navržené **ID zařízení** nebo zadejte svoje **ID zařízení**s malými písmeny. Můžete také zadat název nového zařízení. Ujistěte se, že je **zapnuté** **simulované** přepínání, a pak vyberte **vytvořit**.

    ![Simulované zařízení](./media/quick-create-pnp-device/simulated-device.png)

Nyní můžete pracovat se zobrazeními vytvořenými tvůrcem pro šablonu zařízení pomocí simulovaných dat:

1. Na stránce **zařízení** vyberte simulované zařízení.

1. Zobrazení **přehledu** ukazuje vykreslení simulované telemetrie:

    ![Přehledové zobrazení](./media/quick-create-pnp-device/simulated-telemetry.png)

1. V zobrazení **informace** se zobrazují hodnoty vlastností, včetně vlastností cloudu, které jste přidali do zobrazení.

1. Zobrazení **příkazy** umožňuje spouštět příkazy, jako je například **blikání** na zařízení.

1. Zobrazení **Správa zařízení** je formulář, který jste vytvořili pro obsluhu pro správu zařízení.

## <a name="use-a-simulated-device-to-improve-views"></a>Použití simulovaného zařízení ke zlepšení zobrazení

Po vytvoření nového simulovaného zařízení může tvůrce pomocí tohoto zařízení pokračovat ve zlepšování a sestavování zobrazení pro šablonu zařízení.

1. V levém podokně zvolte **šablony zařízení** a vyberte šablonu **MXChip IoT DevKit** .

1. Vyberte libovolné zobrazení, které chcete upravit, nebo vytvořte nové zobrazení. Vyberte **Konfigurovat zařízení verze Preview**a pak **Vyberte ze spuštěného zařízení**. Tady můžete zvolit, aby nedošlo k zobrazení náhledu, reálnému zařízení nakonfigurovanému pro testování nebo existujícímu zařízení, které jste přidali do IoT Central.

1. V seznamu vyberte simulované zařízení. Pak vyberte **použít**. Teď můžete vidět stejné simulované zařízení v zobrazeních šablon zařízení. Toto zobrazení je užitečné pro grafy a další vizualizace.

    ![Konfigurace zařízení Preview](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit šablonu zařízení **MXChip IoT DevKit** a přidat do své aplikace simulované zařízení.

Další informace o monitorování zařízení připojených k vaší aplikaci získáte, když budete pokračovat v rychlém startu:

> [!div class="nextstepaction"]
> [Konfigurace pravidel a akcí](./quick-configure-rules.md)
