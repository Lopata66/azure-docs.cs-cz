---
title: 'Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell: classic: Azure | Dokumentace Microsoftu'
description: Konfigurace brány virtuální sítě pro nasazení classic model virtuální sítě pomocí prostředí PowerShell pro konfiguraci služby ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.openlocfilehash: 82deca87f2410afab7b63f03b222db71db8d9b5c
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218217"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurace brány virtuální sítě pro ExpressRoute přes PowerShell (classic)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Tady jsou kroky pro tuto konfiguraci pro virtuální sítě, které byly vytvořeny pomocí **modelu nasazení classic** a, který se použije v konfiguraci služby ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Před zahájením
Ověřte, že máte nainstalovanou rutin Azure Powershellu, které jsou potřebné pro tuto konfiguraci (verze 1.0.2 nebo novější). Pokud jste nenainstalovali rutiny, budete muset udělat před zahájením kroků konfigurace. Další informace o instalaci Azure Powershellu najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Zobrazit [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

