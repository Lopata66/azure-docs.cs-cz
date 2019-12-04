---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e08860579ca3a18093375652e0243c994c66d75b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795663"
---
Když vytváříte virtuální počítač Azure, musíte vytvořit [virtuální síť](../articles/virtual-network/virtual-networks-overview.md) (VNet), nebo použít existující VNet. Také musíte rozhodnout, jak budou vaše virtuální počítače v síti VNet dostupné. Je důležité [plánovat před vytvořením prostředků](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) a dobře porozumět [omezením síťových prostředků](../articles/azure-subscription-service-limits.md#networking-limits).

Na následujícím obrázku jsou virtuální počítače vyobrazené jako webové a databázové servery. Každá sada virtuálních počítačů je zařazena do oddělené podsítě VNet.

![Virtuální síť Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Virtuální síť můžete vytvořit předtím, než vytvoříte virtuální počítač, nebo můžete vytvořit virtuální počítač. Pro podporu komunikace s virtuálním počítačem můžete vytvořit tyto prostředky:

- Síťová rozhraní
- IP adresy
- Virtuální sítě a podsítě

Vedle těchto základních prostředků byste měli zvážit také následující volitelné:

- Skupiny zabezpečení sítě
- Nástroje pro vyrovnávání zatížení 

## <a name="network-interfaces"></a>Síťová rozhraní

[Síťové rozhraní (NIC)](../articles/virtual-network/virtual-network-network-interface.md) je propojení mezi virtuálním počítačem a virtuální sítí (VNet). Virtuální počítač musí mít alespoň jedno síťové rozhraní, ale může jich mít víc, v závislosti na své velikosti. Přečtěte si, kolik síťových adaptérů podporuje jednotlivé velikosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/sizes.md) nebo [Linux](../articles/virtual-machines/linux/sizes.md).

Můžete vytvořit virtuální počítač s více síťovými kartami a přidat nebo odebrat síťové karty v rámci životního cyklu virtuálního počítače. Více síťových adaptérů umožňuje virtuálnímu počítači připojit se k různým podsítím a odesílat nebo přijímat přenosy přes nejvhodnější rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní můžou existovat ve stejné skupině dostupnosti až do počtu podporovaného velikostí virtuálního počítače. 

Každé síťové rozhraní přidružené k virtuálnímu počítači musí být ve stejném umístění a předplatném Azure jako samotný virtuální počítač. Každé síťové rozhraní musí být připojené k virtuální síti VNet, která je ve stejném umístění a předplatném Azure jako síťové rozhraní. Můžete změnit podsíť, ke které je virtuální počítač připojený po jeho vytvoření, ale virtuální síť nemůžete změnit. Každé síťové rozhraní připojené k virtuálnímu počítači má přiřazenou adresu MAC, která se nezmění, dokud virtuální počítač neodstraníte.

Tato tabulka shrnuje metody, které můžete použít k vytvoření síťového rozhraní.

| Metoda | Popis |
| ------ | ----------- |
| Portál Azure | Když vytvoříte virtuální počítač na webu Azure Portal, síťové rozhraní se vytvoří automaticky (nejde použít síťovou kartu vytvořenou samostatně). Portál vytvoří virtuální počítač s jedinou síťovou kartou. Pokud chcete vytvořit virtuální počítač s více než jedním síťovým rozhraním, musíte použít jinou metodu. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Pomocí [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) s parametrem **-PublicIpAddressId** zadejte identifikátor veřejné IP adresy, kterou jste vytvořili dříve. |
| [Azure CLI](../articles/virtual-machines/linux/multiple-nics.md) | Pokud chcete zadat identifikátor veřejné IP adresy, kterou jste vytvořili dřív, použijte příkaz [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic) s parametrem **--Public-IP-Address** . |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování síťového rozhraní pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

## <a name="ip-addresses"></a>IP adresy 

Síťovému rozhraní v Azure můžete přiřadit tyto typy [IP adres](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md):

- **Veřejné IP adresy** – Slouží k příchozí a odchozí komunikaci (bez překladu adres (NAT)) s internetem a dalšími prostředky Azure, které nejsou připojené k virtuální síti. Přiřazení veřejné IP adresy síťovému rozhraní je volitelné. Veřejné IP adresy mají nominální poplatek a maximální počet, který je možné použít pro každé předplatné.
- **Privátní IP adresy** – Slouží ke komunikaci v rámci virtuální sítě, místní sítě a internetu (s NAT). Virtuálnímu počítači je nutné přiřadit alespoň jeden privátní IP adresu. Další informace o NAT v Azure najdete v článku [Principy odchozích připojení v Azure](../articles/load-balancer/load-balancer-outbound-connections.md).

Veřejné IP adresy můžete přiřadit virtuálním počítačům nebo internetovým nástrojům pro vyrovnávání zatížení. Privátní IP adresy můžete přiřadit virtuálním počítačům a interním nástrojům pro vyrovnávání zatížení. K přiřazování IP adres virtuálním počítačům se používá síťové rozhraní.

Existují dvě metody pro přidělování IP adres prostředkům – dynamická a statická. Výchozí metoda přidělení je dynamická, při které IP adresa není přidělená v okamžiku svého vytvoření. Místo toho se IP adresa přidělí po vytvoření virtuálního počítače nebo spuštění zastaveného virtuálního počítače. Když tento virtuální počítač zastavíte nebo odstraníte, IP adresa se uvolní. 

Pokud chcete zajistit, aby IP adresa virtuálního počítače zůstala stejná, můžete explicitně nastavit statickou metodu přidělování. V tomto případě se IP adresa přiřadí okamžitě. Uvolní se, jenom když virtuální počítač odstraníte nebo změníte jeho metodu přidělování na dynamickou.
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření IP adresy.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Veřejné IP adresy jsou ve výchozím nastavení dynamické a adresy, které jsou k nim přidružené, se mohou změnit, pokud se příslušný virtuální počítač zastaví nebo odstraní. Pokud chcete zajistit, aby virtuální počítač vždycky používal stejnou veřejnou IP adresu, vytvořte statickou veřejnou IP adresu. Ve výchozím nastavení portál při vytváření virtuálního počítače přiřadí síťovému rozhraní dynamickou privátní IP adresu. Po vytvoření virtuálního počítače můžete tuto IP adresu změnit na statickou.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Použijte [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) s parametrem **-element allocationmethod** jako dynamickou nebo statickou. |
| [Azure CLI](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Použijte [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) s parametrem **--allocation-method** Dynamic nebo Static. |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování veřejné IP adresy pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

Vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači tak, že ji přiřadíte síťovému rozhraní.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě

Podsíť je rozsah IP adres ve virtuální síti. Virtuální síť můžete z organizačních a bezpečnostních důvodů rozdělit do několika podsítí. Každé síťové rozhraní ve virtuálním počítači je připojené k jedné podsíti v rámci jedné virtuální sítě. Síťová rozhraní připojená k podsítím (stejným nebo různým) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace.

Při nastavování virtuální sítě se zadává topologii, včetně dostupných adresních prostorů a podsítí. Pokud virtuální síť má být připojená k dalším virtuálním nebo místním sítím, musíte vybrat rozsahy adres, které se nepřekrývají. IP adresy jsou privátní a nelze k nim přicházet z Internetu, což platilo pouze pro IP adresy, které nejsou směrovatelné jako 10.0.0.0/8, 172.16.0.0/12 nebo 192.168.0.0/16. V současnosti Azure považuje všechny rozsah adres za součást privátního IP adresního prostoru virtuální sítě, který je dostupný jenom v rámci příslušné virtuální sítě, vzájemně propojených virtuálních sítí a místního umístění. 

Pokud pracujete v organizaci, ve které je za interní sítě zodpovědný někdo jiný, měli byste se před výběrem adresního prostoru na tuto osobu obrátit. Zkontrolujte, že nedochází k překrytí, a informujte o adresním prostoru, který chcete použít, aby se stejný rozsah IP adres nepokoušel použít někdo jiný. 

Ve výchozím nastavení mezi podsítěmi neexistuje žádná hranice zabezpečení, takže virtuální počítače v každé z těchto podsítí mohou vzájemně komunikovat. Můžete ale nastavit skupiny zabezpečení sítě (NSG), které umožňují řídit tok do a ze sítí a také do a z virtuálních počítačů. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuální sítě a podsítí. 

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/quick-create-portal.md) | Pokud při vytváření virtuálního počítače necháte Azure vytvořit virtuální síť, bude její název kombinací názvu skupiny prostředků, která tuto virtuální síť obsahuje, a řetězce **-vnet**. Adresní prostor je 10.0.0.0/24, název požadované podsítě je **default** a rozsah adres podsítě je 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | K vytvoření podsítě a virtuální sítě použijete [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) . K přidání podsítě do existující virtuální sítě můžete také použít [příkaz Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) . |
| [Azure CLI](../articles/virtual-network/quick-create-cli.md) | Podsíť a virtuální síť se vytvoří ve stejnou dobu. Pro [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) zadejte parametr **--subnet-name** s názvem podsítě. |
| Šablona | Nejjednodušší způsob, jak vytvořit virtuální síť a podsítě, je stáhnout existující šablonu, například [Virtual Network se dvěma podsítěmi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), a upravit ji podle svých potřeb. |

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

[Skupina zabezpečení sítě (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zamítají síťový provoz pro podsítě, síťová rozhraní nebo oboje. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým síťovým rozhraním připojeným k podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny virtuální počítače v této podsíti. Provoz směřující do konkrétního síťového rozhraní se navíc dá omezit tím, že se přímo k tomuto síťovému rozhraní přidruží skupina NSG.

Skupiny NSG obsahují dvě sady pravidel: příchozí a odchozí. Priorita pravidla musí být v rámci jednotlivých sad jedinečná. Každé pravidlo má vlastnosti, které popisují protokol, zdrojový a cílový rozsah portů, předpony adres, směr přenosu, prioritu a typ přístupu. 

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte. 

Pokud přidružíte skupinu NSG k síťové kartě, pravidla pro přístup k síti obsažená v této skupině se použijí jenom na tuto síťovou kartu. Pokud se skupina NSG použije pro jedno síťové rozhraní ve virtuálním počítači s několika síťovými rozhraními, neovlivní provoz směřující do ostatních síťových rozhraní. K síťovému rozhraní (nebo virtuálnímu počítači, podle modelu nasazení) a podsíti, ke které se síťové rozhraní nebo virtuální počítač váže, můžete přidružit různé skupiny NSG. Priorita se určuje na základě směru provozu.

Při plánování virtuálních počítačů a virtuální sítě nezapomeňte [naplánovat](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) skupiny NSG.

Tato tabulka shrnuje metody, které můžete použít k vytvoření skupiny zabezpečení sítě.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/tutorial-filter-network-traffic.md) | Když vytvoříte virtuální počítač na webu Azure Portal, automaticky se vytvoří skupina NSG a přidruží se k síťovému rozhraní, které tento portál vytvořil. Název této skupiny NSG je kombinací názvu virtuálního počítače a řetězce **-nsg**. Tato skupina NSG obsahuje jedno příchozí pravidlo s prioritou 1 000, nastavenou službou RDP, protokolem TCP, portem 3389 a akcí nastavenou na Povolit. Pokud chcete k tomuto virtuálnímu počítači povolit další příchozí provoz, musíte do této skupiny NSG přidat další pravidla. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Použijte [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) a poskytněte informace o požadovaném pravidle. Pomocí [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) vytvořte NSG. Pomocí [set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) nakonfigurujte NSG pro podsíť. K přidání NSG do virtuální sítě použijte [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) . |
| [Azure CLI](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | K úvodnímu vytvoření NSG použijte [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg). Pro přidání pravidel k NSG použijte [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule). K přidání NSG do podsítě použijte [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet). |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování skupiny zabezpečení sítě pomocí šablony použijte článek věnovaný [vytvoření skupiny zabezpečení sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create). |

## <a name="load-balancers"></a>Nástroje pro vyrovnávání zatížení

[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Nástroj pro vyrovnávání zatížení je možné nakonfigurovat tak, aby [vyrovnával příchozí přenosy z Internetu](../articles/load-balancer/load-balancer-internet-overview.md) k virtuálním počítačům nebo aby [vyrovnával přenosy mezi virtuálními počítači ve virtuální síti](../articles/load-balancer/load-balancer-internal-overview.md). Nástroj pro vyrovnávání zatížení může také vyrovnávat přenosy mezi místními a virtuálními počítači na různých místech v síti nebo předávat externí přenosy konkrétnímu virtuálnímu počítači.

Nástroj pro vyrovnávání zatížení mapuje příchozí a odchozí provoz mezi svou veřejnou IP adresou a portem a privátní IP adresou a portem virtuálního počítače.

Když vytvoříte nástroj pro vyrovnávání zatížení, musíte taky zvážit tyto konfigurační prvky:

- **Konfigurace front-endových IP adres** – Nástroj pro vyrovnávání zatížení může zahrnovat jednu nebo několik front-endových IP adres, které se taky označují jako virtuální IP adresy (VIP). Tyto IP adresy slouží jako vstup pro přenos.
- **Fond back-endových adres** – IP adresy, které jsou přiřazené síťovému rozhraní, na které se distribuuje zatížení.
- **Pravidla NAT** – Definují, jakým způsobem příchozí přenos prochází front-endovou IP adresou a distribuuje se na back-endovou IP adresu.
- **Pravidla nástroje pro vyrovnávání zatížení** – Mapují konkrétní kombinaci front-endové IP adresy a portu na sadu kombinací back-endových IP adres a portů. Nástroj pro vyrovnávání zatížení může mít několik pravidel vyrovnávání zatížení. Každé pravidlo je kombinací front-endové IP adresy a portu a back-endové IP adresy a portu přidružených k virtuálním počítačům.
- **[Sondy](../articles/load-balancer/load-balancer-custom-probe-overview.md)** – Monitorují stav virtuálních počítačů. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení k virtuálnímu počítači, který není v pořádku. Stávající připojení to neovlivní a nová připojení jsou odesílaná virtuálním počítačům, které jsou v pořádku.

Tato tabulka shrnuje metody, které můžete použít k vytvoření internetového nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| Portál Azure |  [Pomocí Azure Portal můžete vyrovnávat zatížení internetového provozu na virtuálních počítačích](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md) | Pokud chcete zadat identifikátor veřejné IP adresy, kterou jste vytvořili dřív, použijte [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PublicIpAddress** . Pomocí [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu back-end adres. Pomocí [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte pravidla příchozího překladu adres (NAT) přidružená ke konfiguraci front-endové IP adresy, kterou jste vytvořili. Pomocí [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) můžete vytvořit sondy, které potřebujete. Pomocí [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření nástroje pro vyrovnávání zatížení použijte [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte [az network lb create](https://docs.microsoft.com/cli/azure/network/lb). Pro přidání veřejné IP adresy, kterou jste vytvořili dřív, použijte [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip). K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe). |
| [Šablona](../articles/load-balancer/load-balancer-get-started-internet-arm-template.md) | Jako vodítko při nasazování nástroje pro vyrovnávání zatížení pomocí šablony použijte článek věnovaný [vytvoření dvou virtuálních počítačů v nástroji pro vyrovnávání zatížení a konfiguraci pravidel NAT v tomto prostředí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules). |
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření interního nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| Portál Azure | [Zatížení interního provozu můžete vyrovnávat pomocí základního nástroje pro vyrovnávání zatížení v Azure Portal](../articles/load-balancer/tutorial-load-balancer-basic-internal-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Pokud chcete v podsíti sítě zadat privátní IP adresu, použijte příkaz [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PrivateIpAddress** . Pomocí [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu back-end adres. Pomocí [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte pravidla příchozího překladu adres (NAT) přidružená ke konfiguraci front-endové IP adresy, kterou jste vytvořili. Pomocí [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) můžete vytvořit sondy, které potřebujete. Pomocí [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření nástroje pro vyrovnávání zatížení použijte [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte příkaz [az network lb create](https://docs.microsoft.com/cli/azure/network/lb). K definování privátní IP adresy použijte [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) s parametrem **--private-ip-address**. K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe).|
| [Šablona](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Jako vodítko při nasazování nástroje pro vyrovnávání zatížení pomocí šablony použijte článek věnovaný [vytvoření dvou virtuálních počítačů v nástroji pro vyrovnávání zatížení a konfiguraci pravidel NAT v tomto prostředí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer). |

## <a name="vms"></a>Virtuální počítače

Virtuální počítače je možné vytvořit ve stejné virtuální síti. Ke vzájemnému propojení můžou využít privátní IP adresy. Propojit se můžou v případě, že jsou v různých podsítích, a není přitom potřeba konfigurovat bránu nebo využívat veřejné IP adresy. Pokud chcete umístit virtuální počítač do virtuální sítě, vytvořte virtuální síť a potom jednotlivé virtuální počítače při jejich vytváření přiřazujte do této virtuální sítě a požadované podsítě. Virtuální počítače získají svoje síťové nastavení sítě během nasazení nebo spuštění.  

IP adresa se virtuálním počítačům přiřazuje v okamžiku jejich nasazení. Pokud do virtuální sítě nebo podsítě nasadíte několik virtuálních počítačů, IP adresy se jim přiřazují při spouštění. K virtuálnímu počítači můžete také přidělit statickou IP adresu. Pokud přidělíte statickou IP adresu, měli byste zvážit použití konkrétní podsítě, abyste se vyhnuli nechtěnému opakovanému použití statické IP adresy pro jiný virtuální počítač.  

Pokud vytvoříte virtuální počítač a chcete ho později migrovat do virtuální sítě, nejde o jednoduchou změnu konfigurace. Virtuální počítač je v takovém případě potřeba do virtuální sítě znovu nasadit. Nejjednodušší způsob, jak znovu nasadit virtuální počítač, je odstranit tento počítač, ale ne disky, které jsou k němu připojené, a potom ho znovu vytvořit ve virtuální síti s využitím původních disků. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuálního počítač ve virtuální síti.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md) | Využívá výchozí síťové nastavení, které bylo popsané dřív, k vytvoření virtuálního počítače s jedním síťovým rozhraním. K vytvoření virtuálního počítače s několika síťovými rozhraními musíte použít jinou metodu. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Zahrnuje použití [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) k přidání síťového rozhraní, které jste předtím vytvořili do konfigurace virtuálního počítače. |
| [Azure CLI](../articles/virtual-machines/linux/create-cli-complete.md) | Vytvořte virtuální počítač a připojte ho k virtuální síti, podsíti a síťovému adaptéru, který sestaví jako jednotlivé kroky. |
| [Šablona](../articles/virtual-machines/windows/ps-template.md) | Jako vodítko při nasazování virtuálního počítače pomocí šablony použijte článek věnovaný [velmi jednoduchému nasazení virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows). |

## <a name="next-steps"></a>Další kroky
Kroky specifické pro virtuální počítače týkající se správy virtuálních sítí Azure pro virtuální počítače najdete v kurzech pro [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) nebo [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md) .

K dispozici jsou také kurzy pro vyrovnávání zatížení virtuálních počítačů a vytváření vysoce dostupných aplikací pro [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) nebo [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md).

- Naučte se konfigurovat [trasy definované uživatelem a předávání IP](../articles/virtual-network/virtual-networks-udr-overview.md). 
- Naučte se konfigurovat [připojení mezi virtuálními sítěmi](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Naučte se [řešit potíže s trasami](../articles/virtual-network/diagnose-network-routing-problem.md).
- Přečtěte si další informace o [šířce pásma sítě virtuálních počítačů](../articles/virtual-network/virtual-machine-network-throughput.md).
