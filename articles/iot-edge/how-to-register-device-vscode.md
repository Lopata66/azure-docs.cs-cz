---
title: Zaregistrujte si nové zařízení z aplikace Visual Studio Code – Azure IoT Edge | Dokumentace Microsoftu
description: Použití Visual Studio Code k vytvoření nového zařízení IoT Edge ve službě Azure IoT hub a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495268"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Zaregistrujte si nové zařízení Azure IoT Edge z Visual Studio Code

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro úlohy IoT Edge.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí Visual Studio Code (VS Code). Existuje více způsobů provádějí většinu operací v nástroji VS Code. Tento článek používá Průzkumníka, ale můžete také použít paletu příkazů k provedení kroků.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k provádění operací s centrem IoT. Pro tyto operace pro práci budete muset přihlásit ke svému účtu Azure a vyberte své Centrum IoT.

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.

1. V dolní části Průzkumníku, rozbalte **Azure IoT Hub** oddílu.

   ![Rozbalte část zařízení Azure IoT Hub](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. Klikněte na **...**  v **Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, klikněte na nebo najeďte myší na záhlaví.

1. Zvolte **vyberte službu IoT Hub**.

1. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů k tomu.

1. Vyberte své předplatné Azure.

1. Vyberte své Centrum IoT.

## <a name="create-a-device"></a>Vytvoření zařízení

1. V Průzkumníku VS Code rozbalte **zařízení Azure IoT Hub** oddílu.

1. Klikněte na **...**  v **zařízení Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, klikněte na nebo najeďte myší na záhlaví.

1. Vyberte **vytvoření zařízení IoT Edge**.

1. V textovém poli, které se otevře zadejte zařízení identifikátor.

V okně výstupu se zobrazí výsledek příkazu. Vytisknout informace o zařízení, která obsahuje **deviceId** , které jste zadali a **connectionString** , můžete použít k připojení fyzických zařízení do služby IoT hub.

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Všechna zařízení, která se připojují ke službě IoT hub jsou uvedeny v **Azure IoT Hub** části Průzkumníku kódu sady Visual Studio. Zařízení IoT Edge se liší od jiných hraničních zařízení s jinou ikonu a fakt, **$edgeAgent** a **$edgeHub** moduly jsou nasazené na každé zařízení IoT Edge.

   ![Zobrazit všechna zařízení IoT Edge ve službě IoT hub](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. Klikněte pravým tlačítkem na ID zařízení v **Azure IoT Hub** oddílu.

1. Vyberte **zkopírujte připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírován do schránky.

Můžete také vybrat **získat informace o zařízení** v místní nabídce zobrazíte všechny informace o zařízení, včetně připojovací řetězec v okně výstup.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly na zařízení pro Visual Studio Code](how-to-deploy-modules-vscode.md).
