---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 9ab595d3eb81cc7d1bdd65d1e4c3f4273a2460ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "96019102"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure AD Multi-Factor Authentication (MFA) pro uživatele sítě VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Povolit ověřování

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Konfigurovat nastavení přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Možnost 1 – přístup na uživatele

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Možnost 2 – podmíněný přístup

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).