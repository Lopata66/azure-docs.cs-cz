---
title: Pravidla brány firewall na úrovni serveru
description: Pravidla brány firewall na úrovni serveru
keywords: připojení k SQL, připojovací řetězec
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66164276"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V seznamu na levé straně vyberte **všechny služby**.

3. Posuňte se a vyberte **SQL servery**.

    ![Najít na portálu pro váš server Azure SQL Database][b21-FindServerInPortal]
5. Do textového pole filtru začněte psát název vašeho serveru. Zobrazí se řádek, který jste.

6. Vyberte řádek pro váš server. Zobrazí se okno pro váš server.

7. V okně serveru, vyberte **nastavení**.

8. Vyberte **brány Firewall**.

    ![Vyberte nastavení > brány Firewall][b31-SettingsFirewallNavig]
9. Vyberte **přidat klienta IP**. Do prvního textového pole zadejte název pro nové pravidlo.

10. Zadejte hodnoty adres nízký klíč a vysoký IP pro oblast, kterou chcete povolit.

    * Může být užitečné mít nízkou hodnotu končit **.0** a vysoké hodnoty končit **.255**.

11. Vyberte **Uložit**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
