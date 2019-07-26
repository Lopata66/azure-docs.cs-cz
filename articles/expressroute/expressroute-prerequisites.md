---
title: 'Požadavky – ExpressRoute: Azure | Microsoft Docs'
description: Tato stránka obsahuje seznam požadavků, které musí být splněné, než můžete objednat okruh Azure ExpressRoute. Zahrnuje kontrolní seznam.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f16f797afca6d60029c1f39fea7235ce84e4f954
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442217"
---
# <a name="expressroute-prerequisites--checklist"></a>Požadavky ExpressRoute a kontrolní seznam
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, musíte ověřit, že jsou splněné požadavky uvedené v následujících oddílech.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Účet Azure
* Platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení okruhu ExpressRoute. Okruhy ExpressRoute jsou prostředky v rámci předplatných Azure. Předplatné Azure je požadavkem i v případě, že připojení je omezené na cloudové služby Microsoftu mimo Azure, jako jsou například služby Office 365 a Dynamics 365.
* Aktivní předplatné Office 365 (pokud používáte služby Office 365). Další informace najdete v části specifické požadavky pro Office 365 v tomto článku.

## <a name="connectivity-provider"></a>Poskytovatel připojení

* Abyste se připojili ke cloudu Microsoftu, můžete spolupracovat s [partnerem připojení ExpressRoute](expressroute-locations.md#partners). Připojení mezi místní sítí a Microsoftem můžete vytvořit [třemi způsoby](expressroute-introduction.md)
* Pokud váš poskytovatel není partnerem připojení ExpressRoute, můžete se pořád připojit ke cloudu Microsoftu prostřednictvím [poskytovatele cloudové výměny](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Síťové požadavky
* **Redundance v každém umístění partnerského vztahu**: Společnost Microsoft vyžaduje, aby byly nastaveny redundantní relace protokolu BGP mezi směrovači společnosti Microsoft a směrovači partnerského vztahu na každém okruhu ExpressRoute (i když máte pouze [jedno fyzické připojení ke cloudové výměně](expressroute-faqs.md#onep2plink)).
* **Redundance při zotavení po havárii**: Společnost Microsoft důrazně doporučuje nastavit alespoň dva okruhy ExpressRoute v různých umístěních partnerských vztahů, aby nedocházelo k jednomu bodu selhání.
* **Směrování**: v závislosti na tom, jak připojit ke cloudu Microsoftu, vy nebo váš poskytovatel je potřeba nastavit a spravovat relace protokolu BGP pro [domény směrování](expressroute-circuit-peerings.md). Některé poskytovatelé připojení Ethernet nebo cloudu poskytovatelé exchange můžou nabízet správu protokolu BGP jako službu s přidanou hodnotou.
* **NAT**: Microsoft přijímá jenom veřejné IP adresy prostřednictvím partnerského vztahu Microsoftu. Pokud ve vaší místní síti používáte soukromé IP adresy, vy nebo váš poskytovatel musí přeložit privátní IP adresy na veřejné IP adresy [pomocí překladu síťových adres](expressroute-nat.md).
* **QoS**: Skype pro firmy má různé služby (například hlas, video, text), které vyžadují odlišnou úpravu QoS. Vy a váš poskytovatel byste měli postupovat podle [požadavků QoS](expressroute-qos.md).
* **Zabezpečení sítě:** Při připojení ke cloudu Microsoftu pomocí ExpressRoute byste měli zvážit [zabezpečení sítě](../best-practices-network-security.md).

## <a name="office-365"></a>Office 365
Pokud chcete povolit Office 365 v ExpressRoute, přečtěte si následující dokumenty, kde najdete další informace o požadavcích Office 365.

* [Přehled ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Směrování s ExpressRoute pro Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Vysoká dostupnosti a převzetí služeb při selhání s využitím ExpressRoute](https://aka.ms/erhighavailability)
* [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Plánování sítě a optimalizace výkonu pro Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Nástroje a kalkulačky šířky pásma sítě](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integrace Office 365 s místními prostředími](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Školicí videa ExpressRoute v Office 365 pro pokročilé](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Pokud máte v plánu povolit Dynamics 365 na ExpressRoute, zkontrolujte [adresy URL](https://support.microsoft.com/kb/2655102) a [rozsahy IP adres](https://support.microsoft.com/kb/2728473)Dynamics 365.

## <a name="next-steps"></a>Další postup
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Vyhledejte poskytovatele připojení ExpressRoute. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
