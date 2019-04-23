---
title: Azure správy zařízení IoT s využitím rozšíření IoT pro rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Použití rozšíření IoT pro nástroj příkazového řádku Azure pro správu zařízení Azure IoT Hub, poskytuje funkci přímých metod a možnosti správy požadované vlastnosti Dvojčete společnosti.
author: chrissie926
manager: ''
keywords: Správa zařízení Azure iot, správou zařízení azure iot hub, iot správy zařízení, správy zařízení služby iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 6b1029c5532e106c269b47e6e184b9c93faf8d09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399593"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Použití rozšíření IoT pro Azure CLI pro správu zařízení Azure IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je nové open source rozšíření IoT, která přidává do funkce [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Azure CLI zahrnuje příkazy pro komunikaci s Azure resource Manageru a koncových bodů správy. Například můžete použít rozhraní příkazového řádku Azure k vytvoření virtuálního počítače Azure nebo centra IoT. Rozšíření rozhraní příkazového řádku povolí službu pro rozšíření rozhraní příkazového řádku Azure poskytuje přístup k další funkce specifické pro služby Azure. Rozšíření IoT poskytuje vývojářům IoT příkazového řádku přístup k funkcím IoT Hub, IoT Edge a IoT Hub Device Provisioning Service.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost správy          | Úkol  |
|----------------------------|-----------|
| Přímé metody             | Ujistěte se, zařízení fungují jako je například spouštění nebo zastavování zasílání zpráv nebo restartování zařízení.                                        |
| Požadované vlastnosti dvojčete    | Umístěte zařízení do některých stavech, například nastavit kontrolku LED na zelenou nebo nastavení intervalu odeslání telemetrie do 30 minut.         |
| Ohlášené vlastnosti dvojčete   | Získejte ohlášené stav zařízení. Například zařízení hlásí, že je nyní blikající indikátor LED.                                    |
| Značky dvojčat                  | Store metadata specifická pro zařízení v cloudu. Například nasazení umístění prodejní počítače.                         |
| Dotazy dvojčete zařízení        | Dotaz pro načtení ty, které mají libovolné podmínky, jako je identifikace zařízení, které jsou k dispozici pro použití všech dvojčat zařízení. |

Podrobnější vysvětlení na rozdíly a pokyny k použití těchto možností najdete v článku [pokyny komunikace typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) a [pokyny komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub udržuje takové dvojče pro každé zařízení, která se k němu připojuje. Další informace o dvojčata zařízení, najdete v části [Začínáme s dvojčaty zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte pomocí rozšíření IoT pro Azure CLI různé možnosti správy na svém vývojovém počítači.

## <a name="what-you-do"></a>Co můžete dělat

Spuštění rozhraní příkazového řádku Azure a rozšíření IoT pro Azure CLI s různými možnostmi správy.

## <a name="what-you-need"></a>Co potřebujete

* Dokončení [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) kurzu nebo jeden z kurzů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ty zahrnují následující požadavky:

  - Aktivní předplatné Azure.
  - Azure IoT hub v rámci vašeho předplatného.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

* Zajistěte, aby že na zařízení běží v klientské aplikaci během tohoto kurzu.

* [Python 2.7x nebo Python 3.x](https://www.python.org/downloads/)

* Azure CLI. Pokud potřebujete nainstalovat, přečtěte si téma [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –version`. 

* Instalace rozšíření IoT. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-cli-iot-ext`. Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) obsahuje popis několika způsobů instalace rozšíření.

## <a name="log-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Přihlaste se ke svému účtu Azure spuštěním následujícího příkazu:

```bash
az login
```

## <a name="direct-methods"></a>Přímé metody

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Požadované vlastnosti dvojčete zařízení

Nastavit požadovanou vlastnost interval = 3000 spuštěním následujícího příkazu:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Tato vlastnost může číst z vašeho zařízení.

## <a name="device-twin-reported-properties"></a>Ohlášené vlastnosti dvojčete zařízení

Spuštěním následujícího příkazu získejte ohlášené vlastnosti zařízení:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Jeden z dvojčeti nahlásila vlastnosti $metadata. $lastUpdated která ukazuje čas poslední aplikace pro zařízení aktualizovat jeho sady ohlášené vlastnosti.

## <a name="device-twin-tags"></a>Značky dvojčat zařízení

Zobrazení značek a vlastnosti zařízení, spuštěním následujícího příkazu:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Přidání role pole = teploty a vlhkosti zařízení spuštěním následujícího příkazu:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dotazy dvojčete zařízení

Dotazování zařízení značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Dotazování všechna zařízení kromě těch značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Další postup

Jste zjistili, jak pro monitorování zpráv typu zařízení cloud a odesílání zpráv typu cloud zařízení mezi zařízení IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
