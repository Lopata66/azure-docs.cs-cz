---
title: Konfigurace odesílání souborů pomocí webu Azure portal | Dokumentace Microsoftu
description: Jak nakonfigurovat službu IoT hub povolit nahrávání souborů z připojených zařízení pomocí webu Azure portal. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735008"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurace centra IoT nahrávání souborů pomocí webu Azure portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Nahrání souboru

Použít [funkci nahrávání souborů ve službě IoT Hub](iot-hub-devguide-file-upload.md), je třeba nejprve přidružit účet Azure Storage ve vašem Centru. Vyberte **nahrání souboru** zobrazíte seznam vlastností nahrávání souboru pro IoT hub, která je upravována.

![Nahrávání souborů služby IoT Hub zobrazení nastavení na portálu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontejner úložiště**: Pomocí webu Azure portal vyberte kontejner objektů blob v účtu služby Azure Storage v aktuálním předplatném Azure pro přidružení k službě IoT Hub. Pokud nezbytné, můžete vytvořit účet služby Azure Storage na **účty úložiště** blade a objektů blob v kontejneru **kontejnery** okno. IoT Hub automaticky generuje identifikátorů URI SAS s oprávněním pro zápis do tohoto kontejneru objektů blob pro zařízení se má použít při jejich nahrávání souborů.

   ![Na portálu zobrazovat kontejnery úložiště pro nahrání souboru](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Přijímat oznámení o nahraných souborech**: Povolí nebo zakáže oznámení o nahrávání souborů přes přepínač.

* **HODNOTA TTL SAS**: Toto nastavení je, time-to-live identifikátorů URI SAS, které vrátí zařízení služby IoT Hub. Ve výchozím nastavení má jednu hodinu, ale je možné přizpůsobit pro ostatní hodnoty pomocí posuvníku.

* **Soubor oznámení, nastavení výchozí hodnota TTL**: Oznámení time-to-live souboru nahrát, než vyprší jejich platnost. Ve výchozím nastavení má jeden den, ale je možné přizpůsobit pro ostatní hodnoty pomocí posuvníku.

* **Soubor oznámení maximální počet doručení**: Počet, kolikrát se IoT Hub pokusí doručit do souboru odeslat oznámení. Ve výchozím nastavení má 10, ale je možné přizpůsobit pro ostatní hodnoty pomocí posuvníku.

   ![Konfigurace odesílání souborů služby IoT Hub v portálu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Další postup

Další informace o možnostech nahrávání souborů služby IoT Hub najdete v tématu [nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md) v příručce pro vývojáře IoT Hub.

Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpečení řešení IoT od základů nahoru](../iot-fundamentals/iot-security-ground-up.md)
