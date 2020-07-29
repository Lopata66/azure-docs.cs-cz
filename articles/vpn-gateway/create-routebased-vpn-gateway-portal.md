---
title: 'Vytvoření brány sítě VPN založené na trasách: portál'
titleSuffix: Azure VPN Gateway
description: Vytvoření VPN Gateway založeného na směrování pomocí Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4409df576c23e0dbc3e663cc348b45bc23ad5460
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987717"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal

Tento článek vám pomůže rychle vytvořit bránu Azure VPN založenou na směrování pomocí Azure Portal.  Brána sítě VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuální sítě můžete použít taky bránu VPN. 

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na směrování (bránu virtuální sítě). Po dokončení vytváření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurace a vytvoření brány

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>SKU brány úrovně Basic nepodporuje ověřování IKEv2 nebo RADIUS. Pokud plánujete, že se klienti se systémem Mac připojí k vaší virtuální síti, nepoužívejte základní SKU.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Zobrazit bránu VPN

1. Po vytvoření brány na portálu přejděte na VNet1. Brána sítě VPN se zobrazí na stránce Přehled jako připojené zařízení.

   ![Připojená zařízení](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Připojená zařízení")

2. V seznamu zařízení klikněte na **VNet1GW** . zobrazí se další informace.

   ![Zobrazit bránu VPN Gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Zobrazit bránu VPN Gateway")

## <a name="next-steps"></a>Další kroky

Po vytvoření brány můžete vytvořit připojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi vaší virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
