---
title: 'Azure VPN Gateway: informace o zařízeních VPN pro připojení'
description: Tento článek probírá zařízení VPN a parametry protokolu IPsec pro připojení mezi různými místy pomocí VPN Gateway typu S2S. V článku najdete také odkazy na pokyny a příklady pro konfiguraci.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: 58278e241df3d9242c1cb3ad5d6e931c9c4ca701
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150918"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>O zařízeních VPN a o parametrech protokolu IPsec/IKE pro připojení typu Site-to-Site ke službě VPN Gateway

Pro konfiguraci připojení VPN typu Site-to-Site (S2S) mezi různými místy pomocí brány VPN Gateway je potřeba zařízení VPN. Připojení typu Site-to-Site lze použít k vytvoření hybridního řešení, nebo kdykoli chcete zabezpečit připojení mezi místními a virtuálními sítěmi. Tento článek obsahuje seznam ověřených zařízení VPN a seznam parametrů protokolu IPsec/IKE pro brány VPN.

> [!IMPORTANT]
> Pokud mezi místními zařízeními VPN a bránami VPN dochází k problémům s připojením, vyhledejte informace v části [Známé problémy s kompatibilitou zařízení](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Při procházení tabulek si všimněte:

* Došlo ke změně terminologie pro služby Azure VPN Gateway. Změnily se pouze názvy. Nedošlo k žádné změně funkce.
  * Statické směrování = PolicyBased
  * Dynamické směrování = RouteBased
* Specifikace pro vysokovýkonné brány VPN a brány VPN typu RouteBased jsou stejné, není-li uvedeno jinak. Například ověřená zařízení VPN, která jsou kompatibilní s bránami VPN typu RouteBased, budou kompatibilní také s vysokovýkonnou bránou VPN.

## <a name="devicetable"></a>Ověřená zařízení VPN a průvodci konfigurací zařízení

Ve spolupráci s dodavateli zařízení jsme ověřili sadu standardních zařízení VPN. Všechna zařízení v řadách zařízení v následujícím seznamu by měla fungovat s bránami VPN. V tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype) zjistíte, jaký typ sítě VPN (PolicyBased nebo RouteBased) použít pro řešení VPN Gateway, které chcete konfigurovat.

Informace o konfiguraci zařízení VPN najdete v odkazech odpovídajících příslušné rodině zařízení. Při poskytování odkazů na pokyny se snažíme maximálně vyhovět. Pro podporu zařízení VPN kontaktujte výrobce zařízení.

|**Dodavatel**          |**Řada zařízení**     |**Minimální verze operačního systému** |**Pokyny ke konfiguraci PolicyBased** |**Pokyny ke konfiguraci RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Není kompatibilní  |[Průvodce konfigurací](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |Směrovače VPN řady AR |AR-Series 5.4.7 +               | [Průvodce konfigurací](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Průvodce konfigurací](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Barracuda Networks, Inc. |Barracuda CloudGen firewall |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Průvodce konfigurací](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Průvodce konfigurací](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Security Gateway |80.10 jazyka r |[Průvodce konfigurací](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Průvodce konfigurací](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Podporuje se |[Průvodce konfigurací*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: iOS 15.1<br>RouteBased: iOS 15.2 |Podporuje se |Podporuje se |
| Cisco | CSR | RouteBased: IOS – XE 16,10 | (netestováno) | [Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased: iOS 15.0<br>RouteBased*: iOS 15.1 |Podporuje se |Podporuje se |
| Cisco |Meraki |neuvedeno |Není kompatibilní |Není kompatibilní |
| Cisco | vEdge (Viptela OS) | 18.4.0 (aktivní/pasivní režim)<br><br>19,2 (aktivní/aktivní režim) | Není kompatibilní |  [Ruční konfigurace (aktivní/pasivní)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Konfigurace cloudu OnRamp (aktivní/aktivní)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 a vyšší |[Průvodce konfigurací](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Není kompatibilní |
| F5 |Řada BIG-IP |12.0 |[Průvodce konfigurací](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Průvodce konfigurací](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (netestováno) |[Průvodce konfigurací](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone sítě | Další – obecné brány firewall (NGFW) | 5.5 verze R7  | (netestováno) | [Průvodce konfigurací](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |Řada SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Průvodce konfigurací](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Není kompatibilní |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Podporuje se |[Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |Řada J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Podporuje se |[Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Podporuje se |[Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Podporuje se |[Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12. x|Podporuje se |[Konfigurační skript](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Služba Směrování a vzdálený přístup |Windows Server 2012 |Není kompatibilní |Podporuje se |
| Open Systems AG |Mission Control Security Gateway |neuvedeno |[Průvodce konfigurací](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Není kompatibilní |
| Palo Alto Networks |Všechna zařízení se systémem PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 nebo novější<br>RouteBased: 7.1.4 |Podporuje se |[Průvodce konfigurací](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (vývojář) | VyOS | VyOS 1.2.2 | (netestováno) | [Průvodce konfigurací](https://vyos.readthedocs.io/en/latest/appendix/examples/azure-vpn-bgp.html)|
| ShareTech | UTM příští generace (řada NU) | 9.0.1.3 | Není kompatibilní | [Průvodce konfigurací](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Řada TZ, řada NSA<br>Řada SuperMassive<br>Řada E-Class NSA |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Není kompatibilní |[Průvodce konfigurací](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Next Gen Firewall | XG v17 | (netestováno) | [Průvodce konfigurací](https://community.sophos.com/kb/127546)<br><br>[Průvodce konfigurací – vícenásobné SAs](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM 1.1.5/VpnPlusServer-1.2.0 | (netestováno) | [Průvodce konfigurací](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v 1.10 | (netestováno) | [BGP přes IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI přes IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347)
| WatchGuard |Všechny |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Průvodce konfigurací](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Průvodce konfigurací](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG Series<br>Série ATP ZyWALL<br>ZyWALL Series VPN | ZLD v 4.32 + | (netestováno) | [VTI přes IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP přes IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) Cisco ASA verze 8.4+ přidávají podporu IKEv2 a umožňují připojení ke službě Azure VPN Gateway pomocí vlastních zásad IPsec/IKE s možností UsePolicyBasedTrafficSelectors. Další informace najdete v tomto [článku s postupem](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (**) Směrovače řady ISR 7200 podporují jenom sítě VPN typu PolicyBased.

## <a name="configscripts"></a>Stažení skriptů konfigurace zařízení VPN z Azure

Pro určitá zařízení můžete konfigurační skripty stáhnout přímo z Azure. Další informace a pokyny ke stažení najdete v tématu [stažení skriptů pro konfiguraci zařízení VPN](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Zařízení s dostupnými konfiguračními skripty

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="additionaldevices"></a>Neověřená zařízení VPN

Nevidíte-li své zařízení v tabulce Ověřená zařízení VPN, stále je možné, že bude fungovat s připojením typu Site-to-Site. Kvůli další podpoře a pokynům ke konfiguraci se obraťte na výrobce zařízení.

## <a name="editing"></a>Ukázky úpravy konfigurace zařízení

Po stažení ukázky konfigurace zařízení VPN budete muset nahradit některé hodnoty tak, aby odpovídaly nastavení vašeho prostředí.

### <a name="to-edit-a-sample"></a>Chcete-li upravit ukázku:

1. Otevřete ukázku pomocí Poznámkového bloku.
2. Vyhledejte všechny řetězce *text* a nahraďte je hodnotami, které odpovídají vašemu prostředí. Nezapomeňte zahrnout < a >. Zadané názvy by měly být jedinečné. Pokud příkaz nefunguje, obraťte se na dokumentaci výrobce zařízení.

| **Text v ukázce** | **Změňte na** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Zvolený název pro tento objekt. Příklad: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |Zvolený název pro tento objekt. Příklad: myAzureNetwork |
| &lt;RP_AccessList&gt; |Zvolený název pro tento objekt. Příklad: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |Zvolený název pro tento objekt. Příklad: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Zvolený název pro tento objekt. Příklad: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Zadejte rozsah. Příklad: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Zadejte masku podsítě. Příklad: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Zadejte místní rozsah. Příklad: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Zadejte masku místní podsítě. Příklad: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Tato informace je specifická pro vaši virtuální síť a najdete ji v Portálu pro správu jako **IP adresa brány**. |
| &lt;SP_PresharedKey&gt; |Tato informace je specifická pro vaši virtuální síť a najdete ji v Portálu pro správu jako Správa klíče. |

## <a name="ipsec"></a>Parametry protokolu IPsec/IKE

> [!IMPORTANT]
> 1. Následující tabulky obsahují kombinaci algoritmů a parametrů, které služba Azure VPN Gateway používá ve výchozí konfiguraci. Pro brány sítě VPN založené na trasách a vytvořené pomocí modelu nasazení správy prostředků Azure můžete zadat vlastní zásadu pro každé jednotlivé připojení. Podrobné pokyny najdete v tématu [Konfigurace zásad IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md).
>
> 2. Kromě toho musíte uchytit **MSS** protokolu TCP na **1350**. Pokud vaše zařízení nepodporuje uchycení MSS, můžete místo toho nastavit **MTU** na rozhraní tunelu na **1400** bajtů.
>

V následujících tabulkách:

* SA je přidružení zabezpečení.
* IKE fáze 1 se také nazývá „hlavní režim“.
* IKE fáze 2 se také nazývá „rychlý režim“.

### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokolu IKE fáze 1 (hlavní režim)

| **Vlastnost**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Verze IKE           |IKEv1              |IKEv1 a IKEv2    |
| Skupina Diffie-Hellman  |Skupina 2 (1 024 bitů) |Skupina 2 (1 024 bitů) |
| Metoda ověřování |Předsdílený klíč     |Předsdílený klíč     |
| Algoritmy šifrování a hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Životnost SA           |28 800 sekund     |28 800 sekund     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokolu IKE fáze 2 (rychlý režim)

| **Vlastnost**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Verze IKE                   |IKEv1          |IKEv1 a IKEv2                              |
| Algoritmy šifrování a hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Nabídky RouteBased QM SA](#RouteBasedOffers) |
| Životnost SA (čas)            |3 600 sekund  |27 000 sekund                               |
| Životnost SA (bajty)           |102 400 000 kB | -                                           |
| Metoda Perfect Forward Secrecy (PFS) |Ne             |[Nabídky RouteBased QM SA](#RouteBasedOffers) |
| Detekce mrtvých partnerských zařízení (DPD)     |Nepodporováno  |Podporuje se                                    |


### <a name ="RouteBasedOffers"></a>Nabídky RouteBased VPN IPsec Security Association (rychlý režim IKE SA)

Následující tabulka uvádí nabídky IPsec SA (rychlý režim IKE). Nabídky jsou uvedeny v pořadí podle preference jejich předávání nebo přijímání.

#### <a name="azure-gateway-as-initiator"></a>Služba Azure Gateway jako iniciátor

|-  |**Šifrování**|**Ověřování**|**Skupina PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Žádný         |
| 2 |AES256        |SHA1              |Žádný         |
| 3 |3DES          |SHA1              |Žádný         |
| 4 |AES256        |SHA256            |Žádný         |
| 5 |AES128        |SHA1              |Žádný         |
| 6 |3DES          |SHA256            |Žádný         |

#### <a name="azure-gateway-as-responder"></a>Služba Azure Gateway jako respondér

|-  |**Šifrování**|**Ověřování**|**Skupina PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Žádný         |
| 2 |AES256        |SHA1              |Žádný         |
| 3 |3DES          |SHA1              |Žádný         |
| 4 |AES256        |SHA256            |Žádný         |
| 5 |AES128        |SHA1              |Žádný         |
| 6 |3DES          |SHA256            |Žádný         |
| 7 |DES           |SHA1              |Žádný         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Žádný         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* U vysokovýkonných bran VPN a bran VPN typu RouteBased můžete zadat šifrování protokolem IPsec s prázdným ESP. Prázdné šifrování neposkytuje ochranu přenášených dat a mělo by se používat pouze pokud je vyžadována maximální propustnost a minimální latence. Klienti toho mohou využít ve scénářích komunikace typu VNet-to-VNet nebo pokud k šifrování dochází jinde v rámci řešení.
* Pro připojení mezi různými místy prostřednictvím Internetu použijte výchozí nastavení služby Azure VPN Gateway s šifrováním a algoritmy hash uvedenými v tabulkách výše, abyste zajistili bezpečnost důležité komunikace.

## <a name="known"></a>Známé problémy s kompatibilitou zařízení

> [!IMPORTANT]
> Jsou známy problémy s kompatibilitou mezi zařízeními VPN třetích stran a bránami VPN Azure. Tým Azure aktivně spolupracuje s dodavateli na řešení problémů, které jsou zde uvedeny. Po vyřešení problémů bude tato stránka aktualizována, aby obsahovala nejnovější informace. Pravidelně se sem vracejte.
>
>

### <a name="feb-16-2017"></a>16. února 2017

**Zařízení Palo Alto Networks s verzí dřívější než 7.1.4** pro síť VPN Azure založenou na trasách: Pokud používáte zařízení VPN z Palo Alto Networks s verzí PAN-OS dřívější než 7.1.4 a dochází k problémům s připojením k bránám sítě VPN Azure založené na směrování, proveďte následující kroky:

1. Zkontrolujte verzi firmwaru zařízení Palo Alto Networks. Pokud je verze PAN-OS starší než 7.1.4, proveďte upgrade na verzi 7.1.4.
2. Na zařízení Palo Alto Networks změňte při připojování k bráně VPN Azure životnost přidružení zabezpečení (SA) Fáze 2 (nebo přidružení zabezpečení rychlého režimu) na 28 800 sekund (8 hodin).
3. Pokud i nadále dochází k problému s připojením, otevřete žádost o podporu na webu Azure Portal.
