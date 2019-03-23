---
title: IoT DevKit do cloudu – IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit a připojte IoT DevKit AZ3166 se do služby Azure IoT Hub, kterou může odesílat data do cloudové platformy Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 941455e39a32405097563b043046866aeb5c7964
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351928"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Můžete použít [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) k vývoji a řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Microsoft Azure. Zahrnuje kompatibilní s Arduino panel s bohatou periferní zařízení a senzorů, balíček panel open source a stále se rozšiřující [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co můžete dělat

Připojení DevKit do služby Azure IoT hub, kterou vytvoříte. Potom shromažďovat teploty a vlhkosti data ze senzorů a odesílání dat do služby IoT hub.

DevKit ještě nemáte? Zkuste [DevKit simulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) nebo [nákupu DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Co se naučíte

* Jak se připojit IoT DevKit k dispozici bezdrátový přístupový bod a příprava vývojového prostředí.
* Postup vytvoření služby IoT hub a registrace zařízení pro MXChip IoT DevKit.
* Jak shromažďovat data ze senzorů pomocí Průvodce ukázkovou aplikaci na MXChip IoT DevKit.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

* Deska MXChip IoT DevKit pomocí kabelu Micro USB. [Získat](https://aka.ms/iot-devkit-purchase).
* Počítač se systémem Windows 10 nebo macOS 10.10 +.
* Aktivní předplatné Azure. [Aktivace bezplatné 30denní zkušební verze Microsoft Azure účtu](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Připravte svůj hardware

Připojení následující hardware v počítači:

* Panel DevKit
* Micro USB kabel

![Požadovaný hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit se připojte k počítači, postupujte podle těchto kroků:

1. Připojte USB end k počítači.

2. Připojení k DevKit end Micro USB.

3. Zelená LED výkon potvrdí připojení.

   ![Připojení hardwaru](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurace Wi-Fi

Spolehněte se na připojení k Internetu projekty IoT. Použijte následující pokyny ke konfiguraci DevKit pro připojení k Wi-Fi.

### <a name="enter-ap-mode"></a>Zadejte režim Asie a Tichomoří

Podržte tlačítko B, nabízených oznámení a uvolněte tlačítko resetovat a pak uvolněte tlačítko B. Vaše DevKit přejde do režimu přístupový bod ke konfiguraci Wi-Fi. Na obrazovce se zobrazí identifikátor service set identifier (SSID) DevKit a konfigurace portálu IP adresy.

![Resetovat tlačítko, tlačítko B a SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![Nastavení režimu Asie a Tichomoří](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Připojte se k DevKit Asie a Tichomoří

Nyní použijte jiné povolené Wi-Fi zařízení (počítač nebo mobilní telefon) pro připojení k SSID DevKit (zvýrazněný na předchozím obrázku). Ponechte prázdné heslo.

![Informace o síti a tlačítko pro připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurace Wi-Fi DevKit

Otevřete IP adresou uvedenou na obrazovce DevKit do svého počítače nebo mobilního telefonu prohlížeče, vyberte, který chcete DevKit pro připojení k síti Wi-Fi a pak zadejte heslo. Vyberte **Connect** (Připojit).

![Pole pro heslo a tlačítko pro připojení](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Když je připojení úspěšné, DevKit restartuje za pár sekund. Zobrazí název sítě Wi-Fi a IP adresu na obrazovce:

![Název sítě Wi-Fi a IP adresy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Pro IoT DevKit práci budete potřebovat síť 2,4 GHz. Modul Wi-Fi na IoT DevKit není kompatibilní s 5GHz sítí. Zkontrolujte [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) další podrobnosti.

Po dokončení konfigurace Wi-Fi svoje přihlašovací údaje se uloží na zařízení pro toto připojení i v případě, zařízení je odpojen. Pokud nakonfigurujete DevKit pro Wi-Fi v domácnosti a pak proveďte DevKit kanceláři, je potřeba překonfigurovat režimu Asie a Tichomoří (cena začíná na krok v části "Zadejte přístupový bod režim") pro připojení DevKit pro pobočku Wi-Fi.

## <a name="start-using-the-devkit"></a>Začněte používat DevKit

Výchozí aplikaci spuštěnou v prostředí DevKit zkontroluje nejnovější verzi firmwaru a zobrazí data ze senzorů diagnostiku za vás.

### <a name="upgrade-to-the-latest-firmware"></a>Upgrade na nejnovější firmware

> [!NOTE]
> Od verze 1.1 DevKit umožňuje BEZPEČNÉ ST v zaváděcího programu pro spouštění. Budete muset upgradovat firmware, pokud používáte verzi nižší než verze 1.1.

Pokud potřebujete upgrade firmwaru, se na obrazovce zobrazil firmware aktuální a nejnovější verze. Chcete-li provést upgrade, postupujte [Upgrade firmwaru](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) průvodce.

![Zobrazení aktuálních a nejnovější firmware verzí](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Toto je jednorázová úsilí. Poté, co začít s vývojem pro DevKit a nahrát aplikaci, budou přicházet nejnovější firmware s vaší aplikací.

### <a name="test-various-sensors"></a>Testování různých senzorů

Kliknutím na tlačítko B testování snímačům. Pokračujte stisknutím a uvolněním tlačítka B k cyklování skrze každý senzoru.

![Zobrazit tlačítko B a snímače](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Doporučujeme [nástroje Azure IoT](https://aka.ms/azure-iot-tools) balíček rozšíření pro Visual Studio Code pro vývoj na DevKit. Obsahuje nástroje Azure IoT [Workbench zařízení Azure IoT](https://aka.ms/iot-workbench) vyvíjet a ladit s různými zařízeními IoT devkit a [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit) ke správě a interakci s Azure IoT Hub.

Můžete sledovat ty [Channel 9](https://channel9.msdn.com/) videa, která mají přehled o jejich význam:
* [Úvod do nové aplikace IoT Workbench rozšíření pro VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [Co je nového v rozšíření IoT Toolkit pro VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

Použijte následující postup Příprava vývojového prostředí pro DevKit:

1. Nainstalujte [rozhraním Arduino IDE](https://www.arduino.cc/en/Main/Software). Poskytuje nezbytné sady nástrojů pro kompilaci a nahrání kódu Arduino.
    * **Windows:** Používejte verzi Instalační služby systému Windows. Neinstalujte z App Store.
    * **macOS**: Přetáhnout myší extrahované **Arduino.app** do `/Applications` složky.
    * **Ubuntu**: Například rozbalte do složky `$HOME/Downloads/arduino-1.8.8`

2. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/), editor zdrojového kódu napříč platformami s výkonné vývojářské nástroje, jako je doplňování kódu IntelliSense a ladění.

3. Spusťte VS Code, vyhledejte **Arduino** v marketplace pro rozšíření a nainstalujte ho. Toto rozšíření nabízí vylepšené prostředí pro vývoj na platformě Arduino.
    ![Nainstalujte Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Vyhledejte **nástroje Azure IoT** v marketplace pro rozšíření a nainstalujte ho.
    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Nainstalovat balíček rozšíření pro nástroje Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Konfigurace VS Code s rozhraním Arduino nastavení.

    Ve Visual Studio Code, klikněte na tlačítko **soubor > Předvolby > Nastavení**. Klikněte **...**  a **otevřete settings.json**.
    ![Instalace nástrojů Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Přidejte následující řádky do konfigurace Arduino v závislosti na vaší platformě: 

    * **Windows:**
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Nahradit **{username}** zástupný symbol níže se svým uživatelským jménem.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Arduino: Panel Správce**. Vyhledejte **AZ3166** a nainstalujte nejnovější verzi.
    ![Nainstalujte sadu SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalace ovladačů ST – odkaz

[Sv-odkaz/V2](https://www.st.com/en/development-tools/st-link-v2.html) je rozhraní USB používající IoT DevKit ke komunikaci s počítači pro vývoj. Budete muset nainstalovat na Windows tak, aby zařízení zkompilovaný kód, který DevKit. Postupujte podle kroků specifické pro operační systém umožňující počítači přístup k zařízení.

* **Windows:** Stáhnout a nainstalovat ovladač USB z [STMicroelectronics webu](https://www.st.com/en/development-tools/stsw-link009.html) pro [přímý odkaz](https://aka.ms/stlink-v2-windows).
* **macOS**: Žádný ovladač je vyžadován pro macOS.
* **Ubuntu**: Spuštění příkazů v terminálu a odhlásit se a přihlásit projeví se změna skupiny:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nyní vše je nastaveno s Příprava a konfigurace vývojového prostředí. Dejte nám ukázku "Hello World" sestavit pro IoT: odesílá telemetrická data o teplotě do služby Azure IoT Hub.

## <a name="build-your-first-project"></a>Svůj první projekt sestavit

### <a name="open-sample-code-from-sample-gallery"></a>Otevřete ukázkový kód z Galerie ukázek

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Otevřít příklady...** . Potom vyberte **IoT DevKit** jako panel.

1. Na stránce IoT aplikace Workbench příklady najít **Začínáme** a klikněte na tlačítko **otevřít ukázkové**. Potom vybere výchozí cestu pro stažení ukázkového kódu.
    ![Otevřete ukázkový](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Zřízení Azure IoT Hub a zařízení

1. V novém okně otevřeném projektu, klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Zřízení služby Azure...** . Postupujte podle podrobného průvodce na dokončení zřízení služby Azure IoT Hub a vytvoření zařízení IoT Hub.
    ![Příkaz zřizování](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Pokud nejste v Azure. Použijte místní oznámení pro přihlášení.

1. Vyberte předplatné, které chcete použít.
    ![Vyberte sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Vyberte nebo vytvořte novou [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Vyberte skupinu prostředků.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Ve skupině prostředků, které jste zadali postupujte podle průvodce a vyberte nebo vytvořte novou službu Azure IoT Hub.
    ![Vyberte službu IoT Hub kroky](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Vyberte službu IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Vybranému centru IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. V okně výstupu uvidíte Azure IoT Hub, zřídit ![zřízené centra IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Vyberte nebo vytvořte nové zařízení ve službě Azure IoT Hub jste zřídili.
    ![Vyberte zařízení IoT kroky](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Vyberte možnost pro zřízení zařízení IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nyní máte zřízené služby Azure IoT Hub a zařízení se vytvořilo v ní. Také připojovací řetězec zařízení se uloží v nástroji VS Code pro konfiguraci IoT DevKit později.
    ![Zřízení Hotovo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurace a kompilaci kódu zařízení

1. V pravém dolním stavovém, zkontrolujte **MXCHIP AZ3166** se zobrazuje jako vybraný panelu a sériového portu pomocí **STMicroelectronics** se používá.
    ![Vyberte panelu a modelu COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Konfigurace nastavení zařízení...** a pak vyberte **připojovací řetězec zařízení Config > Vyberte připojovací řetězec zařízení IoT Hub**.

1. U DevKit, podržte klávesu **tlačítko A**, push a verzi **resetování** tlačítko a pak uvolněte **tlačítko A**. Vaše DevKit přejde do režimu konfigurace a uloží připojovací řetězec.
    ![Připojovací řetězec](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klikněte na tlačítko `F1` znovu, zadejte a vyberte **Workbench zařízení Azure IoT: Nahrát kód zařízení**. Spustí se kompilace a nahrajte kód do DevKit.
    ![Nahrávání Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> Pokud je nějaké chyby nebo přerušení, můžete vždy obnovit opětovným spuštěním příkazu.

## <a name="test-the-project"></a>Testování projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Zobrazit telemetrická data odesílaná do služby Azure IoT Hub

Klikněte na ikonu power moduly na stavovém řádku otevřete sériového portu monitorování: ![Monitorování sériového portu](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Ukázková aplikace pracuje správně, když se zobrazí následující výsledky:

* Monitorování sériového portu zobrazuje zpráva odeslaná do služby IoT Hub.
* Je bliká vám kontrolka LED na MXChip IoT DevKit.

![Výstup sériové monitorování](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Zobrazení telemetrických dat přijatých službou Azure IoT Hub

Můžete použít [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro monitorování zpráv typu zařízení cloud (D2C) ve službě IoT Hub.

1. Přihlaste se [webu Azure portal](https://portal.azure.com/), najít IoT Hub, který jste vytvořili.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. V **zásady sdíleného přístupu** podokně klikněte na tlačítko **iothubowner zásady**a poznamenejte si připojovací řetězec služby IoT hub.
    ![Azure IoT Hub připojovacího řetězce](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. V nástroji VS Code, klikněte na tlačítko `F1`zadejte a vyberte **Azure IoT Hub: Nastavit připojovací řetězec služby IoT Hub**. Zkopírujte připojovací řetězec do něj.
    ![Nastavit připojovací řetězec centra IoT Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozbalte **zařízení AZURE IOT HUB** podokno na pravé straně, klikněte pravým tlačítkem myši klikněte na název zařízení, které jste vytvořili a vyberte **spustit monitorování zpráv D2C**.
    ![Monitorování zprávy D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. V **výstup** podokně, zobrazí se příchozí zprávy D2C do služby IoT Hub.
    ![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Kontrola kódu

`GetStarted.ino` Je hlavní soubor sketch Arduino.

![Zpráva D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Chcete-li zjistit, jak se odesílají telemetrii zařízení do služby Azure IoT Hub, otevřete `utility.cpp` soubor ve stejné složce. Zobrazení [Reference k rozhraní API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) se naučíte používat senzory a periferní zařízení v IoT DevKit.

`DevKitMQTTClient` Použitá je obálku **iothub_client** z [sadami SDK služby Microsoft Azure IoT a knihovny pro jazyk C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) k interakci s Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, můžete zkontrolovat v řešení [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás z [Gitteru](https://gitter.im/Microsoft/azure-iot-developer-kit). Můžete také udělit nám zpětnou vazbu přenechte komentář na této stránce.

## <a name="next-steps"></a>Další postup

Právě jste úspěšně propojili MXChip IoT DevKit ke službě IoT hub a data ze snímačů zachycené jste odeslali do služby IoT hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
