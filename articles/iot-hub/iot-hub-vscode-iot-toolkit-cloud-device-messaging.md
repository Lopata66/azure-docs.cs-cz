---
title: Správa zařízení Azure IoT Hub cloudové zasílání zpráv s Azure IoT Tools pro Visual Studio Code | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure IoT Tools for Visual Studio Code k monitorování zařízení, aby zprávy typu cloud a cloud posílat zprávy zařízení ve službě Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: f8799fcb06235474bb33cdaf0406300065208024
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543824"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Nástroje pro Azure IoT pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a centrem IoT

![Diagram začátku do konce](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) je užitečná rozšíření Visual Studio Code, který usnadňuje služby IoT Hub správu a vývoj aplikací IoT. Tento článek se zaměřuje na tom, jak používat Azure IoT Tools for Visual Studio Code k odesílání a příjem zpráv mezi zařízením a centrem IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co se dozvíte

Zjistíte, jak pomocí Azure IoT Tools for Visual Studio Code pro monitorování zpráv typu zařízení cloud a odesílat zprávy typu cloud zařízení. Zprávy typu zařízení cloud může být data ze senzorů, který shromažďuje vaše zařízení a pak odešle do služby IoT hub. Zprávy typu cloud zařízení může být příkazy, které služby IoT hub odesílá do vašeho zařízení blikání kontrolku LED, která je připojená k zařízení.

## <a name="what-you-will-do"></a>Co budete dělat

* Pomocí nástrojů Azure IoT pro Visual Studio Code pro monitorování zpráv typu zařízení cloud.

* Použití Azure IoT Tools for Visual Studio Code pro odesílání zpráv typu cloud zařízení.

## <a name="what-you-need"></a>Co potřebujete

* Aktivní předplatné Azure.

* Azure IoT hub v rámci vašeho předplatného.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Nástroje Azure IoT pro VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo [otevření tohoto odkazu ve Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

1. V **Explorer** VS Code, rozbalte **zařízení Azure IoT Hub** části v levém dolním rohu.

2. Klikněte na tlačítko **vyberte centrum IoT** v kontextové nabídce.

3. Automaticky otevírané okno se zobrazí v pravém dolním rohu umožňuje při prvním přihlášení k Azure.

4. Po přihlášení se zobrazí seznam vašich předplatných Azure a potom vyberte předplatné Azure a IoT Hub.

5. Zobrazí se seznam zařízení v **zařízení Azure IoT Hub** kartu za pár sekund.

   > [!Note]
   > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). Zadejte připojovací řetězec služby IoT hub, který připojí zařízení IoT k v místním okně.

## <a name="monitor-device-to-cloud-messages"></a>Monitorování zpráv typu zařízení cloud

Pokud chcete monitorovat zprávy odeslané ze zařízení do služby IoT hub, postupujte takto:

1. Klikněte pravým tlačítkem na zařízení a vyberte **spustit monitorování integrovaných událostí koncový bod**.

2. Sledované zprávy zobrazí ve **výstup** > **Azure IoT Hub Toolkit** zobrazení.

3. Pokud chcete zastavit monitorování, klikněte pravým tlačítkem **výstup** zobrazit a vybrat **zastavit monitorování vestavěné koncový bod událostí**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Odeslat zprávu na vaše zařízení ze služby IoT hub, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **C2D odeslat zprávu do zařízení**.

2. Zadejte zprávu vstupního pole.

3. Výsledky se zobrazí v **výstup** > **Azure IoT Hub Toolkit** zobrazení.

## <a name="next-steps"></a>Další postup

Jste zjistili, jak pro monitorování zpráv typu zařízení cloud a odesílání zpráv typu cloud zařízení mezi zařízení IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]