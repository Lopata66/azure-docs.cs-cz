---
title: Privátní odkaz – Azure Database for MySQL
description: Přečtěte si, jak soukromý odkaz funguje pro Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: f780bf946e81e9873a1828f9d697f69c81cef513
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509317"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Privátní odkaz pro Azure Database for MySQL

Privátní odkaz vám umožní připojit se k různým službám PaaS v Azure prostřednictvím privátního koncového bodu. Privátní propojení Azure v podstatě přináší služby Azure do privátního Virtual Network (VNet). K prostředkům PaaS se dá dostat pomocí privátní IP adresy stejně jako u jakéhokoli jiného prostředku ve virtuální síti.

Seznam pro PaaS služby, které podporují funkce privátního propojení, najdete v [dokumentaci](https://docs.microsoft.com/azure/private-link/index)k privátním odkazům. Privátní koncový bod je privátní IP adresa v konkrétní [virtuální](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) síti a podsíti.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně optimalizované pro Pro obecné účely a paměť.

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Data ex filtrátu v Azure Database for MySQL je, že ověřený uživatel, jako je správce databáze, může extrahovat data z jednoho systému a přesunout je do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště, jehož vlastníkem je třetí strana.

Vezměte v úvahu scénář s uživatelem, který aplikaci MySQL Workbench používá v rámci virtuálního počítače Azure, který se připojuje k serveru Azure Database for MySQL zřízenému v Západní USA. Následující příklad ukazuje, jak omezit přístup k veřejným koncovým bodům na Azure Database for MySQL pomocí řízení přístupu k síti.

* Nastavením *Povolit službám Azure* na off zakažte veškerý provoz služeb azure pro Azure Database for MySQL prostřednictvím veřejného koncového bodu. Zajistěte, aby žádné IP adresy nebo rozsahy neumožňovaly přístup k serveru prostřednictvím [pravidel brány firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) nebo [koncových bodů služby virtuální sítě](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Povolte provoz do Azure Database for MySQL jenom pomocí privátní IP adresy virtuálního počítače. Další informace najdete v článcích o [pravidlech brány firewall pro virtuální](howto-manage-vnet-using-portal.md)počítače a [služby](concepts-data-access-and-security-vnet.md) .

* Na virtuálním počítači Azure upřesněte rozsah odchozího připojení pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) a značek služeb následujícím způsobem.

    * Zadejte pravidlo NSG, které povolí provoz pro *tag Service = SQL. WestUs* – umožňuje připojení k Azure Database for MySQL jenom v západní USA
    * Zadejte pravidlo NSG (s vyšší prioritou) pro odepření provozu pro *značku služby = SQL* – odepření připojení pro aktualizaci Azure Database for MySQL ve všech oblastech.</br></br>

Na konci této instalace se virtuální počítač Azure může připojit pouze k Azure Database for MySQL v oblasti Západní USA. Připojení ale není omezené na jednu Azure Database for MySQL. Virtuální počítač se stále může připojit k libovolnému Azure Database for MySQL v Západní USA oblasti, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstraníme ji úplně.</br>

Pomocí privátního odkazu teď můžete nastavit řízení přístupu k síti, jako je skupin zabezpečení sítě, a omezit tak přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý program Insider má přístup jenom k mapovanému prostředku PaaS (například Azure Database for MySQL) a žádnému jinému prostředku.

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes soukromý partnerský vztah

Když se připojíte k veřejnému koncovému bodu z místních počítačů, vaše IP adresa musí být přidána do brány firewall založené na protokolu IP pomocí pravidla brány firewall na úrovni serveru. I když tento model funguje dobře a umožňuje přístup k jednotlivým počítačům pro vývoj nebo testování, je obtížné ho spravovat v produkčním prostředí.

Pomocí privátního odkazu můžete povolit přístup mezi různými místy k privátnímu koncovému bodu pomocí připojení typu [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), privátního partnerského vztahu nebo [tunelového připojení VPN](https://docs.microsoft.com/azure/vpn-gateway/). Můžou následně zakázat veškerý přístup prostřednictvím veřejného koncového bodu a nepoužívat bránu firewall na základě IP adresy.

> [!NOTE]
> V některých případech jsou Azure Database for MySQL a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Ujistěte se, že oba odběry mají zaregistrovaný poskytovatel prostředků **Microsoft. DBforMySQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Konfigurace privátního odkazu pro Azure Database for MySQL

### <a name="creation-process"></a>Proces vytváření

Aby bylo možné povolit privátní propojení, jsou vyžadovány privátní koncové body. To lze provést pomocí následujících průvodců.

* [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Rozhraní příkazového řádku](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Proces schválení
Po vytvoření privátního koncového bodu (PE) správcem sítě může správce MySQL spravovat připojení privátního koncového bodu (PEC) k Azure Database for MySQL. Tato oddělení povinností mezi správcem sítě a DBA je užitečné pro správu Azure Database for MySQLho připojení. 

* V Azure Portal přejděte na prostředek serveru Azure Database for MySQL. 
    * Vyberte připojení privátního koncového bodu v levém podokně.
    * Zobrazuje seznam všech připojení privátního koncového bodu (PECs).
    * Byl vytvořen odpovídající privátní koncový bod (PE).

![vybrat portál privátního koncového bodu](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Vyberte jednotlivé řadiče PEC ze seznamu tím, že je vyberete.

![vyberte privátní koncový bod, který čeká na schválení.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Správce serveru MySQL se může rozhodnout pro schválení nebo zamítnutí serveru PEC a volitelně také přidat krátkou odpověď na text.

![Vyberte zprávu privátního koncového bodu.](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po schválení nebo odmítnutí bude seznam odpovídat příslušnému stavu spolu s textem odpovědi.

![Vyberte koncový stav privátního koncového bodu.](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Případy použití privátního odkazu pro Azure Database for MySQL

Klienti se můžou připojit ke soukromým koncovým bodem ze stejné virtuální sítě, partnerské virtuální sítě ve stejné oblasti nebo prostřednictvím připojení VNet-to-VNet napříč oblastmi. Klienti se navíc mohou připojit z místního prostředí pomocí ExpressRoute, privátního partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

![Výběr privátního koncového bodu – přehled](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Připojení z virtuálního počítače Azure v partnerském Virtual Network (VNet)
Nakonfigurujte [partnerský vztah](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) virtuálních sítí pro navázání připojení k Azure Database for MySQL z virtuálního počítače Azure ve virtuální síti s partnerským vztahem.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Připojení z virtuálního počítače Azure v prostředí VNet-to-VNet
Nakonfigurujte [připojení typu VNet-to-VNet ke službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) , aby se navázalo připojení k Azure Database for MySQL z virtuálního počítače Azure v jiné oblasti nebo předplatném.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes síť VPN
Pokud chcete navázat připojení z místního prostředí k Azure Database for MySQL, vyberte a implementujte jednu z možností:

* [Připojení Point-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Připojení site-to-site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privátní propojení v kombinaci s pravidly brány firewall

Při použití privátního odkazu v kombinaci s pravidly brány firewall jsou možné následující situace a výsledky:

* Pokud žádná pravidla brány firewall nenakonfigurujete, nebude mít žádný provoz přístup k Azure Database for MySQL.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, budou se podle odpovídajícího typu pravidla brány firewall autorizovat různé typy příchozích přenosů.

* Pokud neprovedete konfiguraci žádného veřejného provozu nebo koncového bodu služby a vytvoříte privátní koncové body, je Azure Database for MySQL přístupný pouze prostřednictvím privátních koncových bodů. Pokud neprovedete konfiguraci veřejného provozu nebo koncového bodu služby, po odmítnutí nebo odstranění všech schválených privátních koncových bodů nebude mít žádný provoz přístup k Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Odepřít veřejný přístup pro Azure Database for MySQL

Pokud se chcete spoléhat jenom na soukromé koncové body pro přístup ke svým Azure Database for MySQL, můžete zakázat nastavení všech veřejných koncových bodů (tj. [pravidla brány firewall](concepts-firewall-rules.md) a [koncové body služby virtuální](concepts-data-access-and-security-vnet.md)sítě) nastavením **Odepřít konfiguraci přístupu k veřejné síti** na databázovém serveru. 

Pokud je toto nastavení nastaveno na *Ano*, budou mít Azure Database for MySQL pouze připojení prostřednictvím privátních koncových bodů. Pokud je toto nastavení nastaveno na *ne*, klienti se mohou připojit k vašemu Azure Database for MySQL v závislosti na nastaveních koncových bodů brány firewall nebo služby virtuální sítě. Po nastavení hodnoty přístup k privátní síti můžou zákazníci taky přidávat a aktualizovat existující pravidla brány firewall a pravidla koncových bodů služby virtuální sítě.

> [!Note]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL – jeden server podporuje Pro obecné účely a cenová úroveň optimalizované pro paměť.
>
> Toto nastavení nemá žádný vliv na konfigurace protokolu SSL a TLS pro vaše Azure Database for MySQL.

Informace o tom, jak nastavit **přístup k veřejné síti odepřít** pro váš Azure Database for MySQL z Azure Portal, najdete v tématu [jak nakonfigurovat přístup k veřejné síti odepřít](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Další kroky

Další informace o funkcích Azure Database for MySQL zabezpečení najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Database for MySQL, přečtěte si téma [Podpora brány firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Informace o tom, jak nakonfigurovat koncový bod služby virtuální sítě pro váš Azure Database for MySQL, najdete v tématu [Konfigurace přístupu z virtuálních sítí](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Přehled připojení Azure Database for MySQL najdete v tématu [Architektura připojení Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md