---
title: Jak používat Automatické zřizování Azure IoT Hub Device Provisioning Service k registraci MXChip IoT DevKit s IoT Hub | Microsoft Docs
description: Jak používat Automatické zřizování Azure IoT Hub Device Provisioning Service (DPS) k registraci DevKit IoT pomocí IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975648"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Pomocí automatického zřizování Azure IoT Hub Device Provisioning Service Zaregistrujte MXChip IoT DevKit s IoT Hub

Tento článek popisuje, jak pomocí [automatického zřizování](concepts-auto-provisioning.md)Azure IoT Hub Device Provisioning Service zaregistrovat MXChip IoT DevKit s Azure IoT Hub. V tomto kurzu se naučíte:

* Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
* Pomocí jedinečného tajného klíče zařízení (UDS) Vygenerujte certifikát X. 509.
* Zaregistrujte jednotlivá zařízení.
* Ověřte, jestli je zařízení zaregistrované.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je All-in-One kompatibilní s Arduino s bohatými periferními a snímači. Můžete ji vyvíjet pro IT pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo rozšíření sady [nástrojů Azure iot Tools](https://aka.ms/azure-iot-tools) v Visual Studio Code. DevKit se dodává s rostoucím [katalogem projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) , které vám pomohou s řešením prototypů Internet věcí (IoT), která využívají služby Azure.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto kurzu nejdříve proveďte následující úlohy:

* Nakonfigurujte Wi-Fi DevKit a připravte vývojové prostředí pomocí postupu v části "Příprava vývojového prostředí" v tématu [připojení IoT DEVKIT AZ3166 k Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Upgradujte na nejnovější firmware (1.3.0 nebo novější) pomocí kurzu [aktualizovat DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Vytvořením a propojením IoT Hub instance služby Device Provisioning podle kroků v části [nastavení IoT Hub Device Provisioning Service s Azure Portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

1. Ujistěte se, že vaše aplikace IoT DevKit není **připojená** k vašemu počítači. Nejprve začněte VS Code a pak připojte DevKit k počítači.

1. Kliknutím na `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: otevřít příklady...** . Pak jako panel vyberte **IoT DevKit** .

1. Na stránce příklady IoT Workbench Najděte **registraci zařízení pomocí DPS** a klikněte na **otevřít ukázku**. Pak vybere výchozí cestu pro stažení ukázkového kódu.
    ![otevřít ukázku](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Uložení jedinečného tajného klíče zařízení v úložišti zabezpečení zařízení

Automatické zřizování se dá nakonfigurovat na zařízení na základě [mechanismu ověřování](concepts-security.md#attestation-mechanism)zařízení. MXChip IoT DevKit používá [modul složení identity zařízení](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) ze [skupiny důvěryhodných computingů](https://trustedcomputinggroup.org). K vygenerování jedinečného [certifikátu X. 509](concepts-security.md#x509-certificates)se používá **jedinečný tajný klíč zařízení** (UDS) uložený ve STSAFE bezpečnostním čipu ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) v DevKit. Certifikát se používá později pro proces registrace ve službě Device Provisioning a během registrace za běhu.

Typickou na UDS je řetězec 64 znaků, jak je vidět v následující ukázce:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Uložení UDS na DevKit:

1. V VS Code klikněte na stavový řádek a vyberte port COM pro DevKit.
  ![vyberte port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. V DevKit stiskněte **tlačítko**a, nahrajte a uvolněte tlačítko **obnovit** a pak uvolněte **tlačítko**a. Vaše DevKit vstoupí do režimu konfigurace.

1. Kliknutím na `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: Konfigurace nastavení zařízení... Jedinečný řetězec zařízení > config (UDS)** .
  ![nakonfigurovat UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Poznamenejte si generovaný řetězec UDS. Budete ho potřebovat k vygenerování certifikátu X. 509. Pak stiskněte `Enter`.
  ![kopírovat](media/how-to-connect-mxchip-iot-devkit/copy-uds.png) UDS

1. Potvrďte od oznámení, že na STSAFE bylo úspěšně nakonfigurováno UDS.
  ![nakonfigurovat úspěšnost UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Případně můžete přes sériový port nakonfigurovat UDS pomocí nástrojů, jako je například výstup. V takovém případě [použijte režim konfigurace](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) .

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizuje globální koncový bod zařízení a rozsah ID.

V části kód zařízení musíte zadat [koncový bod zřizování zařízení](/azure/iot-dps/concepts-service#device-provisioning-endpoint) a rozsah ID, abyste zajistili izolaci tenanta.

1. V Azure Portal Vyberte podokno **Přehled** služby Device Provisioning a poznamenejte si hodnoty **globální koncový bod zařízení** a **Rozsah ID** .
  ![globální koncový bod služby Device Provisioning a rozsah ID](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otevřete **DevKitDPS. ino**. Vyhledejte a nahraďte `[Global Device Endpoint]` a `[ID Scope]` hodnoty, které jste právě poznamenali.
  Koncový bod služby ![Device Provisioning](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Vyplňte `registrationId` proměnnou v kódu. Povolují se jenom alfanumerické znaky, malá písmena a spojovníky, které mají maximálně 128 znaků. Také se poznamenala hodnota.
  ID registrace ![](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klikněte na `F1`, zadejte a vyberte **Azure IoT Device Workbench: nahrání kódu zařízení**. Spustí kompilování a nahrání kódu do DevKit.
  ![nahrávání zařízení](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generovat certifikát X. 509

[Mechanismus ověřování](/azure/iot-dps/concepts-device#attestation-mechanism) , který používá tato ukázka, je certifikát X. 509. K jeho vygenerování musíte použít nástroj.

1. V VS Code klikněte na `F1`, zadejte a vyberte **Otevřít nový terminál** . otevře se okno terminálu.

1. Spusťte `dps_cert_gen.exe` v `tool` složce.

1. Zadejte umístění zkompilovaného binárního souboru jako `..\.build\DevKitDPS`. Pak vložte **UDS** a **registrationId** , které jste právě poznamenali. 
  ![generovat X. 509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. `.pem` certifikát X. 509 generuje ve stejné složce.
  ![soubor X. 509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. V Azure Portal otevřete službu Device Provisioning, přejděte do části Správa registrací a klikněte na **přidat jednotlivou registraci**.
  ![přidat jednotlivou registraci](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Kliknutím na ikonu souboru vedle **souboru primární certifikát. pem nebo. cer** nahrajte vygenerovaný soubor `.pem`.
  ![nahrání. pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ověřte, že je DevKit zaregistrovaný v Azure IoT Hub

V DevKit klikněte na tlačítko **resetovat** . Měl by se zobrazit **DPS připojené!** na obrazovce DevKit. Po restartování zařízení proběhne následující akce:

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
1. Služba Device Provisioning odešle zpět výzvu k registraci, na kterou vaše zařízení reaguje.
1. Po úspěšné registraci služba Device Provisioning odešle IoT Hub identifikátor URI, ID zařízení a šifrovaný klíč zpátky do zařízení.
1. Klientská aplikace IoT Hub v zařízení se připojí k vašemu centru.
1. Po úspěšném připojení k rozbočovači se zařízení zobrazí v Device Explorer IoT Hub.
  ![registrovaných zařízení](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, přečtěte si [Nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)ke službě IoT DevKit nebo si Projděte následující kanály, které vám pobudou:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak bezpečně zaregistrovat zařízení do služby Device Provisioning pomocí modulu složení identity zařízení, aby se zařízení mohlo automaticky zaregistrovat pomocí IoT Hub Azure. 

V souhrnu jste zjistili, jak:

> [!div class="checklist"]
> * Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
> * Pomocí jedinečného tajného klíče zařízení Vygenerujte certifikát X. 509.
> * Zaregistrujte jednotlivá zařízení.
> * Ověřte, jestli je zařízení zaregistrované.

Naučte se [vytvářet a zřizovat simulované zařízení](./quick-create-simulated-device.md).

