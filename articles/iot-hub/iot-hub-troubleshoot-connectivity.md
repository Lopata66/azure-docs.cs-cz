---
title: Monitorování a řešení potíží odpojení pomocí Azure IoT Hub
description: Naučte se monitorovat a řešit běžné chyby s připojením zařízení pro Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b22573fc98d4647c2b05763d9b67c37f6f023f98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322716"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorování, diagnostika a řešení potíží odpojení pomocí Azure IoT Hub

Problémy s připojením pro zařízení IoT můžou být obtížné řešit, protože existuje mnoho možných bodů selhání. Služba Application Logic, fyzické sítě, protokoly, hardware, IoT Hub a další cloudové služby mohou způsobovat problémy. Možnost detekovat a identifikovat zdroj problému je kritická. Řešení IoT ve velkém měřítku ale může mít tisíce zařízení, takže není praktické kontrolovat jednotlivá zařízení ručně. Aby vám pomohl detekovat, diagnostikovat a řešit tyto problémy ve velkém měřítku, využijte možnosti monitorování IoT Hub poskytuje prostřednictvím Azure Monitor. Tyto možnosti jsou omezené na to, co IoT Hub může sledovat, proto doporučujeme, abyste pro zařízení a další služby Azure sledovali osvědčené postupy monitorování.

## <a name="get-alerts-and-error-logs"></a>Získat výstrahy a protokoly chyb

Pomocí Azure Monitor můžete získat výstrahy a zapsat protokoly, když se zařízení odpojí.

### <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Pokud chcete protokolovat události a chyby připojení zařízení, zapněte diagnostiku pro IoT Hub. Tyto protokoly doporučujeme co nejdříve zapnout, protože pokud nejsou povolené diagnostické protokoly a dojde k odpojení zařízení, nebudete mít k dispozici žádné informace k řešení problému se službou.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT Hub.

3. Vyberte **nastavení diagnostiky**.

4. Vyberte **zapnout diagnostiku**.

5. Povolte shromažďování protokolů **připojení** .

6. Pro snazší analýzu zapněte **odeslání do Log Analytics** ([Viz ceny](https://azure.microsoft.com/pricing/details/log-analytics/)). Podívejte se na příklad v části [řešení chyb připojení](#resolve-connectivity-errors).

   ![Doporučené nastavení](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Další informace najdete v tématu [monitorování stavu Azure IoT Hub a rychlé diagnostikování problémů](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Nastavení upozornění pro odpojení zařízení ve velkém měřítku

Pokud chcete dostávat upozornění, když se zařízení odpojí, nakonfigurujte upozornění na metrikě **připojená zařízení (Preview)** .

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT Hub.

3. Vyberte **výstrahy**.

4. Vyberte **Nové pravidlo upozornění**.

5. Vyberte **Přidat podmínku**a pak vyberte připojená zařízení (Preview).

6. Nastavte prahovou hodnotu a výstrahy pomocí následujících výzev.

Další informace najdete v tématu [co jsou výstrahy v Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Zjišťování odpojení jednotlivých zařízení

Chcete-li detekovat odpojení *podle zařízení* , například pokud potřebujete znát objekt pro vytváření, který je právě přepnut do režimu offline, [nakonfigurujte události odpojení zařízení pomocí Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Řešení chyb připojení

Když zapnete diagnostické protokoly a výstrahy pro připojená zařízení, zobrazí se upozornění, když dojde k chybám. Tato část popisuje, jak vyhledat běžné problémy, když obdržíte výstrahu. Následující postup předpokládá, že jste nastavili protokoly Azure Monitor pro diagnostické protokoly.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejděte do služby IoT Hub.

1. Vyberte **Protokoly**.

1. Pokud chcete izolovat protokoly chyb připojení pro IoT Hub, zadejte následující dotaz a pak vyberte **Spustit**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Pokud jsou k dispozici výsledky, vyhledejte `OperationName` , `ResultType` (kód chyby) a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.

   ![Příklad protokolu chyb](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Projděte si průvodce řešením problémů s nejčastějšími chybami:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Vyzkoušel jsem postup, ale nefungoval

Pokud vám předchozí kroky neudělaly, zkuste:

* Pokud máte přístup k problematickým zařízením buď fyzicky, nebo vzdáleně (jako je SSH), postupujte podle pokynů v [Průvodci odstraňováním potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a pokračujte v řešení potíží.

* Ověřte, jestli jsou zařízení **povolená** v Azure Portal > vaše zařízení IoT ve službě iot hub >.

* Pokud zařízení používá protokol MQTT, ověřte, že je otevřený port 8883. Další informace najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Získejte pomoc od [Microsoft Q&stránku s otázkou pro azure IoT Hub](https://docs.microsoft.com/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)nebo [podporu Azure](https://azure.microsoft.com/support/options/).

Pokud vám tato příručka nepomohly vylepšit dokumentaci pro všechny uživatele, ponechte v části zpětná vazba komentář.

## <a name="next-steps"></a>Další kroky

* Další informace o řešení přechodných problémů naleznete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

* Další informace o sadě Azure IoT SDK a správě opakování najdete v tématu [Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK pro zařízení azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
