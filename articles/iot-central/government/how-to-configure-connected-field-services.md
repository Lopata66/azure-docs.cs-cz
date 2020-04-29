---
title: Připojení aplikace Azure IoT Central k polím služby Dynamics 365 | Microsoft Docs
description: Naučte se vytvářet ucelená řešení s využitím Azure IoT Central a služby pole Dynamics 365.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157445"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Sestavení uceleného řešení s využitím Azure IoT Central a služby pole Dynamics 365 



Jako tvůrce můžete povolit integraci vaší aplikace Azure IoT Central do jiných podnikových systémů. 


Například v řešení pro správu propojených odpadů můžete optimalizovat odesílání nákladových kolekcí odpadků. Optimalizace se dá udělat na základě dat ze senzorů IoT z připojených přihrádek odpadu. V [aplikaci pro správu propojených odpadů v IoT Central](./tutorial-connected-waste-management.md) můžete nakonfigurovat pravidla a akce a nastavit je tak, aby aktivovaly vytváření výstrah ve službě Dynamics Field Service. Tento scénář je možné provést pomocí Microsoft Flow, který se nakonfiguruje přímo v IoT Central pro automatizaci pracovních postupů napříč aplikacemi a službami. Kromě toho je možné informace na základě aktivit služeb v poli služby odeslat zpět do Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak připojit vaši aplikaci Azure IoT Central k polím služeb Dynamics 365 

Níže uvedené procesy integrace lze snadno implementovat na základě čistého prostředí pro konfiguraci:
* Služba Azure IoT Central může odesílat informace o anomáliích zařízení do služby propojeného pole (jako upozornění IoT) pro diagnostiku.
* Služba připojeného pole může vytvořit případy nebo pracovní příkazy aktivované ze anomálií zařízení.
* Služba připojeného pole může naplánovat techniky na kontrolu, aby nedocházelo k výpadkům.
* Řídicí panel zařízení Azure IoT Central se dá aktualizovat o relevantní informace o službě a plánování.


## <a name="next-steps"></a>Další kroky
* Další informace o [IoT Central státních šablonách](./overview-iot-central-government.md)
* Další informace o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Další informace o [službách pro pole Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
