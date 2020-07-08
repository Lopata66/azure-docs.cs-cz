---
title: Odepřít přístup k veřejné síti – Azure Portal-Azure Database for PostgreSQL-jeden server
description: Naučte se konfigurovat přístup k veřejné síti pomocí Azure Portal pro váš Azure Database for PostgreSQL jeden server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79375119"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odepřít přístup k veřejné síti na Azure Database for PostgreSQL jednom serveru pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat Azure Database for PostgreSQL jediný server pro odepření všech veřejných konfigurací a povolit jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for PostgreSQL jeden server](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit přístup k veřejné síti odepřít

Pomocí těchto kroků nastavte PostgreSQL jeden server odepřít přístup k veřejné síti:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for PostgreSQL jeden server.

1. Na stránce PostgreSQL jeden server klikněte v části **Nastavení**na **zabezpečení připojení** a otevřete stránku konfigurace zabezpečení připojení.

1. V nástroji **Odepřít přístup k veřejné síti**vyberte **Ano** , pokud chcete povolit přístup odepřít veřejný přístup pro váš PostgreSQL Server.

    ![Azure Database for PostgreSQL jeden server odepřít přístup k síti](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Úspěch Azure Database for PostgreSQL odepření přístupu k síti jednomu serveru](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).