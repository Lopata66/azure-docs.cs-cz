---
title: 'Jak nakonfigurovat OpenVPN ve službě Azure VPN Gateway: Prostředí PowerShell | Dokumentace Microsoftu'
description: Postup konfigurace OpenVPN pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281960"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurace OpenVPN pro point-to-site Azure VPN Gateway (Preview)

Tento článek vám pomůže nastavit **OpenVPN® protokol** ve službě Azure VPN Gateway. Tento článek předpokládá, že jste již pracovní prostředí point-to-site. Pokud ho nevidíte, postupujte podle pokynů v kroku 1 k vytvoření sítě VPN point-to-site.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Registrace této funkce

Klikněte na tlačítko **TryIt** v následujícím postupu registrace tuto funkci snadno pomocí Azure Cloud Shell.

>[!NOTE]
>Pokud si tuto funkci nezaregistrujete, nebudete moct používat.
>

Po kliknutí na **Vyzkoušet** se otevře Azure Cloud Shell. Zkopírujte a vložte následující příkazy:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Jakmile bude tato funkce zaregistrovaná, znovu zaregistrujte předplatné do oboru názvů Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Vytvoření sítě VPN point-to-site

Pokud ještě nemáte funkční prostředí point-to-site, postupujte podle pokynů a vytvořte si ho. Zobrazit [vytvořit síť point-to-site VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) vytváření a konfiguraci brány VPN typu point-to-site s ověřováním pomocí nativního certifikátu Azure. 

> [!IMPORTANT]
> Základní SKU se nepodporuje pro OpenVPN.

## <a name="enable"></a>2. Povolit OpenVPN na bráně

Povolte OpenVPN na bráně. Ujistěte se, že brána je už nakonfigurovaná pro point-to-site (protokol IKEv2 nebo SSTP) před spuštěním následujících příkazů:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Další postup

Konfigurování klientů pro OpenVPN, naleznete v tématu [klienty nakonfigurovat OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**