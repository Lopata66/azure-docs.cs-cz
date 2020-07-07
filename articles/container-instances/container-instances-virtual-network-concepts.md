---
title: Scénáře použití virtuální sítě
description: Scénáře, prostředky a omezení pro nasazení skupin kontejnerů do služby Azure Virtual Network.
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 77fbdb1720e571027f28b5bdca5c0e3c65c3ded2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82584399"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scénáře a prostředky virtuální sítě

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečenou privátní síť pro vaše Azure a místní prostředky. Když nasadíte skupiny kontejnerů do služby Azure Virtual Network, můžou vaše kontejnery bezpečně komunikovat s ostatními prostředky ve virtuální síti. 

Tento článek poskytuje základní informace o scénářích, omezeních a prostředcích virtuální sítě. Příklady nasazení pomocí rozhraní příkazového řádku Azure najdete v tématu [nasazení instancí kontejnerů do služby Azure Virtual Network](container-instances-vnet.md).

## <a name="scenarios"></a>Scénáře

Skupiny kontejnerů nasazené do služby Azure Virtual Network povolují scénáře, jako jsou:

* Přímá komunikace mezi skupinami kontejnerů ve stejné podsíti
* Odeslání výstupu [úloh na základě úlohy](container-instances-restart-policy.md) z instancí kontejneru do databáze ve virtuální síti
* Načte obsah pro instance kontejnerů z [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) ve virtuální síti.
* Povolení komunikace kontejneru s místními prostředky prostřednictvím [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integrace s [Azure firewall](../firewall/overview.md) k identifikaci odchozího provozu, který pochází z kontejneru 
* Překládat názvy pomocí interní Azure DNS pro komunikaci s prostředky Azure ve virtuální síti, jako jsou třeba virtuální počítače.
* Použití pravidel NSG k řízení přístupu kontejneru k podsítím nebo jiným síťovým prostředkům

## <a name="unsupported-networking-scenarios"></a>Nepodporované scénáře sítě 

* **Azure Load Balancer** – umístění Azure Load Balancer před instancemi kontejnerů v síťové skupině kontejnerů se nepodporuje.
* **Globální partnerský vztah virtuální sítě** – globální partnerský vztah (propojení virtuálních sítí napříč oblastmi Azure) není podporovaný.
* **Veřejná IP adresa nebo popisek DNS** – skupiny kontejnerů nasazené ve virtuální síti aktuálně nepodporují vystavování kontejnerů přímo na internetu s použitím veřejné IP adresy nebo plně kvalifikovaného názvu domény.

## <a name="other-limitations"></a>Další omezení

* V současné době jsou ve skupině kontejnerů nasazené do virtuální sítě podporované jenom kontejnery Linux.
* K nasazení skupin kontejnerů do podsítě nemůže podsíť obsahovat další typy prostředků. Před nasazením skupin kontejnerů do něj odeberte všechny stávající prostředky ze stávající podsítě, nebo vytvořte novou podsíť.
* [Spravovanou identitu](container-instances-managed-identity.md) nemůžete použít ve skupině kontejnerů nasazené do virtuální sítě.
* V rámci skupiny kontejnerů nasazených do virtuální sítě nemůžete povolit [test živého provozu](container-instances-liveness-probe.md) nebo test [připravenosti](container-instances-readiness-probe.md) .
* Kvůli dalším zapojení síťových prostředků jsou nasazení do virtuální sítě obvykle pomalejší než nasazení standardní instance kontejneru.

## <a name="where-to-deploy"></a>Místo nasazení

Následující oblasti a maximální prostředky jsou k dispozici pro nasazení skupiny kontejnerů ve službě Azure Virtual Network.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Požadované síťové prostředky

Existují tři prostředky Azure Virtual Network, které jsou potřeba k nasazení skupin kontejnerů do virtuální sítě: vlastní [virtuální síť](#virtual-network) , [delegovaná podsíť](#subnet-delegated) v rámci virtuální sítě a [profil sítě](#network-profile). 

### <a name="virtual-network"></a>Virtuální síť

Virtuální síť definuje adresní prostor, ve kterém vytvoříte jednu nebo více podsítí. Potom do podsítí ve vaší virtuální síti nasadíte prostředky Azure (například skupiny kontejnerů).

### <a name="subnet-delegated"></a>Podsíť (delegovaný)

Podsítě segmentují virtuální síť do samostatných adresních prostorů použitelných prostředky Azure, které do nich umístíte. Vytvoříte jednu nebo několik podsítí v rámci virtuální sítě.

Podsíť, kterou použijete pro skupiny kontejnerů, může obsahovat pouze skupiny kontejnerů. Když nasadíte skupinu kontejnerů do podsítě poprvé, Azure deleguje tuto podsíť Azure Container Instances. Po delegování se podsíť dá použít jenom pro skupiny kontejnerů. Pokud se pokusíte nasadit jiné prostředky než skupiny kontejnerů na delegovanou podsíť, operace se nezdaří.

### <a name="network-profile"></a>Profil sítě

Profil sítě je šablona konfigurace sítě pro prostředky Azure. Určuje určité vlastnosti sítě pro prostředek, například podsíť, do které se má nasadit. Při prvním použití příkazu [AZ Container Create][az-container-create] k nasazení skupiny kontejnerů do podsítě (a tedy virtuální sítě) vytvoří Azure profil sítě za vás. Pak můžete tento profil sítě použít pro budoucí nasazení do podsítě. 

Chcete-li použít šablonu Správce prostředků, soubor YAML nebo programovou metodu pro nasazení skupiny kontejnerů do podsítě, je nutné zadat úplné ID Správce prostředků prostředku profilu sítě. Můžete použít profil, který jste vytvořili dříve pomocí [AZ Container Create][az-container-create], nebo vytvořit profil pomocí šablony Správce prostředků (viz [příklad šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) a [reference](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Pokud chcete získat ID dříve vytvořeného profilu, použijte příkaz [AZ Network Profile list][az-network-profile-list] . 

V následujícím diagramu byly nasazeny některé skupiny kontejnerů do podsítě delegované do Azure Container Instances. Po nasazení jedné skupiny kontejnerů do podsítě můžete do ní nasadit další skupiny kontejnerů zadáním stejného profilu sítě.

![Skupiny kontejnerů v rámci virtuální sítě][aci-vnet-01]

## <a name="next-steps"></a>Další kroky

* Příklady nasazení pomocí Azure CLI najdete v tématu [nasazení instancí kontejnerů do služby Azure Virtual Network](container-instances-vnet.md).
* Postup nasazení nové virtuální sítě, podsítě, profilu sítě a skupiny kontejnerů pomocí šablony Správce prostředků najdete v tématu [Vytvoření skupiny kontejnerů Azure s virtuální](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)sítí.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list

