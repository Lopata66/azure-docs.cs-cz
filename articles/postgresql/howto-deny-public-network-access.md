---
title: Odepřít přístup k veřejné síti – Azure Portal-Azure Database for PostgreSQL-jeden server
description: Naučte se konfigurovat přístup k veřejné síti pomocí Azure Portal pro váš Azure Database for PostgreSQL jeden server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: bd777fe42dc8fa3ec4643fa9607f5ca8b7aac795
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240320"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odepřít přístup k veřejné síti na Azure Database for PostgreSQL jednom serveru pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat Azure Database for PostgreSQL jediný server pro odepření všech veřejných konfigurací a povolit jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for PostgreSQL jeden server](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit přístup k veřejné síti odepřít

Pomocí těchto kroků nastavte PostgreSQL jeden server odepřít přístup k veřejné síti:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for PostgreSQL jeden server.

1. Na stránce PostgreSQL jeden server klikněte v části **Nastavení** na **zabezpečení připojení** a otevřete stránku konfigurace zabezpečení připojení.

1. V nástroji **Odepřít přístup k veřejné síti** vyberte **Ano** , pokud chcete povolit přístup odepřít veřejný přístup pro váš PostgreSQL Server.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL jeden server odepřít přístup k síti":::

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Úspěch Azure Database for PostgreSQL odepření přístupu k síti jednomu serveru":::

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
