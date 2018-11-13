---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571946"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |530 GB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Redis Cache repliky (pro zajištění vysoké dostupnosti) |1 |
| Horizontální oddíly takovým premium mezipaměť pomocí clusterování |10 |

Omezení Azure Redis Cache a velikosti se liší u jednotlivých cenových úrovní. Cenové úrovně a jejich přidružené velikostí najdete v tématu [Azure Redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).

Další informace o omezení konfigurace Azure Redis Cache najdete v tématu [výchozí konfigurace serveru Redis](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Protože společnost Microsoft se provádí konfigurace a správa instancí služby Azure Redis Cache, ne všechny příkazy Redis se nepodporuje v Azure Redis Cache. Další informace najdete v tématu [Redis příkazy nejsou podporované v Azure Redis Cache](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

