---
title: Přehled značek služeb Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o značkách služby. Značky služeb pomůžou minimalizovat složitost vytváření pravidel zabezpečení.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 26906f2a7343dbaf09f3107d2598e81a42c65091
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064644"
---
# <a name="virtual-network-service-tags"></a>Značky služby virtuální sítě 
<a name="network-service-tags"></a>

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. 

Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) nebo [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například **ApiManagement**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo zamítnout provoz pro odpovídající službu. 

Pomocí značek služeb můžete zajistit izolaci sítě a chránit prostředky Azure před obecným internetem při přístupu ke službám Azure, které mají veřejné koncové body. Vytvořte pravidla skupiny zabezpečení příchozích a odchozích sítí pro zamítnutí provozu do a z **Internetu** a umožněte přenos do/z **AzureCloud** nebo jiných [dostupných značek služeb](#available-service-tags) pro konkrétní služby Azure. 

## <a name="available-service-tags"></a>Dostupné značky služeb
Následující tabulka obsahuje všechny značky služeb, které jsou k dispozici pro použití v pravidlech [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Sloupce označují, zda značka:

- Je vhodný pro pravidla, která se vztahují na příchozí nebo odchozí provoz.
- Podporuje [regionální](https://azure.microsoft.com/regions) rozsah.
- Je použitelný v pravidlech [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags) .

Ve výchozím nastavení značky služby odráží rozsahy celého cloudu. Některé značky služby také umožňují podrobnější kontrolu tím, že omezují odpovídající rozsahy IP adres na určitou oblast. Například **úložiště** značek služby představuje Azure Storage pro celý Cloud, ale **Storage. WestUS** zúží rozsah jenom na rozsahy IP adres úložiště z oblasti WestUS. V následující tabulce je uvedeno, zda každá značka služby podporuje tento regionální rozsah.  

| Značka | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Provoz správy pro nasazení ve službě Azure API Management vyhrazena. | Obojí | Ne | Ano |
| **ApplicationInsightsAvailability** | Application Insights dostupnost. | Příchozí | Ne | Ne |
| **AppService**    | Azure App Service. Tato značka se doporučuje pro odchozí pravidla zabezpečení na front-endy webové aplikace. | Odchozí | Ano | Ano |
| **AppServiceManagement** | Provoz správy pro nasazení vyhrazená pro App Service Environment. | Obojí | Ne | Ano |
| **Azureactivedirectory selhala** | Azure Active Directory. | Odchozí | Ne | Ano |
| **AzureActiveDirectoryDomainServices** | Provoz správy pro nasazení vyhrazená pro Azure Active Directory Domain Services. | Obojí | Ne | Ano |
| **AzureAdvancedThreatProtection** | Rozšířená ochrana před internetovými útoky Azure. | Odchozí | Ne | Ne |
| **AzureBackup** |Azure Backup.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **úložiště** a **azureactivedirectory selhala** . | Odchozí | Ne | Ano |
| **AzureBotService** | Azure Bot Service. | Odchozí | Ne | Ne |
| **AzureCloud** | Všechny [veřejné IP adresy Datacenter](https://www.microsoft.com/download/details.aspx?id=56519) | Odchozí | Ano | Ano |
| **AzureCognitiveSearch** | Azure Kognitivní hledání (Pokud používáte indexery s dovednosti). | Obojí | Ne | Ne |
| **AzureConnectors** | Konektory Azure Logic Apps pro připojení sondy/back-end. | Příchozí | Ano | Ano |
| **AzureContainerRegistry** | Azure Container Registry. | Odchozí | Ano | Ano |
| **AzureCosmosDB** | Azure Cosmos DB. | Odchozí | Ano | Ano |
| **AzureDatabricks** | Azure Databricks. | Obojí | Ne | Ne |
| **AzureDataExplorerManagement** | Správa Azure Průzkumník dat. | Příchozí | Ne | Ne |
| **AzureDataLake** | Azure Data Lake. | Odchozí | Ne | Ano |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Poznámka:* Tato značka se zabývá Azure Event Gridmi koncovými body v USA (střed) – jih, USA – východ, USA – východ 2, USA – západ 2 a USA – střed. | Obojí | Ne | Ne |
| **AzureFrontDoor** | Přední dveře Azure. | Obojí | Ne | Ne |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **azureactivedirectory selhala** a **AzureFrontDoor. front-endu** . Seznamte se prosím i po následujících IP adresách (Tato závislost bude brzy odebrána): 13.107.6.181 & 13.107.9.181. | Odchozí | Ne | Ne |
| **AzureIoTHub** | IoT Hub Azure. | Odchozí | Ne | Ne |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Poznámka:* Tato značka má závislost na značce **azureactivedirectory selhala** . | Odchozí | Ano | Ano |
| **AzureLoadBalancer** | Nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na [virtuální IP adresu hostitele](security-overview.md#azure-platform-considerations) (168.63.129.16), ve které vznikly sondy stavu Azure. Nezahrnuje provoz do vašeho prostředku Azure Load Balancer. Pokud nepoužíváte Azure Load Balancer, můžete toto pravidlo přepsat. | Obojí | Ne | Ne |
| **AzureMachineLearning** | Azure Machine Learning. | Obojí | Ne | Ano |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon a vlastní metriky (koncové body 4Gigový).<br/><br/>*Poznámka:* U Log Analytics má tato značka závislost na značce **úložiště** . | Odchozí | Ne | Ano |
| **AzurePlatformDNS** | Služba DNS základní infrastruktury (výchozí).<br/><br>Pomocí této značky můžete zakázat výchozí DNS. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | Ne | Ne |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), což je základní služba infrastruktury.<br/><br/>Pomocí této značky můžete zakázat výchozí IMDS. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | Ne | Ne |
| **AzurePlatformLKM** | Licencování Windows nebo služba správy klíčů.<br/><br/>Pomocí této značky můžete zakázat výchozí hodnoty licencování. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | Ne | Ne |
| **AzureResourceManager** | Azure Resource Manager. | Odchozí | Ne | Ne |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **úložiště**, **azureactivedirectory selhala**a **EventHub** . | Odchozí | Ne | Ne |
| **AzureTrafficManager** | IP adresy služby Azure Traffic Manager PROBE.<br/><br/>Další informace o Traffic Manager IP adres sondy najdete v tématu [Nejčastější dotazy k Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Příchozí | Ne | Ano |  
| **BatchNodeManagement** | Provoz správy pro nasazení vyhrazená pro Azure Batch. | Obojí | Ne | Ano |
| **CognitiveServicesManagement** | Rozsah adres pro přenos pro Azure Cognitive Services. | Odchozí | Ne | Ne |
| **Dynamics365ForMarketingEmail** | Rozsah adres pro marketingovou e-mailovou službu Dynamics 365. | Odchozí | Ano | Ne |
| **ElasticAFD** | Elastické přední dveře Azure. | Obojí | Ne | Ne |
| **EventHub** | Event Hubs Azure. | Odchozí | Ano | Ano |
| **GatewayManager** | Provoz správy pro nasazení vyhrazená pro Azure VPN Gateway a Application Gateway. | Příchozí | Ne | Ne |
| **GuestAndHybridManagement** | Azure Automation a konfigurace hostů. | Odchozí | Ne | Ano |
| **HDInsight** | Azure HDInsight. | Příchozí | Ano | Ne |
| **Internet** | Adresní prostor IP adres, který je mimo virtuální síť a dosažitelný veřejným internetem.<br/><br/>Rozsah adres zahrnuje [veřejný adresní prostor IP adres ve vlastnictví Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obojí | Ne | Ne |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Odchozí | Ne | Ne |
| **MicrosoftContainerRegistry** | Registr kontejnerů pro Image Microsoft Container. <br/><br/>*Poznámka:* Seznam povolených IP adres (Tato závislost bude brzy odebrána): 204.79.197.219. | Odchozí | Ano | Ano |
| **ServiceBus** | Azure Service Bus provoz, který využívá úroveň služby Premium. | Odchozí | Ano | Ano |
| **ServiceFabric** | Service Fabric Azure.<br/><br/>*Poznámka:* Tato značka představuje koncový bod služby Service Fabric pro rovinu ovládacího prvku na oblast. To zákazníkům umožňuje provádět operace správy pro své Service Fabric clustery ze své virtuální sítě (koncové body). https://westus.servicefabric.azure.com) | Obojí | Ne | Ne |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL a Azure SQL Data Warehouse.<br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL. | Odchozí | Ano | Ano |
| **SqlManagement** | Provoz správy pro nasazení vyhrazená SQL. | Obojí | Ne | Ano |
| **Úložiště** | Azure Storage. <br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage. | Odchozí | Ano | Ano |
| **VirtualNetwork** | Adresní prostor virtuální sítě (všechny rozsahy IP adres definované pro virtuální síť), všechny připojené místní adresní prostory, [partnerské](virtual-network-peering-overview.md) virtuální sítě, virtuální sítě připojené k [bráně virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [virtuální IP adresu hostitele](security-overview.md#azure-platform-considerations)a předpony adres používané na [trasách definovaných uživatelem](virtual-networks-udr-overview.md). Tato značka může obsahovat také výchozí trasy. | Obojí | Ne | Ne |

>[!NOTE]
>V modelu nasazení Classic (před Azure Resource Manager) je podporována podmnožina značek uvedená v předchozí tabulce. Tyto značky jsou napsané jinak:
>
>| Klasický pravopis | Ekvivalentní značka Správce prostředků |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Značky služeb Azure označují předpony adres z konkrétního cloudu, který se používá. Například základní rozsahy IP adres, které odpovídají hodnotě značky **SQL** ve veřejném cloudu Azure, se budou lišit od základních rozsahů v cloudu Azure Čína.

> [!NOTE]
> Pokud implementujete [koncový bod služby pro virtuální síť](virtual-network-service-endpoints-overview.md) pro službu, jako je Azure Storage nebo Azure SQL Database, Azure pro tuto službu přidá [trasu](virtual-networks-udr-overview.md#optional-default-routes) k podsíti virtuální sítě. Předpony adresy v trase jsou stejné předpony adres nebo rozsahy CIDR jako u odpovídající značky služby.

## <a name="service-tags-on-premises"></a>Místní značky služby  
Aktuální informace o značce služby a rozsahu, které se mají zahrnout, můžete získat jako součást místních konfigurací brány firewall. Tyto informace představují aktuální časový Seznam rozsahů IP adres, které odpovídají jednotlivým značkám služby. Informace můžete získat prostřednictvím kódu programu nebo stažením souboru JSON, jak je popsáno v následujících částech.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Použití rozhraní API zjišťování značek služeb (Public Preview)
Můžete programově načíst aktuální seznam značek služeb společně s podrobnostmi o rozsahu IP adres:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> I když je ve verzi Public Preview, rozhraní API pro zjišťování může vracet informace, které jsou méně aktuální než informace vrácené stahováním JSON. (Další informace najdete v další části.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Zjišťování značek služeb pomocí souborů JSON ke stažení 
Můžete stáhnout soubory JSON, které obsahují aktuální seznam značek služeb společně s podrobnostmi o rozsahu IP adres. Tyto seznamy se aktualizují a publikují týdně. Umístění pro každý Cloud:

- [Veřejné Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Německo](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Podmnožina těchto informací byla publikována v souborech XML pro [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Čína](https://www.microsoft.com/download/details.aspx?id=42064)a [Azure Německo](https://www.microsoft.com/download/details.aspx?id=54770). Tyto soubory XML budou zastaralé od 30. června 2020 a po tomto datu již nebudou k dispozici. Měli byste migrovat na použití rozhraní Discovery API nebo souboru JSON ke stažení, jak je popsáno v předchozích částech.

### <a name="tips"></a>Tipy 
- Můžete zjišťovat aktualizace z jedné publikace na další tím, že zaznamenáte zvýšené hodnoty *changeNumber* v souboru JSON. Každý pododdíl (například **Storage. WestUS**) má svůj vlastní *changeNumber* , který se zvýší, protože dojde ke změnám. Nejvyšší úroveň *changeNumber* souboru se zvýší, když se změní některá z dílčích sekcí.
- Příklady, jak analyzovat informace o značce služby (například získat všechny rozsahy adres pro úložiště v WestUS), najdete v dokumentaci k [rozhraní API zjišťování značek služby](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Další kroky
- Naučte se [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
