---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561668"
---
| Prostředek                                | Omezení        |
|-----------------------------------------|------------------------------|
| Předpony adres virtuální sítě                   | 600 na bránu VPN          |
| Agregované trasy protokolu BGP                    | 4 000 na bránu VPN        |
| Předpony adresy brány místní sítě  | 1000 na bránu místní sítě               |
| Připojení S2S                         | [Závisí na SKU brány.](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Připojení P2S                         | [Závisí na SKU brány.](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limit trasy P2S – IKEv2                 | 256 pro jiný systém než Windows **/** 25 pro Windows           |
| Limit trasy P2S – OpenVPN               | 1000                         |
| Max. flows                              | 100 tisíc for VpnGw1/AZ  **/**  512K for VpnGw2-4/AZ|