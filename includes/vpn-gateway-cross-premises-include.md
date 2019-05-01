---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 14793d7f787780bfc8604e4af11eb05f1ff0d937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859723"
---
|  | **Point-to-Site** | **Site-to-Site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Podporované služby Azure** |Cloud Services a Virtual Machines |Cloud Services a Virtual Machines |[Seznam služeb](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Typické šířky pásma** |Podle skladové položky (SKU) brány |Obvykle celkem < 1 Gb/s |50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s |
| **Podporované protokoly** |SSTP (Secure Sockets Tunneling Protocol) a IPsec |Protokol IPsec |Přímé připojení přes sítě VLAN, technologie VPN od poskytovatelů síťových služeb (MPLS, VPLS…) |
| **Směrování** |Na základě trasy (RouteBased) (dynamické) |Podporujeme sítě VPN se statickým směrováním (na základě zásad – PolicyBased) nebo dynamickým směrováním (na základě trasy – RouteBased). |Protokol BGP |
| **Odolnost připojení** |aktivní-pasivní |aktivní-aktivní nebo aktivní-pasivní |aktivní-aktivní |
| **Typický případ použití** |Vytváření prototypů, vývojové/testovací/laboratorní scénáře pro cloudové služby a virtuální počítače |Vývojové/testovací/laboratorní scénáře a produkční úlohy v malém měřítku pro cloudové služby a virtuální počítače |Přístup ke všem službám Azure (ověřený seznam), úlohy podnikové třídy a kritické úlohy, zálohování, velké objemy dat, Azure jako web pro zotavení po havárii |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Ceny** |[Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Ceny](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technická dokumentace** |[Dokumentace ke službě VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentace ke službě VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentace ke službě ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Nejčastější dotazy** |[VPN Gateway – nejčastější dotazy](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway – nejčastější dotazy](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute – nejčastější dotazy](../articles/expressroute/expressroute-faqs.md) |
