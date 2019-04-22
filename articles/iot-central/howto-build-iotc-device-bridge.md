---
title: Vytváření mostu zařízení Azure IoT Central | Dokumentace Microsoftu
description: Vytvoření IoT Central most zařízení pro připojení jiných cloudech IoT (Sigfox, částice, The věci sítě atd.) do vaší aplikace IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 246c9ad8ab3083c1b847c1c25230a7193a8192e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263566"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Vytvoření IoT Central most zařízení pro připojení jiných cloudech IoT k IoT Central

*Toto téma se vztahuje na správce.*

Most zařízení IoT Central je řešení typu open source, který se připojuje vaše Sigfox, částice, sítě věcí a další cloudy do vaší aplikace IoT Central. Ať už používáte sledování zařízení připojená k vaší Sigfox s nízkou-Power-Wide Area Network prostředků, nebo pomocí air kvality monitorování zařízení na Cloud částice zařízení nebo pomocí monitorování zařízení na TTN vlhkosti bomby, můžete přímo využijte výkon IoT Centrální pomocí bridge zařízení IoT Central. Most zařízení připojení jiných cloudech IoT pomocí IoT Central předávání dat, která vaše zařízení odesílají do jiných cloudech prostřednictvím aplikace IoT Central. V aplikaci pro IoT Central můžete pravidla sestavení a spouštět analýzy na těchto datech, vytváření pracovních postupů v Microsoft Flow a Azure Logic apps, exportovat data a spoustu dalších věcí. Získejte [most zařízení IoT Central](https://aka.ms/iotcentralgithubdevicebridge) z Githubu

## <a name="what-is-it-and-how-does-it-work"></a>Co je a jak to funguje?
Most zařízení IoT Central je řešení typu open source na Githubu. Je připravené s tlačítkem "Nasazení do Azure", která nasadí vlastní šablony Azure Resource Manageru s několika prostředků Azure do vašeho předplatného Azure. Prostředky patří:
-   Aplikace Azure Function app
-   Účet služby Azure Storage
-   Plán Consumption
-   Azure Key Vault

Aplikace function app je důležitých most zařízení. Začne přijímat požadavky HTTP POST z jiné platformy IoT nebo všechny vlastní platformy prostřednictvím integrace jednoduché webhooku. Uvádíme příklady, které ukazují, jak se připojit k Sigfox, částice a TTN cloudy. Můžete jednoduše rozšířit toto řešení pro připojení k vaší vlastní cloudu IoT, pokud vaši platformu zasílat požadavky HTTP POST na aplikaci function App.
Aplikace Function app transformuje data do formátu přijal IoT Central a předá jej podél prostřednictvím rozhraní API a distribučních bodů.

![Snímek obrazovky funkcí Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Pokud vaše aplikace IoT Central rozpoznává ID zařízení v přesměrovaná zpráva nového měření se zobrazí pro toto zařízení. Pokud ID zařízení nikdy ukázala aplikací pro IoT Central, aplikace function app se pokusí zaregistrovat nová zařízení s tímto Identifikátorem zařízení, se zobrazí jako "nepřidružené zařízení" v aplikaci pro IoT Central. 

## <a name="how-do-i-set-it-up"></a>Jak můžu nastavit ho?
Podle pokynů uvedených v podrobně v souboru README v úložišti na Githubu. 

## <a name="pricing"></a>Ceny
Prostředky Azure se hostovat ve vašem předplatném Azure. Další informace o cenách v [souboru README](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Další postup
Teď, když jste zjistili, jak vytvářet most zařízení IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices.md)
