---
title: Protokoly – Citus (škálování) – Azure Database for PostgreSQL
description: Přístup k protokolům databáze pro Azure Database for PostgreSQL – Citus (škálování)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492331"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Protokoly v Azure Database for PostgreSQL – Citus (škálování)

Protokoly PostgreSQL jsou k dispozici na všech uzlech skupiny serverů Citus (). Protokoly můžete dodávat do serveru úložiště nebo do služby Analytics Service. Protokoly je možné použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k zajištění optimálního výkonu.

## <a name="accessing-logs"></a>Přístup k protokolům

Chcete-li získat přístup k protokolům PostgreSQL pro koordinátor Citus () nebo pracovní uzel, otevřete uzel v Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="seznam uzlů":::

Pro vybraný uzel otevřete **nastavení diagnostiky** a klikněte na **+ Přidat nastavení diagnostiky**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Tlačítko Přidat nastavení diagnostiky":::

Vyberte název nového nastavení diagnostiky a zaškrtněte políčko **PostgreSQLLogs** .  Vyberte cílové umístění, do kterých mají být protokoly přijímány.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Zvolit protokoly PostgreSQL":::

## <a name="next-steps"></a>Další kroky

- [Začínáme s dotazy Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)
- Další informace o [službě Azure Event Center](../event-hubs/event-hubs-about.md)