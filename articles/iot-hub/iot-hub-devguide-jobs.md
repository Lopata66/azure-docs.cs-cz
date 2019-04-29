---
title: Vysvětlení úloh služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – plánování spouštění úloh v různých zařízeních připojené ke službě IoT hub. Úlohy můžete aktualizovat značky a požadované vlastnosti a vyvolání přímých metod na více zařízeních.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: aacb0ab69dad45f9ca7655daaae0c2acff0403f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740435"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Plánování úloh na několika zařízeních

Azure IoT Hub umožňuje pomocí stavebních bloků, jako je číslo [vlastnosti dvojčat zařízení a značky](iot-hub-devguide-device-twins.md) a [přímé metody](iot-hub-devguide-direct-methods.md). Back endové aplikace obvykle povolit zařízení správci a operátoři, aktualizovat a komunikovat se zařízeními IoT hromadně a v naplánovaném čase. Úlohy spustit aktualizace dvojčat zařízení nebo přímých metod pro skupiny zařízení v naplánovaném čase. Operátor byste například použili back endové aplikace, který iniciuje a sleduje úlohu restartujte sadu zařízení v budově 43 a podlaží 3 v čase, který nebude působit rušivě pro operace vytvoření.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zvažte použití úloh když budete potřebovat k plánování a sledování průběhu některý z následujících aktivit na skupiny zařízení:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání přímých metod

## <a name="job-lifecycle"></a>Životní cyklus úlohy

Úlohy jsou iniciovaných back-end řešení a spravuje pomocí služby IoT Hub. Můžete spustit úlohu prostřednictvím služby přístupem k identifikátoru URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) a dotazování na průběh provádění úlohy prostřednictvím služby přístupem k identifikátoru URI (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). K aktualizaci stavu spuštěných úloh po zahájení úlohy, spusťte dotaz úlohy.

> [!NOTE]
> Když spustíte úlohu, názvy a hodnoty vlastností může obsahovat pouze US-ASCII tisknutelný alfanumerické znaky, s výjimkou těch následující: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Úlohy spouštějí přímé metody

Následující fragment kódu ukazuje podrobnosti o žádosti HTTPS 1.1 pro spouštění [přímá metoda](iot-hub-devguide-direct-methods.md) na sadu zařízení pomocí úlohy:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Podmínka dotazu může být také na jedno zařízení ID nebo na seznam zařízení ID, jak je znázorněno v následujícím příkladu:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Dotazovací jazyk IoT Hub](iot-hub-devguide-query-language.md) pokrývá dotazovací jazyk služby IoT Hub v dalších podrobností.

## <a name="jobs-to-update-device-twin-properties"></a>Úlohy se aktualizovat vlastnosti dvojčat zařízení

Následující fragment kódu ukazuje podrobnosti o žádosti HTTPS 1.1 pro aktualizaci vlastnosti dvojčat zařízení pomocí úlohy:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>Dotazování na průběh úlohy

Následující fragment kódu ukazuje podrobnosti o žádosti HTTPS 1.1 pro dotazování pro úlohy:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

Token continuationToken je k dispozici z odpovědi.

Můžete zadat dotaz na stav provádění úlohy na každé zařízení pomocí [dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Vlastnosti úlohy

Následující seznam obsahuje vlastnosti a odpovídající popisy, které je možné použít při dotazování pro úlohy nebo výsledky úlohy.

| Vlastnost | Popis |
| --- | --- |
| **jobId** |Aplikace uvedené ID pro úlohu. |
| **startTime** |Aplikace zadaný počáteční čas (ISO-8601) pro konkrétní úlohu. |
| **endTime** |IoT Hub poskytuje data (ISO-8601) pro dokončení úlohy. Platné až po úloze dosáhne stavu "dokončení". |
| **type** |Typy úloh: |
| | **scheduledUpdateTwin**: Úlohy, který se používá k aktualizaci sadu požadovaných vlastností nebo značky. |
| | **scheduledDeviceMethod**: Úlohy použít k vyvolání metody zařízení na sadu dvojčata zařízení. |
| **status** |Aktuální stav úlohy. Možné hodnoty pro stav: |
| | **Čekající**: Naplánované a čeká, aby ji mohla služba úloh. |
| | **Naplánované**: Naplánovat čas v budoucnosti. |
| | **Spuštění**: Aktuálně aktivní úloha. |
| | **Bylo zrušeno**: Úloha byla zrušena. |
| | **Nepovedlo**: Úloha se nezdařila. |
| | **Dokončení**: Úloha byla dokončena. |
| **deviceJobStatistics** |Statistika týkající se spuštění úlohy. |
| | **deviceJobStatistics** vlastnosti: |
| | **deviceJobStatistics.deviceCount**: Počet zařízení v rámci úlohy. |
| | **deviceJobStatistics.failedCount**: Počet zařízení, kde úloha se nezdařila. |
| | **deviceJobStatistics.succeededCount**: Počet zařízení, ve kterém úloha úspěšně. |
| | **deviceJobStatistics.runningCount**: Počet zařízení, které jsou aktuálně spuštěné úlohy. |
| | **deviceJobStatistics.pendingCount**: Počet zařízení, která čekají na spuštění úlohy. |

### <a name="additional-reference-material"></a>Další referenční materiál

Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý IoT hub zpřístupní pro operace za běhu a správy.

* [Omezování a kvótách](iot-hub-devguide-quotas-throttling.md) popisuje kvóty, které platí pro službu IoT Hub a omezování chování očekávat, když používáte službu.

* [Azure IoT zařízení a služby sady SDK](iot-hub-devguide-sdks.md) uvádí různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk služby IoT Hub. Použijte tento dotazovací jazyk k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokolu MQTT.

## <a name="next-steps"></a>Další postup

Vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujícím kurzu služby IoT Hub:

* [Úlohy vysílání a plánování](iot-hub-node-node-schedule-jobs.md)