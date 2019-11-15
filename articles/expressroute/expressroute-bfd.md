---
title: 'Azure ExpressRoute: Konfigurace BFD'
description: Tento článek obsahuje pokyny, jak nakonfigurovat BFD (obousměrné předávání detekce) přes partnerský vztah privátní okruhu ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080797"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurace BFD přes ExpressRoute

ExpressRoute podporuje rozpoznávání obousměrného předávání (BFD) jak privátní, tak partnerské vztahy Microsoftu. Povolením BFD over ExpressRoute můžete urychlit propojení mezi zařízeními Microsoft Enterprise Edge (MSEE) a směrovači, na kterých jste ukončili okruh ExpressRoute (CE/PE). ExpressRoute můžete ukončit přes hranice zákazníka zařízení pro směrování nebo směrování partnera hraničních zařízeních (Pokud jste provedli pomocí spravované služby připojení vrstvy 3). Tento dokument vás provede potřebu BFD a jak povolit BFD přes ExpressRoute.

## <a name="need-for-bfd"></a>Potřebu BFD

Následující diagram znázorňuje výhodu, že povolení BFD přes okruh ExpressRoute: [ ![1]][1]

Můžete povolit okruh ExpressRoute, buď pomocí připojení vrstvy 2 nebo spravovaná připojení vrstvy 3. V obou případech Pokud jsou na cestě připojení ExpressRoute, jeden nebo více zařízení vrstvy 2 odpovědnost zjišťování jakýchkoliv odkazů v cestě je nadřízená protokolu BGP.

Na zařízeních směrovači MSEE keepalive protokolu BGP a blokování času jsou obvykle nakonfigurované jako 60 – 180 sekund v uvedeném pořadí. Po selhání odkazu, by trvat až proto se tři minuty k nalezení propojit selhání a přepínat provoz připojení.

Časovače protokolu BGP můžete řídit konfigurací keepalive nižší protokolu BGP a dobu uchování na partnerské vztahy hraniční zařízení zákazníka. Pokud časovače protokolu BGP se neshoda mezi dvě partnerské zařízení, využije relace protokolu BGP mezi partnerské uzly nižší hodnota časovače. Keepalive protokolu BGP je možné nastavit pouhých tří sekund a doby uchování v pořadí desítky vteřin. Nastavení časovačů protokolu BGP je ale méně žádoucí, protože protokol je náročný na zpracování.

V tomto scénáři může pomoct BFD. BFD poskytuje rozpoznávání selhání připojení s nízkou režií v časovém intervalu subsecond. 


## <a name="enabling-bfd"></a>Povolení BFD

BFD je nakonfigurované ve výchozím nastavení v části všechny nově vytvořené ExpressRoute privátního partnerského vztahu rozhraní na směrovači Msee. Aby bylo možné povolit BFD, je třeba pouze nakonfigurovat BFD na vašem zařízení pro zápis a zápis na úrovni služby (na primární i sekundární zařízení). Konfigurace BFD proces se dvěma kroky: musíte nakonfigurovat BFD na rozhraní a pak ho propojit s relací protokolu BGP.

Příklad konfigurace CE/PE (s použitím Cisco IOS XE) je uvedený níže. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Povolit BFD v již existující soukromý partnerský vztah; budete muset obnovit partnerský vztah. Viz [resetování partnerských vztahů ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Vyjednávání BFD časovače

Mezi rovnocennými počítači BFD určit pomalejší dvě spolupracovníků přenosovou rychlost. Msee BFD přijímání přenosu intervalech jsou nastaveny na 300 milisekund. V některých scénářích interval může být nastaven na hodnotu vyšší 750 milisekund. Nakonfigurováním vyšší hodnoty můžete vynutit tyto intervaly delší; ale ne kratší.

>[!NOTE]
>Pokud jste nakonfigurovali geograficky redundantní okruhy ExpressRoute nebo jako zálohu používáte připojení VPN typu Site-to-site IPSec, povolením BFD pomůžete urychlit převzetí služeb při selhání po selhání připojení ExpressRoute. 
>

## <a name="next-steps"></a>Další kroky

Další informace a nápovědu najdete na následujících odkazech:

- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD urychluje propojování odvození selhání"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






