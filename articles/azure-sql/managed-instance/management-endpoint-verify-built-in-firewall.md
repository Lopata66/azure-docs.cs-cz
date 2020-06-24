---
title: Ověření zabezpečení portů v integrované bráně firewall
description: Přečtěte si, jak ověřit integrovanou ochranu brány firewall ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708701"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Ověřte vestavěnou bránu firewall Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Povinná příchozí pravidla zabezpečení](connectivity-architecture-overview.md#mandatory-inbound-security-rules) Azure SQL Managed instance vyžadují, aby byly porty pro správu 9000, 9003, 1438, 1440 a 1452 otevřené z **libovolného zdroje** ve skupině zabezpečení sítě (NSG), která chrání SQL Managed instance. I když jsou tyto porty otevřeny na úrovni NSG, jsou chráněny na úrovni sítě pomocí integrované brány firewall.

## <a name="verify-firewall"></a>Ověřit bránu firewall

Chcete-li ověřit tyto porty, použijte k otestování těchto portů nástroj pro kontrolu zabezpečení. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověřování integrované brány firewall](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě SQL Managed instance a připojení najdete v tématu [Architektura připojení spravované instance Azure SQL](connectivity-architecture-overview.md).
