---
title: 'Azure ExpressRoute: přidání brány do virtuální sítě: Classic'
description: Nakonfigurujte bránu virtuální sítě pro virtuální síť modelu nasazení Classic pomocí prostředí PowerShell pro konfiguraci ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bcffd60596f8c2286b91eaf13be3e09bb8bec94f
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84736403"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede jednotlivými kroky pro přidání, změnu velikosti a odebrání brány virtuální sítě (VNet) pro stávající virtuální síť. Kroky pro tuto konfiguraci jsou určené konkrétně pro virtuální sítě, které byly vytvořeny pomocí **modelu nasazení Classic** a které budou použity v konfiguraci ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Před zahájením
Ověřte, že jste nainstalovali rutiny Azure PowerShell potřebné pro tuto konfiguraci.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete připojit virtuální síť k okruhu ExpressRoute. Viz [propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

