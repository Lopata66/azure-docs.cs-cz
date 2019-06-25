---
title: Azure IoT Hub migrace do nastavení diagnostiky | Dokumentace Microsoftu
description: Postup aktualizace Azure IoT Hub a nastavení Azure diagnostics nahrazujícím operace monitorování ke sledování stavu operací ve službě IoT hub v reálném čase.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: b6cde8402c699a7477cd0efc79a44b3f5e150ad0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146329"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrace služby IoT Hub z operací do nastavení diagnostiky

Zákazníci, kteří používají [monitorování operací](iot-hub-operations-monitoring.md) sledovat stav operací ve službě IoT Hub můžete migrovat do pracovního postupu [nastavení Azure diagnostics](../azure-monitor/platform/diagnostic-logs-overview.md), funkce služby Azure Monitor. Nastavení diagnostiky zadat diagnostické informace na úrovni prostředků pro řadou služeb Azure.

**Operace je zastaralé funkce monitorování služby IoT Hub**a byla odebrána z portálu. Tento článek vysvětluje, jak přesunovat zatížení z operací do nastavení diagnostiky. Další informace o vyřazení časové osy, naleznete v tématu [monitorování řešení Azure IoT pomocí Azure monitoru a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Update IoT Hub

Aktualizace služby IoT Hub na webu Azure Portal, nejprve zapnout nastavení diagnostiky a potom vypnout monitorování operací.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Vypnout monitorování operací

> [!NOTE]
> Stejně jako z 11. března 2019 operace, které funkce monitorování se odebere ze služby IoT Hub rozhraní Azure portal. Následující postup přestanou platit. Pokud chcete migrovat, ujistěte se, že správnou kategorií jsou zapnuté v Azure Monitor nastavení diagnostiky výše.

Jakmile otestovat nové nastavení diagnostiky pracovního postupu, můžete vypnout funkci monitorování operací. 

1. V nabídce centra IoT, vyberte **monitorování operací**.

2. V každé kategorii monitorování vyberte **žádný**.

3. Uložte provoz, sledování změn.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizace aplikace, které používají monitorování operací

Schémata pro monitorování operací a nastavení diagnostiky se mírně liší. Je důležité, že můžete aktualizovat aplikace, které operace sledování dnes můžete využít k mapovány na schéma používané nastavení diagnostiky. 

Nastavení diagnostiky navíc nabízí pět nové kategorie pro sledování. Po aktualizaci aplikace pro existující schéma, přidáte nové kategorie:

* Operace dvojčete typu cloud zařízení
* Operace dvojčete zařízení cloud
* Dotazy dvojčete
* Operace úloh
* Přímé metody

Struktury konkrétní schématu naleznete v tématu [pochopit, že schéma pro nastavení diagnostiky](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorování zařízení připojit a odpojit události s nízkou latencí

K monitorování zařízení připojit a odpojit události v produkčním prostředí, doporučujeme, aby se přihlásíte k odběru [ **odpojení zařízení** události](iot-hub-event-grid.md#event-types) na služby Event Grid výstrahy a monitorování stavu připojení zařízení. Použijte tento [kurzu](iot-hub-how-to-order-connection-state-events.md) se naučíte integrovat zařízení připojené a odpojené zařízení události ze služby IoT Hub v řešení IoT.

## <a name="next-steps"></a>Další postup

[Monitorování stavu služby Azure IoT Hub a rychlá diagnostika potíží](iot-hub-monitor-resource-health.md)
