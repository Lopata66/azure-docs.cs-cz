---
title: Pokročilé scénáře se službou Azure MFA a sítěmi VPN třetích stran – Azure Active Directory
description: Podrobní průvodci konfigurací pro Azure MFA pro integraci s Cisco, Citrix a Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad2b3075ae9d5ccd7e32f039fbbbc8583cde73c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055956"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Pokročilé scénáře se službou Azure Multi-Factor Authentication a řešeními sítí VPN třetích stran

Azure Multi-Factor Authentication umožňuje bez problémů připojovat pomocí různých řešení VPN jiných výrobců. Tento článek se týká zařízení VPN Azure Stream Analytics® Cisco, Citrix NetScaler SSL VPN zařízení a Juniper sítě zabezpečený přístup/Pulse Secure připojení zabezpečení SSL zařízení VPN. Jsme vytvořili průvodců konfigurace k vyřešení těchto tří běžné zařízení. Multi-Factor Authentication Server můžete také integrovat s nejvíce jinými systémy, které pomocí protokolu RADIUS, LDAP, IIS nebo ověřování nezaloženého na deklaracích se službou AD FS. Můžete najít další informace naleznete v [konfigurace MFA serveru](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Od 1. července 2019 společnost Microsoft již nabízí MFA Server pro nová nasazení. Noví zákazníci, kteří by chtěli požadovat použití vícefaktorového ověřování od jejich uživatelů by měla používat cloudové ověřování Azure Multi-Factor Authentication. Stávající zákazníci, kteří si aktivovali MFA Server před 1. července budou moct stáhnout nejnovější verzi aktualizace budoucí a vygenerovat aktivační přihlašovací údaje jako obvykle.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Zařízení Cisco ASA VPN a ověřování Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integruje do vašeho zařízení VPN Azure Stream Analytics® Cisco pro dodatečné zabezpečení pro sítě VPN Cisco AnyConnect® přihlášení a přístup k portálu.  Můžete použít protokol RADIUS nebo LDAP.  Vyberte jednu z následujících ke stažení podrobné podrobní průvodci konfigurací.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Cisco ASA s konfigurací Anyconnect VPN a Azure MFA pro LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Vaše zařízení Cisco ASA VPN integrovat s Azure MFA pomocí protokolu LDAP |
| [Cisco ASA s konfigurací Anyconnect VPN a Azure MFA pro RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Vaše zařízení Cisco ASA VPN integrovat s Azure MFA pomocí protokolu RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN a Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integruje vaše zařízení Citrix NetScaler SSL VPN k zajištění další zabezpečení pro přístup k portálu a Citrix NetScaler SSL VPN přihlášení.  Můžete použít protokol RADIUS nebo LDAP.  Vyberte jednu z následujících ke stažení podrobné podrobní průvodci konfigurací.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Konfigurace Citrix NetScaler SSL VPN a Azure MFA pro LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrace Citrix NetScaler SSL VPN s Azure MFA zařízení pomocí protokolu LDAP |
| [Konfigurace Citrix NetScaler SSL VPN a Azure MFA pro RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Vaše zařízení Citrix NetScaler SSL VPN integrovat s Azure MFA pomocí protokolu RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Zařízení Juniper/Pulse Secure SSL VPN a ověřování Azure Multi-Factor Authentication
Ověřování Azure Multi-Factor Authentication se integruje s Juniper/Pulse Secure SSL VPN zařízením poskytnout dodatečné zabezpečení pro přístup k portálu a Juniper/Pulse Secure SSL VPN přihlášení.  Můžete použít protokol RADIUS nebo LDAP.  Vyberte jednu z následujících ke stažení podrobné podrobní průvodci konfigurací.

| Průvodce konfigurací | Popis |
| --- | --- |
| [Juniper/Pulse zabezpečené sítě VPN SSL a Azure MFA konfiguraci protokolu LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrace s Azure MFA zařízení pomocí protokolu LDAP SSL VPN Juniper/Pulse Secure |
| [Juniper/Pulse zabezpečené sítě VPN SSL a Azure MFA konfigurace RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Vaše zařízení Juniper/Pulse Secure SSL VPN integrovat s Azure MFA pomocí protokolu RADIUS |

## <a name="next-steps"></a>Další postup

- [Posílení vaší stávající infrastruktury pro ověřování pomocí rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
