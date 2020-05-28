---
title: Virtuální síť pro služby Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o výhodách nasazení prostředků do virtuální sítě. Prostředky ve virtuálních sítích mohou komunikovat mezi sebou a místními prostředky, aniž by bylo nutné přecházet přes Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 5b9d18d47c1430023dc9d892ce25389810690a4d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021632"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Nasazení vyhrazených služeb Azure do virtuálních sítí

Když nasadíte vyhrazené služby Azure ve [virtuální síti](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě prostřednictvím privátních IP adres.

![Služby nasazené ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služeb v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky ve virtuální síti můžou vzájemně komunikovat soukromě prostřednictvím privátních IP adres. Například přímý přenos dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres přes [síť VPN typu Site-to-Site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- U virtuálních sítí je možné navázat [partnerský vztah](virtual-network-peering-overview.md) , aby bylo možné mezi sebou vzájemně komunikovat prostředky pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou většinou plně spravovány službou Azure. To zahrnuje monitorování stavu prostředků a škálování s využitím zátěže.
- Instance služby jsou nasazeny do podsítě ve virtuální síti. Příchozí a odchozí síťový přístup pro podsíť musí být otevřený prostřednictvím [skupin zabezpečení sítě](security-overview.md#network-security-groups)podle pokynů poskytovaných službou.
- Některé služby také ukládají omezení pro podsíť, ve které jsou nasazená, a omezují použití zásad, trasy nebo kombinování virtuálních počítačů a prostředků služeb ve stejné podsíti. U každé služby se můžete podívat na konkrétní omezení, která se v průběhu času můžou měnit. Příklady takových služeb jsou Azure NetApp Files, vyhrazené HSM, Azure Container Instances App Service. 
- V případě potřeby můžou služby vyžadovat [delegovanou podsíť](virtual-network-manage-subnet.md#add-a-subnet) jako explicitní identifikátor, který podsíť může hostovat určitou službu. Delegováním služeb získají explicitní oprávnění k vytváření prostředků specifických pro službu v delegované podsíti.
- Podívejte se na příklad odpovědi REST API ve [virtuální síti s delegovanou podsítí](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). K dispozici je úplný seznam služeb, které používají delegovaný model podsítě, [k dispozici](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) prostřednictvím rozhraní API pro delegování.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

|Kategorie|Služba| Vyhrazená podsíť<sup>1</sup>SUP>1 </sup>
|-|-|-|
| Compute | Virtuální počítače: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloudová služba](https://msdn.microsoft.com/library/azure/jj156091): jenom virtuální síť (Classic)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Ne <br/> Ne <br/> Ne <br/> Žádné<sup>2</sup>SUP>2</sup>
| Síť | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Síťová virtuální zařízení](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ano <br/> Ano <br/> Ano <br/> Ne
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ano <br/> Ano <br/> 
|Analýzy | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Žádné<sup>2</sup>SUP>2</Su<sup>2</sup>> <br/> Ne<sup>2</sup> <br/> 
| Identita | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ne <br/>
| Kontejnery | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>Modul [Azure Container Service](https://github.com/Azure/acs-engine) s [modulem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) Azure Virtual Network CNI<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Žádné<sup>2</sup>SUP>2</sup><br/> Ano <br/><br/> Ne <br/> Ano
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/> Ano <br/> Ano
| Hostováno | [Rezervované HSM Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/>
| | |

<sup>1</sup> "vyhrazené" znamená, že v této podsíti můžou být nasazené jenom prostředky specifické pro službu a nejde je kombinovat s virtuálním počítačem/VMSSs zákazníka. <br/> 
<sup>2</sup> doporučuje se mít tyto služby ve vyhrazené podsíti, ale nejedná se o povinný požadavek, který služba ukládá.
