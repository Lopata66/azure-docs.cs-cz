---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712448"
---
Fluent je open source kolekce dat pro jednotné protokolování. Nastavení spravují připojení kontejneru k serveru, na který se [hovoří.](https://www.fluentd.org) `Fluentd` Kontejner obsahuje poskytovatele s mikroprotokolem, který umožňuje kontejneru zapisovat protokoly a volitelně také data metriky na server, který je na něj.

Následující tabulka popisuje nastavení konfigurace podporovaná v `Fluentd` části.

| Název | Datový typ | Popis |
|------|-----------|-------------|
| `Host` | Řetězec | IP adresa nebo název hostitele DNS serveru. |
| `Port` | Integer | Port serverového serveru.<br/> Výchozí hodnota je 24224. |
| `HeartbeatMs` | Integer | Interval prezenčního signálu v milisekundách. Pokud se před uplynutím tohoto intervalu nepošle žádný přenos události, pošle se na tento server prezenční signál. Výchozí hodnota je 60000 milisekund (1 minuta). |
| `SendBufferSize` | Integer | Velikost vyrovnávací paměti sítě (v bajtech) přidělená pro operace odeslání. Výchozí hodnota je 32768 bajtů (32 kilobajtů). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Časový limit (v milisekundách) k navázání připojení SSL/TLS k tomuto serveru. Výchozí hodnota je 10000 milisekund (10 sekund).<br/> Pokud `UseTLS` je hodnota nastavena na false, bude tato hodnota ignorována. |
| `UseTLS` | Logická hodnota | Určuje, jestli má kontejner pro komunikaci se serverem používat protokol SSL/TLS. Výchozí hodnota je False. |