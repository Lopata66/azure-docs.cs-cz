---
title: Řešení potíží s chybami Azure IoT Hub 412002 DeviceMessageLockLost
description: Vysvětlení, jak opravit chybu 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144269"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Tento článek popisuje příčiny a řešení 412002 chyb **DeviceMessageLockLost** .

## <a name="symptoms"></a>Příznaky

Když se pokusíte odeslat zprávu typu cloud-zařízení, požadavek se nezdařil s chybou **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Příčina

Když zařízení obdrží zprávu typu cloud-zařízení z fronty (například pomocí [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), zpráva se zamkne IoT Hub po dobu vypršení časového limitu zámku 1 minuty. Pokud se zařízení pokusí dokončit zprávu po vypršení časového limitu zámku, IoT Hub vyvolá tuto výjimku.

## <a name="solution"></a>Řešení

Pokud IoT Hub neobdrží oznámení v rámci uplynutí časového limitu zámku uzamčení, nastaví zprávu zpět na stav zařazeno do *fronty* . Zařízení se může pokusit o přijetí zprávy znovu. Aby nedošlo k chybě v budoucnu, implementujte na straně zařízení logiku, která dokončí zprávu během jedné minuty od přijetí zprávy. Tento časový limit 1. minut se nedá změnit.