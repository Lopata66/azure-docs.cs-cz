---
title: Vlastní výstrahy zabezpečení
description: Přečtěte si o přizpůsobitelných výstrahách zabezpečení a doporučené nápravě pomocí programu Defender pro funkce a službu IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809282"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Defender pro vlastní výstrahy zabezpečení IoT

Defender pro IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Doporučujeme, abyste vytvořili vlastní výstrahy na základě vašich znalostí očekávaného chování zařízení a zajistili, že výstrahy budou fungovat jako nejúčinnější indikátory potenciálního ohrožení v rámci jedinečného nasazení organizace a na šířku.

Následující seznamy programu Defender pro výstrahy IoT je možné definovat podle vašeho očekávaného IoT Hub nebo chování zařízení. Další informace o tom, jak přizpůsobit jednotlivé výstrahy, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Integrované vlastní výstrahy v IoT Hub

| Závažnost | Název upozornění | Zdroj dat | Description | Navrhovaná náprava |
|--|--|--|--|--|
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Počet zpráv z cloudu na zařízení (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Počet zpráv z cloudu na zařízení (Protokol AMQP) zamítnutý zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv zařízení do cloudu v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Množství zpráv ze zařízení do cloudu (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet volání přímé metody je mimo povolený rozsah. | IoT Hub | Množství přímé metody vyvolání v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet nahrávání souborů je mimo povolený rozsah. | IoT Hub | Velikost nahrávání souborů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu HTTP je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol HTTP) v časovém okně není v nakonfigurovaném povoleném rozsahu. |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv cloudu na zařízení v protokolu HTTP není v povoleném rozsahu. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet zpráv zařízení do cloudu v protokolu HTTP je mimo povolený rozsah. | IoT Hub | Velikost zařízení pro cloudové zprávy (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol MQTT) odmítnuté zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní upozornění – počet zpráv zařízení do cloudu v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv ze zařízení do cloudu (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet vyprázdnění fronty příkazů, které jsou mimo povolený rozsah | IoT Hub | Velikost fronty příkazů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet nepracujících aktualizací modulu je mimo povolený rozsah. | IoT Hub | Množství nespuštěných aktualizací v rámci určitého časového okna je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet neautorizovaných operací je mimo povolený rozsah. | IoT Hub | Množství neautorizovaných operací v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |


## <a name="agent-based-security-custom-alerts"></a>Vlastní výstrahy zabezpečení založené na agentech

| Závažnost | Název upozornění | Zdroj dat | Description | Navrhovaná náprava |
|--|--|--|--|--|
| Nízká | Vlastní výstraha – počet aktivních připojení je mimo povolený rozsah. | Klasický modul zabezpečení, Azure RTO | Počet aktivních připojení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. | Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených připojení. |
| Nízká | Vlastní výstraha – odchozí připojení vytvořené na IP adresu není povolené. | Klasický modul zabezpečení, Azure RTO | Odchozí připojení bylo vytvořeno na IP adresu, která je mimo povolený seznam IP adres. | Prozkoumejte protokoly zařízení. Zjistěte, kde připojení pochází, a zjistěte, jestli je neškodný nebo škodlivý. Pokud máte škodlivou, odstraňte možný malware a porozumět zdroji. Pokud je neškodný, přidejte zdroj do seznamu povolených IP adres. |
| Nízká | Vlastní výstraha – počet neúspěšných místních přihlášení je mimo povolený rozsah. | Klasický modul zabezpečení, Azure RTO | Počet neúspěšných místních přihlášení v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – přihlášení uživatele, který není v seznamu povolených uživatelů | Klasický modul zabezpečení, Azure RTO | Místní uživatel mimo seznam povolených uživatelů, přihlášený k zařízení. | Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení. |
| Nízká | Vlastní výstraha – proces byl spuštěn, což není povoleno. | Klasický modul zabezpečení, Azure RTO | V zařízení se spustil proces, který není povolený. | Pokud ukládáte nezpracovaná data, přejděte k účtu Log Analytics a pomocí dat Prozkoumejte zařízení, identifikujte zdroj a pak pro tato nastavení opravte seznam povolených/blokovaných dat. Pokud v současné době neukládáte nezpracovaná data, přečtěte si zařízení a opravte seznam povolených a blokovaných dat pro tato nastavení. |
|

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak [přizpůsobit upozornění](quickstart-create-custom-alerts.md)
- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
