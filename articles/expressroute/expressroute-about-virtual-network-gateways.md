---
title: O branách virtuálních sítí ExpressRoute – Azure | Dokumentace Microsoftu
description: Další informace o branách virtuálních sítí pro ExpressRoute. Tento článek obsahuje informace o SKU brány a typy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: d9c607114d6c6c56c25303a88dcc11f4ab804eb4
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404329"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>O branách virtuálních sítí pro ExpressRoute
Bránu virtuální sítě slouží k posílání síťového provozu mezi virtuálními sítěmi Azure a místními umístěními. Bránu virtuální sítě můžete použít pro provoz ExpressRoute nebo VPN provoz. Tento článek se zaměřuje na brány virtuální sítě ExpressRoute a obsahuje informace o SKU, zvýšily výkon s SKU a brány typů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Typy bran

Při vytváření brány virtuální sítě, budete muset zadat několik nastavení. Jeden z požadovaných nastavení "-GatewayType", určuje, jestli je brána používat pro ExpressRoute, nebo přenosy VPN. Dvě brány typy jsou:

* **VPN** – Pokud chcete posílat šifrovaný provoz přes veřejný Internet, použijte typ brány "Vpn". To se také označuje jako bránu sítě VPN. Připojení typu Site-to-Site, Point-to-Site a VNet-to-VNet používají bránu VPN.

* **ExpressRoute** – Pokud chcete posílat síťový provoz na privátní připojení, použijte typ brány "ExpressRoute". Tím se také označuje jako bránu ExpressRoute a je typ brány, kterou používá při konfiguraci ExpressRoute.

Každá virtuální síť může mít pouze jednu bránu virtuální sítě pro každý typ brány. Například můžete mít jednu bránu virtuální sítě, která má typ -GatewayType Vpn a jednu, který má typ -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU brány
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Pokud chcete provést upgrade brány na výkonnější skladové položky brány, ve většině případů můžete použít rutinu Powershellu "Změna velikosti AzVirtualNetworkGateway". To bude fungovat pro upgrade na Standard a HighPerformance SKU. Pokud chcete upgradovat na UltraPerformance SKU, musíte však znovu vytvořte bránu. Opětovné vytvoření brány způsobí výpadek.

### <a name="aggthroughput"></a>Odhadované výkonů podle SKU brány
Následující tabulka ukazuje typy brány a odhadovanou funkční. Tato tabulka platí pro model nasazení Resource Manager i pro klasický model.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Výkon aplikace závisí na několika faktorech, například na celkovou latenci a počet přenosové toky, které aplikace se otevře. Čísla v tabulce představují horní mez, které může aplikace dosáhnout teoreticky v prostředí ideální.
>
>

### <a name="zrgw"></a>Zónově redundantní SKU brány

Můžete také nasadit brány ExpressRoute v zónách dostupnosti Azure. To fyzicky a logicky je odděluje do různých zón dostupnosti, chrání před výpadky na úrovni zóny připojení k místní síti do Azure.

![Zónově redundantní brány ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zónově redundantní brány používají konkrétní nové SKU brány pro bránu ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nové SKU brány podporovat i jiné možnosti nasazení, aby co nejlépe odpovídaly vašim potřebám. Při vytváření brány virtuální sítě pomocí nové SKU brány, máte také možnost k nasazení brány v konkrétní zóně. To se označuje jako brána oblastmi. Při nasazení oblastmi brány všechny instance brány jsou nasazené ve stejné zóně dostupnosti.

## <a name="resources"></a>Rutiny Powershellu a rozhraní REST API
Pro další zdroje technických informací a požadavky na konkrétní syntaxe při použití rozhraní REST API a rutin prostředí PowerShell pro konfiguraci brány virtuální sítě naleznete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Další postup
Zobrazit [přehled ExpressRoute](expressroute-introduction.md) pro další informace o konfiguracích dostupné připojení.

Zobrazit [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) pro další informace o vytváření brány ExpressRoute.

Zobrazit [vytvořit bránu virtuální sítě zónově redundantní](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) pro další informace o konfiguraci brány zónově redundantní.
