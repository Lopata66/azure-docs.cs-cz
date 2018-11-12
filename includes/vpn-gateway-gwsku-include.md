---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ae3a17c9756a38414ee25fd24f7d12d6179e95f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285744"
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA.

###  <a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKU brány podle funkcí

Nová brána VPN skladové jednotky zjednodušují sady funkcí nabízen brány:

| **SKU**| **Funkce**|
| ---    | ---         |
|**Základní** (*)   | **Síť VPN založená na směrování**: 10 tunelů S2S nebo připojení; bez ověřování RADIUS pro P2S; bez IKEv2 pro P2S<br>**Síť VPN založená na zásadách**: (IKEv1): 1 tunel S2S nebo připojení, bez P2S|
| **VpnGw1, VpnGw2 a VpnGw3** | **Síť VPN založená na směrování**: až 30 tunelů (*), P2S, BGP, aktivní aktivní, vlastní protokolu IPsec/IKE zásad, koexistence ExpressRoute/VPN |
|        |             |

( * ) Můžete nakonfigurovat PolicyBasedTrafficSelectors pro připojení brány sítě VPN založené na směrování (VpnGw1, VpnGw2, VpnGw3) k několika místním zařízením brány firewall založeným na zásadách. Podrobnosti najdete v tématu věnovaném [připojení bran VPN k několika místním zařízením VPN založeným na zásadách s využitím PowerShellu](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

(\*\*) Základní SKU se považuje za starší verze SKU. Základní skladová položka má určitá omezení funkce. Nelze změnit velikost brány využívající základní SKU na jednu z nové SKU brány, musíte místo toho změnit na novou skladovou Položku, která zahrnuje odstranění a opětovné vytvoření brány VPN.

###  <a name="workloads"></a>SKU brány - produkčního prostředí vs. vývojářské a testovací úlohy

Vzhledem k rozdílům ve SLA a sadách funkcí doporučujeme pro produkční prostředí a vývoj a testování následující SKU:

| **Úloha**                       | **SKU**               |
| ---                                | ---                    |
| **Produkce, kritické úlohy** | VpnGw1, VpnGw2, VpnGw3 |
| **Vývoj a testování nebo testování konceptu**   | Basic (*)                 |
|                                    |                        |

(\*\*) Základní SKU je považován za starší verze SKU a má omezení funkcí. Ověřte, že je funkce, které potřebujete podporovat před použít základní SKU.

Pokud používáte staré SKU (starší verze), produkci se doporučují Standard a HighPerformance. Informace a pokyny, jak starých SKU najdete v tématu [skladové položky brány (starší verze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
