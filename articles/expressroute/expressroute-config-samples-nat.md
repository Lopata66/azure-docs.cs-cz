---
title: 'Azure ExpressRoute: Ukázky konfigurace směrovače – překlad adres (NAT)'
description: Tato stránka poskytuje ukázky konfigurace směrovače pro směrovače Cisco a Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74072113"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Ukázky konfigurace směrovače pro nastavení a správu překladu adres (NAT)

Tato stránka poskytuje ukázky konfigurace NAT pro směrovače Cisco ASA a Juniper SRX Series při práci s ExpressRoute. Jsou určené jenom pro podrobné pokyny a nesmí se používat jako. S vaším dodavatelem můžete pracovat s odpovídajícími konfiguracemi pro vaši síť.

> [!IMPORTANT]
> Ukázky na této stránce jsou určené pouze pro doprovodné materiály. Musíte pracovat s prodejem a technickým týmem vašeho dodavatele a týmem vaší sítě, aby se dosáhlo správných konfigurací, které vyhovují vašim potřebám. Společnost Microsoft nebude podporovat problémy související s konfiguracemi uvedenými na této stránce. Pokud potřebujete problémy s podporou, obraťte se na dodavatele zařízení.
> 
> 

* Níže uvedené ukázky konfigurace směrovače se vztahují na partnerské vztahy Azure a veřejné partnerské vztahy Microsoftu. Pro privátní partnerské vztahy Azure nesmíte nakonfigurovat překlad adres (NAT). Další podrobnosti najdete v požadavcích na [partnerské vztahy ExpressRoute](expressroute-circuit-peerings.md) a [ExpressRoute překladu adres (NAT)](expressroute-nat.md) .

* Pro připojení k Internetu a ExpressRoute je nutné použít samostatné fondy IP adres NAT. Použití stejného fondu IP adres NAT v Internetu a ExpressRoute bude mít za následek asymetrické směrování a ztrátu připojení.


## <a name="cisco-asa-firewalls"></a>Firewally Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Konfigurace PAT pro provoz ze sítě zákazníka do Microsoftu
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Konfigurace PAT pro provoz od Microsoftu do zákaznické sítě

**Rozhraní a směr:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Rozšířeného**

Fond NAT:

    object network outbound-PAT
        host <NAT-IP>

Cílový server:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Skupina objektů pro IP adresy zákazníka

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Příkazy NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Směrovače společnosti Juniper SRX Series
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Vytvořte redundantní rozhraní sítě Ethernet pro cluster.
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Vytvořte dvě zóny zabezpečení
* Zóna důvěryhodnosti pro interní síť a nedůvěryhodnou zónu pro vnější hraniční směrovače sítě
* Přiřazení vhodných rozhraní k zónám
* Povolení služeb v rozhraních

    zabezpečení {Zones {Security-Zone Trust {System-Inbound-provoz {System-Services {test;                   } protokoly {BGP;                   }} rozhraní {RETH 0.100;               }} zóna Security nedůvěřuje {System-Inbound-provoz {System-Services {test;                   } protokoly {BGP;                   }} rozhraní {RETH 1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. vytvoření zásad zabezpečení mezi zónami
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. konfigurace zásad NAT
* Vytvořte dva fondy NAT. Ten se použije pro přenos dat NAT do Microsoftu a dalších od Microsoftu po zákazníka.
* Vytvořit pravidla pro NAT odpovídající provozu
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. konfigurace protokolu BGP pro inzerování selektivních předpon v každém směru
Přečtěte si ukázky na stránce [ukázky konfigurace směrování](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. vytvoření zásad
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Další kroky
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

