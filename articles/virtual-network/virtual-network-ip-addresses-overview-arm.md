---
title: Typy IP adres v Azure
titlesuffix: Azure Virtual Network
description: Další informace o veřejných a privátních IP adresách v Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 176cd9b0bf72a123bc644ebc27ee0e091aa54e97
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024615"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Typy IP adres a metody přidělování v Azure

Přiřazením IP adres k prostředkům Azure umožníte komunikaci s ostatními prostředky Azure, místní sítí a internetem. Existují dva typy IP adres, které můžete v Azure využít:

* **Veřejné IP adresy:** Slouží ke komunikaci s internetem, včetně veřejně přístupných služeb Azure.
* **Privátní IP adresy:** Slouží ke komunikaci v rámci virtuální sítě Azure (VNet) a místní sítě, pokud použijete VPN Gateway nebo okruh ExpressRoute pro rozšíření vaší sítě do Azure.

Prostřednictvím předpony veřejné IP adresy můžete vytvořit také souvislý rozsah statických veřejných IP adres. [Informace o předponě veřejné IP adresy](public-ip-address-prefix.md)

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Tento článek se věnuje modelu nasazení Resource Manager, který Microsoft doporučuje pro většinu nových nasazení namísto [klasického modelu nasazení](virtual-network-ip-addresses-overview-classic.md).
> 

Pokud už klasický model nasazení znáte, prohlédněte si [rozdíly v IP adresování mezi klasickým nasazením a nasazením Resource Manageru](/previous-versions/azure/virtual-network/virtual-network-ip-addresses-overview-classic#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Veřejné IP adresy

Veřejné IP adresy umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s internetovými službami a veřejně přístupnými službami Azure prostřednictvím IP adresy přiřazené prostředku. Adresa je pro prostředek vyhrazená, dokud její přiřazení nezrušíte. Pokud není k prostředku přiřazená veřejná IP adresa, prostředek stále může využívat odchozí komunikaci s internetem, ale Azure dynamicky přiřadí dostupnou IP adresu, která není pro prostředek vyhrazená. Další informace o odchozích připojeních v Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[Veřejná IP](virtual-network-public-ip-address.md) adresa v Azure Resource Manageru je prostředek, který má svoje vlastní vlastnosti. Mezi prostředky, ke kterým můžete přiřadit prostředek s veřejnou IP adresou, patří:

* Síťová rozhraní virtuálního počítače
* Internetové nástroje pro vyrovnávání zatížení
* Brány VPN Gateway
* Application Gateway
* Azure Firewall

### <a name="ip-address-version"></a>Verze IP adresy

Veřejné IP adresy se vytvářejí s IPv4 nebo IPv6 adresou. 

### <a name="sku"></a>Skladová položka

Veřejné IP adresy se vytvářejí s jednou z následujících SKU:

>[!IMPORTANT]
> Pro nástroj pro vyrovnávání zatížení a veřejné prostředky IP se musí použít odpovídající SKU. Není možné kombinovat prostředky z SKU Basic s prostředky z SKU Standard. Samostatné virtuální počítače, virtuální počítače v prostředku skupiny dostupnosti ani prostředky škálovacích sad virtuálních počítačů není možné připojit k oběma SKU zároveň.  Při nových návrzích by se měla zvážit možnost použít prostředky SKU Standard.  Podrobnosti najdete v článku o [nástroji pro vyrovnávání zatížení úrovně Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="basic"></a>Úroveň Basic

Všechny veřejné IP adresy vytvořené před zavedením skladových položek jsou veřejné IP adresy základních SKU. Se zavedením skladových položek máte možnost pro veřejnou IP adresu určit požadovanou SKU. Adresy základních SKU:

- Jsou přiřazované pomocí metody statického nebo dynamického přidělení.
- Musí mít nastavitelný časový limit nečinnosti příchozího výstupního toku 4-30 minut, výchozí hodnota je 4 minuty a pevný časový limit odchozího pocházejícího toku je 4 minuty.
- Jsou standardně otevřené.  K omezení příchozího a odchozího provozu se doporučuje použít skupiny zabezpečení sítě, ale není to nezbytné.
- Jsou přiřazované k jakémukoli prostředku Azure, ke kterému může být přiřazena veřejná IP adresa, jako jsou například síťová rozhraní, brány VPN Gateway, brány Application Gateway a internetové nástroje pro vyrovnávání zatížení.
- Nepodporují scénáře zón dostupnosti.  Pro scénáře zón dostupnosti je třeba použít veřejnou IP adresu standardního SKU. Další informace o zónách dostupnosti najdete v článku s [přehledem zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a v článku o [nástroji pro vyrovnávání zatížení úrovně Standard a zónách dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Úroveň Standard

Veřejné IP adresy standardních SKU jsou:

- Vždy použít metodu statického přidělení.
- Musí mít nastavitelný časový limit nečinnosti příchozího výstupního toku 4-30 minut, výchozí hodnota je 4 minuty a pevný časový limit odchozího pocházejícího toku je 4 minuty.
- Standardně zabezpečené a uzavřené vůči příchozímu provozu. Příchozí provoz je nutné explicitně povolit pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups).
- Přiřazeno k síťovým rozhraním, standardním veřejným nástrojům pro vyrovnávání zatížení nebo aplikačním branám. Další informace o Standard Load Balancer najdete v tématu [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Zóna redundantní ve výchozím nastavení a volitelná oblast (lze vytvořit oblast a zaručit ji v konkrétní zóně dostupnosti). Další informace o zónách dostupnosti najdete v článku s [přehledem zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a v článku o [nástroji pro vyrovnávání zatížení úrovně Standard a zónách dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Příchozí komunikace se zdrojem standardní skladové položky selže, dokud nevytvoříte a nepřidružíte [skupinu zabezpečení sítě](security-overview.md#network-security-groups) a výslovně nepovolíte požadovaný příchozí provoz.

> [!NOTE]
> Při použití [služby metadata instance IMDS](../virtual-machines/windows/instance-metadata-service.md)jsou k dispozici pouze veřejné IP adresy se základní SKU. Standardní SKU se nepodporuje.

### <a name="allocation-method"></a>Metoda přidělování

Veřejné IP adresy SKU Basic i Standard podporují metodu *statického* přidělení.  Prostředku se při vytváření přiřadí IP adresa, která se po odstranění prostředku zase uvolní.

Veřejné IP adresy SKU Basic navíc podporují metodu *dynamického* přidělení, což je výchozí metoda pro případ, že se žádná metoda přidělení nezadá.  Když se pro prostředek veřejné IP adresy úrovně Basic vybere metoda *dynamického* přidělení, IP adresa se **nepřidělí** během vytváření prostředku.  Přidělí se až při přidružení veřejné IP adresy k virtuálnímu počítači nebo po umístění první instance virtuálního počítače do back-endového fondu nástroje pro vyrovnávání zatížení úrovně Basic.   Když tento prostředek zastavíte (nebo odstraníte), IP adresa se uvolní.  Po uvolnění z prostředku A je možné IP adresu například přiřadit jinému prostředku. Pokud IP adresu přiřadíte k jinému prostředku, zatímco je prostředek A zastavený, po restartování prostředku A se mu přiřadí jiná IP adresa. Pokud změníte metodu přidělení prostředku veřejné IP adresy úrovně Basic ze *statického* přidělení na *dynamické*, adresa se uvolní. Pokud chcete zajistit, aby IP adresa přidruženého prostředku zůstala stejná, můžete explicitně nastavit *statickou* metodu přidělování. Statická IP adresa se přiřadí okamžitě.

> [!NOTE]
> Ani při nastavení *statické* metody přidělování není možné určit vlastní IP adresu přiřazenou k prostředku s veřejnou IP adresou. Azure přiřazuje IP adresu z fondu dostupných IP adres v umístění Azure, ve kterém je prostředek vytvořený.
>

Statické veřejné IP adresy se obvykle používají v následujících scénářích:

* Když potřebujete aktualizovat pravidla brány firewall pro komunikaci s prostředky Azure.
* Překlad názvů DNS, kde by změna IP adresy vyžadovala aktualizace záznamů A.
* Vaše prostředky Azure komunikují s ostatními aplikacemi nebo službami, které využívají model zabezpečení založený na IP adresách.
* Využíváte certifikáty SSL propojené k IP adrese.

> [!NOTE]
> Azure přiřazuje veřejné IP adresy z rozsahu, který je pro každou oblast v každém cloudu Azure jedinečný. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).
>

### <a name="dns-hostname-resolution"></a>Překlad názvů hostitelů DNS
Můžete zadat popisek názvu domény DNS pro veřejný IP prostředek. Na serverech DNS spravovaných Azure se vytvoří mapování *popisek_názvu_domény*.*umístění*.cloudapp.azure.com na veřejnou IP adresu. Pokud například vytvoříte prostředek s veřejnou IP adresou, který jako *popisek_názvu_domény* má **contoso** a jako *umístění* v Azure používá **Západní USA**, plně kvalifikovaný název domény (FQDN) **contoso.westus.cloudapp.azure.com** se přeloží na veřejnou IP adresu tohoto prostředku.

> [!IMPORTANT]
> Každý vytvořený popisek názvu domény musí být v rámci příslušného umístění Azure jedinečný.  
>

### <a name="dns-best-practices"></a>Osvědčené postupy DNS
Pokud budete někdy potřebovat migrovat do jiné oblasti, nemůžete migrovat plně kvalifikovaný název domény vaší veřejné IP adresy. Jako osvědčený postup můžete použít plně kvalifikovaný název domény k vytvoření vlastního záznamu CNAME domény odkazujícího na veřejnou IP adresu v Azure. Pokud potřebujete přejít na jinou veřejnou IP adresu, bude potřeba aktualizovat záznam CNAME, aniž byste museli ručně aktualizovat plně kvalifikovaný název domény na novou adresu. Pro záznam DNS můžete použít [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) nebo externího poskytovatele DNS. 

### <a name="virtual-machines"></a>Virtuální počítače

Veřejnou IP adresu můžete k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přidružit tak, že ji přiřadíte jeho **síťovému rozhraní**. Virtuálnímu počítači můžete přiřadit dynamickou nebo statickou veřejnou IP adresu. Další informace o [přiřazování IP adres k síťovým rozhraním](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Internetové nástroje pro vyrovnávání zatížení

Veřejnou IP adresu vytvořenou s kteroukoli [SKU](#sku) můžete přiřadit službě [Azure Load Balancer](../load-balancer/load-balancer-overview.md) tak, že ji přiřadíte konfiguraci **front-endu** tohoto nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení. Front-endu nástroje pro vyrovnávání zatížení můžete přiřadit dynamickou nebo statickou veřejnou IP adresu. Front-endu nástroje pro vyrovnávání zatížení můžete také přiřadit několik veřejných IP adres, což umožňuje použití scénářů s [několika VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), jako je víceklientské prostředí s weby využívajícími SSL. Další informace o skladových položkách nástroje pro vyrovnávání zatížení Azure najdete v tématu věnovaném [standardní SKU nástroje pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>Brány VPN Gateway

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) připojuje virtuální síť Azure k dalším virtuálním sítím Azure nebo k místní síti. Aby služba VPN Gateway mohla komunikovat se vzdálenou sítí, přiřadí se jí veřejná IP adresa. Službě VPN Gateway můžete přiřadit pouze *dynamickou* veřejnou IP adresu úrovně Basic.

### <a name="application-gateways"></a>Application Gateway

Veřejnou IP adresu můžete přiřadit službě [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tak, že ji přiřadíte konfiguraci **front-endu** této brány. Tato veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení. Konfiguraci front-endu služby Application Gateway v1 můžete přiřadit jenom *dynamickou* základní veřejnou IP adresu a jenom *STATICKOU* standardní adresu SKU na konfiguraci front-endu v2.

### <a name="at-a-glance"></a>Přehledně
Následující tabulka ukazuje konkrétní vlastnost, jejímž prostřednictvím je možné veřejnou IP adresu přiřadit prostředku nejvyšší úrovně, a metody přidělení (dynamické nebo statické), které je možné použít.

| Prostředek nejvyšší úrovně | Přidružení IP adresy | Dynamický | Statický |
| --- | --- | --- | --- |
| Virtuální počítač |Síťové rozhraní |Ano |Ano |
| Internetový nástroj pro vyrovnávání zatížení |Konfigurace front-endu |Ano |Ano |
| VPN Gateway |Konfigurace protokolu IP brány |Ano |Ne |
| Application Gateway |Konfigurace front-endu |Ano (jenom V1) |Ano (jenom v2) |

## <a name="private-ip-addresses"></a>Privátní IP adresy
Privátní IP adresy umožňují prostředkům Azure komunikovat s ostatními prostředky ve [virtuální](virtual-networks-overview.md) nebo místní síti prostřednictvím brány sítě VPN nebo okruhu ExpressRoute, a to bez použití IP adresy dostupné na internetu.

V modelu nasazení Azure Resource Manager se IP adresa přidruží k následujícím typům prostředků Azure:

* Síťová rozhraní virtuálního počítače
* Interní nástroje pro vyrovnávání zatížení
* Application Gateway

### <a name="allocation-method"></a>Metoda přidělování

Privátní IP adresa se přiděluje z rozsahu adres podsítě virtuální sítě, ve které je prostředek nasazený. Azure si v rozsahu adres každé podsítě vyhrazuje první čtyři adresy, takže se tyto adresy nepřiřazují prostředkům. Pokud je například rozsah adres podsítě 10.0.0.0/16, adresy 10.0.0.0-10.0.0.3 a 10.0.255.255 nelze přiřadit k prostředkům. IP adresy v rámci rozsahu adres podsítě je možné v každém okamžiku přiřadit jenom jednomu prostředku. 

Existují dvě metody přidělování privátní IP adresy:

- **Dynamická:** Azure přiřadí další dostupnou nepřiřazenou nebo nevyhrazenou IP adresu v rozsahu adres podsítě. Azure novému prostředku například přiřadí 10.0.0.10, pokud adresy 10.0.0.4-10.0.0.9 jsou už přiřazené jiným prostředkům. Dynamická metoda přidělování je výchozí metoda. Jakmile jsou dynamické IP adresy přiřazené, uvolní se pouze v případě odstranění síťového rozhraní, jeho přiřazení k jiné podsíti ve stejné virtuální síti nebo změně metody přidělování na statickou a zadání jiné IP adresy. Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu.
- **Statická:** Vyberete a přiřadíte libovolnou nepřiřazenou nebo nevyhrazenou IP adresu v rozsahu adres podsítě. Pokud rozsah adres podsítě je například 10.0.0.0/16 a adresy 10.0.0.4-10.0.0.9 jsou už přiřazené jiným prostředkům, můžete přiřadit libovolnou adresu mezi 10.0.0.10 a 10.0.255.254. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. Pokud změníte metodu přidělování na dynamickou, Azure jako dynamickou adresu dynamicky přiřadí dříve přiřazenou statickou IP adresu, a to i v případě, že tato adresa není další dostupnou adresou v rozsahu adres podsítě. Adresa se změní také v případě přiřazení síťového rozhraní k jiné podsíti ve stejné virtuální síti. Pokud však chcete síťové rozhraní přiřadit k jiné podsíti, musíte nejprve změnit metodu přidělování ze statické na dynamickou. Jakmile přiřadíte síťové rozhraní k jiné podsíti, můžete metodu přidělování změnit zpět na statickou a přiřadit IP adresu z rozsahu adres nové podsítě.

### <a name="virtual-machines"></a>Virtuální počítače

Jedna nebo několik privátních IP adres se přiřazuje k jednomu nebo několika **síťovým rozhraním** virtuálního počítače s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pro každou privátní IP adresu můžete určit dynamickou nebo statickou metodu přidělování.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Interní překlad názvů hostitelů DNS (pro virtuální počítače)

Všechny virtuální počítače Azure jsou ve výchozím nastavení nakonfigurované se [servery DNS spravovanými Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) (pokud explicitně nenakonfigurujete vlastní servery DNS). Tyto servery DNS poskytují interní překlad IP adres pro virtuální počítače umístěné ve stejné virtuální síti.

Když vytvoříte virtuální počítač, do serverů DNS spravovaných Azure se přidá mapování názvu hostitele na jeho privátní IP adresu. Pokud má virtuální počítač několik síťových rozhraní nebo několik konfigurací IP pro síťové rozhraní, název hostitele se mapuje na privátní IP adresu primární konfigurace IP primárního síťového rozhraní.

Virtuální počítače nakonfigurované se servery DNS spravovanými Azure můžou překládat názvy hostitelů všech virtuálních počítačů v rámci stejné virtuální sítě na jejich privátní IP adresy. K překladu názvů hostitelů virtuálních počítačů v propojených virtuálních sítích musíte použít vlastní server DNS.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interní nástroje pro vyrovnávání a Application Gateway

Privátní IP adresu můžete přiřadit konfiguraci **front-endu** nástroje [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) nebo služby [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tato privátní IP adresa slouží jako interní koncový bod, který je přístupný pouze pro prostředky v příslušné virtuální síti a ve vzdálených sítích připojených k virtuální síti. Konfiguraci front-endu můžete přiřadit dynamickou nebo statickou privátní IP adresu.

### <a name="at-a-glance"></a>Přehledně
Následující tabulka ukazuje konkrétní vlastnost, jejímž prostřednictvím je možné privátní IP adresu přiřadit prostředku nejvyšší úrovně, a metody přidělení (dynamické nebo statické), které je možné použít.

| Prostředek nejvyšší úrovně | Přidružení IP adresy | Dynamický | Statický |
| --- | --- | --- | --- |
| Virtuální počítač |Síťové rozhraní |Ano |Ano |
| Nástroj pro vyrovnávání zatížení |Konfigurace front-endu |Ano |Ano |
| Application Gateway |Konfigurace front-endu |Ano |Ano |

## <a name="limits"></a>Omezení
Omezení IP adresování jsou uvedená v kompletní sadě [omezení sítě](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) v Azure. Omezení platí pro jednotlivé oblasti a jednotlivá předplatná. Pokud chcete v závislosti na svých obchodních potřebách zvýšit výchozí omezení na povolené maximum, [kontaktujte podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="pricing"></a>Ceny
Za veřejné IP adresy se může účtovat nominální poplatek. Další informace o cenách IP adres v Azure najdete na stránce [Ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Další kroky
* [Nasazení virtuálního počítače se statickou veřejnou IP adresou pomocí webu Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
* [Nasazení virtuálního počítače se statickou privátní IP adresou pomocí webu Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
