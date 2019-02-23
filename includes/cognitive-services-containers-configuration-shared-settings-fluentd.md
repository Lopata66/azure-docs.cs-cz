---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740895"
---
Fluentd je open source kolekce pro jednotné přihlašování. `Fluentd` Nastavení spravovat připojení kontejneru [Fluentd](https://www.fluentd.org) serveru. Kontejner obsahuje poskytovatele Fluentd protokolování, který umožňuje zapisovat protokoly kontejneru a volitelně data metriky Fluentd server.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Fluentd` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Host` | Řetězec | IP adresa nebo název hostitele DNS Fluentd serveru. |
| `Port` | Integer | Port serveru Fluentd.<br/> Výchozí hodnota je 24224. |
| `HeartbeatMs` | Integer | Interval prezenčního signálu v milisekundách. Pokud žádný provoz událostí byl odeslán před vypršením platnosti tento interval, na Fluentd server přijde prezenční signál. Výchozí hodnota je 60000 milisekund (1 minuta). |
| `SendBufferSize` | Integer | Vyrovnávací paměť sítě v bajtů přidělených pro operace odeslání. Výchozí hodnota je 32768 bajtů (32 kilobajtů). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Časový limit v milisekundách pro navázání připojení SSL/TLS s Fluentd serveru. Výchozí hodnota je 10000 milisekund (10 sekund).<br/> Pokud `UseTLS` je nastavena na hodnotu false, tato hodnota se ignoruje. |
| `UseTLS` | Logická hodnota | Označuje, zda kontejner musí používat protokol SSL/TLS pro komunikaci se serverem Fluentd. Výchozí hodnota je false. |