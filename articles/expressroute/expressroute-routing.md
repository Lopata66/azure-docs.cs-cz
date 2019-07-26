---
title: 'Požadavky směrování – ExpressRoute: Azure | Microsoft Docs'
description: Tato stránka obsahuje podrobné požadavky pro konfiguraci a správu směrování pro okruhy ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 458808f9d2c496ae4c29b05bd8a3531b94ba78c0
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422684"
---
# <a name="expressroute-routing-requirements"></a>Požadavky na směrování služby ExpressRoute
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, budete muset nastavit a spravovat směrování. Někteří poskytovatelé připojení nabízejí nastavení a správu směrování jako spravovanou službu. Zeptejte se svého poskytovatele připojení, jestli tuto službu nabízí. Pokud ne, je nutné splnit následující požadavky:

Přečtěte si článek [Okruhy a domény směrování](expressroute-circuit-peerings.md), který popisuje relace směrování, které musí být nastaveny k usnadnění připojení.

> [!NOTE]
> Microsoft nepodporuje žádné protokoly redundance směrovačů (např. HSRP nebo VRRP) pro konfigurace s vysokou dostupností. V případě vysoké dostupnosti spoléháme na redundantní dvojici relací protokolu BGP na partnerský vztah.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>IP adresy sloužící pro partnerské vztahy
Je nutné rezervovat několik bloků IP adres, abyste nakonfigurovali směrování mezi vaší sítí a směrovači MSEE (Microsoft Enterprise Edge). Tato část poskytuje seznam požadavků a popisuje pravidla týkající se získávání a použití těchto IP adres.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP adresy sloužící pro soukromý partnerský vztah Azure
Ke konfiguraci partnerských vztahů můžete použít buď soukromé IP adresy, nebo veřejné IP adresy. Rozsah adres použitý ke konfiguraci tras se nesmí překrývat s rozsahy adres použitými k vytvoření virtuálních sítí v Azure. 

* Pro rozhraní směrování musíte rezervovat podsíť /29 nebo dvě podsítě /30.
* Podsítě pro směrování mohou obsahovat buď soukromé IP adresy, nebo veřejné IP adresy.
* Podsítě nesmí být v konfliktu s rozsahem vyhrazeným zákazníkem pro použití v cloudu Microsoftu.
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30. 
  * První podsíť /30 se používá pro primární propojení a druhá podsíť /30 se používá pro sekundární propojení.
  * Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.  

#### <a name="example-for-private-peering"></a>Příklad soukromého partnerského vztahu
Pokud k nastavení partnerského vztahu zvolíte a.b.c.d/29, rozdělí se do dvou podsítí /30. V následujícím příkladu Všimněte si, jak se podsíť a.b.c.d/29 používá:

* a.b.c.d/29 se rozdělí na a.b.c.d/30 a a.b.c.d+4/30 a předá se Microsoftu prostřednictvím rozhraní API pro zřizování.
  * a.b.c.d+1 použijete jako IP adresu VRF pro primární PE a Microsoft využije a.b.c.d+2 jako IP adresu VRF pro primární MSEE.
  * a.b.c.d+5 použijete jako IP adresu VRF pro sekundární PE a Microsoft použije a.b.c.d+6 jako IP adresu VRF pro sekundární MSEE.

Představte si případ, kdy k nastavení soukromého partnerského vztahu vyberete 192.168.100.128/29. 192.168.100.128/29 obsahuje adresy od 192.168.100.128 do 192.168.100.135, kde:

* 192.168.100.128/30 se přiřadí pro link1, kde poskytovatel použije 192.168.100.129 a Microsoft použije 192.168.100.130.
* 192.168.100.132/30 se přiřadí pro link2, kde poskytovatel použije 192.168.100.133 a Microsoft použije 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>IP adresy používané pro partnerský vztah Microsoftu
Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IPv4 adres v registrech RIR a IRR.

* IP adresy uvedené na portálu jako inzerované veřejné předpony pro partnerský vztah Microsoftu vytvoří pro hlavní směrovače Microsoftu seznamy ACL, které povolí příchozí provoz z těchto IP adres. 
* K nastavení partnerského vztahu BGP pro každý partnerský vztah pro každý okruh ExpressRoute (pokud jich používáte víc než jeden) musíte použít jedinečnou podsíť /29 (protokol IPv4) nebo /125 (protokol IPv6) nebo dvě podsítě /30 (protokol IPv4) nebo /126 (protokol IPv6).
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30.
* První podsíť /30 se použije pro primární propojení a druhá podsíť /30 se použije pro sekundární propojení.
* Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
* Pokud se použije podsíť /125, rozdělí se na dvě podsítě /126.
* První podsíť /126 se použije pro primární propojení a druhá podsíť /126 se použije pro sekundární propojení.
* Pro každou z těchto podsítí /126 musíte ve směrovači použít první IP adresu podsítě /126. Microsoft používá druhou IP adresu podsítě /126 k nastavení relace protokolu BGP.
* Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

### <a name="ip-addresses-used-for-azure-public-peering"></a>IP adresy používané pro veřejný partnerský vztah Azure

> [!NOTE]
> Veřejný partnerský vztah Azure není není k dispozici pro nové okruhy.
> 

Pro nastavení relací protokolu BGP musíte použít veřejné IP adresy, které vlastníte. Microsoft musí být schopný ověřit vlastnictví IPv4 adres v registrech RIR a IRR. 

* K nastavení partnerského vztahu BGP pro každý partnerský vztah pro každý okruh ExpressRoute (pokud jich používáte víc než jeden) musíte použít jedinečnou podsíť /29 nebo dvě podsítě /30. 
* Pokud se použije podsíť /29, rozdělí se na dvě podsítě /30. 
  * První podsíť /30 se používá pro primární propojení a druhá podsíť /30 se používá pro sekundární propojení.
  * Pro každou z těchto podsítí /30 musíte ve směrovači použít první IP adresu podsítě /30. Microsoft používá druhou IP adresu podsítě /30 k nastavení relace protokolu BGP.
  * Musíte nastavit obě relace protokolu BGP, aby naše [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/) byla platná.

## <a name="public-ip-address-requirement"></a>Požadavek veřejné IP adresy

### <a name="private-peering"></a>Soukromý partnerský vztah
Pro soukromý partnerský vztah si můžete zvolit použití veřejných nebo privátních IPv4 adres. Poskytujeme kompletní izolaci provozu, takže v případě soukromého partnerského vztahu není možné překrývání adres s jinými zákazníky. Tyto adresy nejsou inzerované na internetu. 

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu
Cesta partnerského vztahu Microsoftu vám umožní připojit se ke cloudovým službám Microsoftu. Seznam služeb zahrnuje služby Office 365, jako je Exchange Online, SharePoint Online, Skype pro firmy, Microsoft teams a Dynamics 365. Microsoft v partnerském vztahu Microsoftu podporuje obousměrné připojení. Přenosy směřující do cloudových služeb Microsoftu musí před vstupem do služby MSN používat platné veřejné IPv4 adresy.

Ujistěte se, že vaše IP adresa a číslo AS jsou registrované na vás v jednom z následujících registrů:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Pokud vám ve výše uvedených registrech nejsou přiřazeny vaše předpony a číslo AS, musíte otevřít případ podpory pro ruční ověření předpon a čísla ASN. Podpora vyžaduje dokumentaci, například zplnomocnění, která prokáže vaše oprávnění používat příslušné prostředky.

U partnerského vztahu Microsoftu je povoleno soukromé číslo AS, které ale také vyžaduje ruční ověření. Kromě toho v AS PATH odebereme soukromá čísla AS pro přijaté předpony. V důsledku toho nebudete moct připojit soukromá čísla AS k AS PATH, abyste [ovlivnili směrování pro partnerský vztah Microsoftu](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Neprovádějí stejnou veřejnou IP trasy do veřejného Internetu a přes ExpressRoute. Aby se snížilo riziko nesprávné konfigurace, která způsobuje asymetrické směrování, důrazně doporučujeme, aby [IP adresy NAT](expressroute-nat.md) inzerované Microsoftu přes ExpressRoute byly z rozsahu, který není inzerovaný pro Internet vůbec. Pokud to není možné dosáhnout, je nutné zajistit, abyste inzerovali konkrétnější rozsah přes ExpressRoute, než je ten v připojení k Internetu. Kromě veřejné trasy pro překlad adres, můžete také inzerovat přes ExpressRoute veřejné IP adresy používané servery ve vaší místní síti, které komunikují s koncovými body služeb Office 365 v rámci Microsoftu. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Veřejný partnerský vztah (zastaralé – není k dispozici pro nové okruhy)
Cesta veřejného partnerského vztahu Azure vám umožňuje připojení ke všem službám, které jsou hostovány v Azure, přes jejich veřejné IP adresy. Sem patří služby uvedené v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md) a všechny služby hostované nezávislými dodavateli softwaru v Microsoft Azure. Připojení ke službám Microsoft Azure ve veřejném partnerském vztahu je vždycky iniciováno z vaší sítě do sítě Microsoftu. Pro přenosy směřující do služby MSN je nutné použít veřejné IP adresy.

> [!IMPORTANT]
> Všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
>   

U veřejného partnerského vztahu je povoleno soukromé číslo AS.

## <a name="dynamic-route-exchange"></a>Dynamická výměna tras
Výměna směrování bude přes protokol EBGP. Relace EBGP se vytvoří mezi směrovači MSEE a vašimi směrovači. Ověřování relací BGP není povinné. V případě potřeby lze nakonfigurovat hodnotu hash MD5. Informace o konfiguraci relací BGP najdete v tématu [Konfigurace směrování](how-to-routefilter-portal.md) a [Pracovní postupy zřizování okruhů a stavy okruhu](expressroute-workflows.md).

## <a name="autonomous-system-numbers"></a>Čísla autonomního systému
Microsoft pro veřejný partnerský vztah Azure, soukromý partnerský vztah Azure a partnerský vztah Microsoftu používá číslo AS 12076. Pro interní použití jsme vyhradili čísla ASN od 65515 do 65520. Jsou podporována 16bitová a 32bitová čísla AS.

Nejsou žádné požadavky týkající se symetrie přenosu dat. Cesty vpřed a zpět můžou procházet různými dvojicemi směrovačů. Identické trasy musí být inzerovány z obou stran napříč páry okruhů, které patří vám. Metriky tras nemusejí být identické.

## <a name="route-aggregation-and-prefix-limits"></a>Agregace tras a omezení předpon
Podporujeme až 4000 předpon, které jsou nám inzerované prostřednictvím soukromého partnerského vztahu Azure. To omezení může být zvýšeno až 10 000 předpon, pokud je povolen doplněk ExpressRoute Premium. Přijímáme až 200 předpon na každou relaci BGP pro veřejný partnerský vztah Azure a partnerský vztah Microsoftu. 

Pokud počet předpon překročí toto omezení, relace BGP se ukončí. Budeme přijímat výchozí trasy jenom na propojeních soukromého partnerského vztahu. Poskytovatel musí odfiltrovat výchozí trasy a privátní IP adresy (RFC 1918) z cest pro veřejný partnerský vztah Azure a partnerský vztah Microsoftu. 

## <a name="transit-routing-and-cross-region-routing"></a>Tranzitní směrování a směrování mezi oblastmi
Službu ExpressRoute nejde nakonfigurovat jako tranzitní směrovače. Ohledně služeb tranzitního směrování se budete muset spoléhat na svého poskytovatele připojení.

## <a name="advertising-default-routes"></a>Inzerování výchozích tras
Výchozí trasy jsou povolené jenom na relacích soukromého partnerského vztahu Azure. V takovém případě bude směrovat veškerý provoz z přidružených virtuálních sítí do své sítě. Inzerování výchozích tras do soukromého partnerského vztahu povede k tomu, že internetové trasy z Azure budou blokovány. Musíte spoléhat na vaše podnikové hraniční servery, že pro služby hostované v Azure přesměrují provoz z internetu a do něj. 

 Chcete-li povolit připojení k dalším službám Azure a službám infrastruktury, je třeba zajistit, že platí jedna z následujících položek:

* Veřejný partnerský vztah Azure povoluje směrování provozu do veřejných koncových bodů.
* Používáte uživatelsky definované směrování umožňující připojení k internetu pro každou podsíť, která připojení k internetu vyžaduje.

> [!NOTE]
> Inzerování výchozích tras poruší aktivaci licencí pro Windows a jiné virtuální počítače. Náhradní řešení najdete [zde](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx).
> 
> 

## <a name="bgp"></a>Podpora komunit protokolu BGP
Tato část obsahuje přehled použití komunit protokolu BGP se službou ExpressRoute. Microsoft bude inzerovat trasy v cestách veřejného partnerského vztahu a partnerského vztahu Microsoftu s trasami, které jsou označené odpovídajícími hodnotami komunity. Důvody tohoto postupu a podrobnosti o hodnotách komunity jsou popsané dál. Microsoft ale nebude ctít žádné hodnoty komunity přiřazené trasám inzerovaným Microsoftu.

Pokud se připojujete k Microsoftu prostřednictvím ExpressRoute v libovolném umístění partnerského vztahu v rámci geopolitické oblasti, budete mít přístup ke všem cloudovým službám Microsoftu přes všechny oblasti v rámci geopolitické hranice. 

Pokud jste například připojení k Microsoftu prostřednictvím ExpressRoute v Amsterdamu, budete mít přístup ke všem cloudovým službám Microsoftu hostovaným v oblastech Severní Evropa a Západní Evropa. 

Podrobný seznam geopolitických oblastí, přidružených oblastí Azure a odpovídajících umístění partnerského vztahu ExpressRoute najdete na stránce [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).

Můžete zakoupit víc než jeden okruh ExpressRoute na geopolitickou oblast. Použití víc připojení nabízí významné výhody vysoké dostupnosti z důvodu georedundance. V případech, kdy máte víc okruhů ExpressRoute obdržíte stejnou sadu předpon inzerovaných od Microsoftu na partnerský vztah Microsoftu a cesty veřejného partnerského vztahu. To znamená, že bude mít z vaší sítě do Microsoftu víc cest. To může potenciálně v rámci vaší sítě způsobovat přijímání neoptimálních rozhodnutí o směrování. V důsledku toho se můžete u různých služeb setkat s neoptimálním průběhem připojení. Při rozhodování o směrování se na tyto hodnoty komunity můžete spoléhat, abyste nabízeli [uživatelům optimální směrování](expressroute-optimize-routing.md).

| **Oblast Microsoft Azure** | **Oblastní komunita protokolu BGP** | **Komunita protokolu BGP úložiště** | **Komunita protokolu BGP SQL** | **Cosmos DB komunita protokolu BGP** |
| --- | --- | --- | --- | --- |
| **Severní Amerika** | |
| USA – východ | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 |
| Východní USA 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 |
| USA – západ | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 |
| USA – západ 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 |
| Západní střed USA | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 |
| Střed USA – sever | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 |
| Střed USA – jih | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 |
| Střed USA | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 |
| Kanada – střed | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 |
| Kanada – východ | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 |
| **Jižní Amerika** | |
| Brazílie – jih | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 |
| **Evropa** | |
| Severní Evropa | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 |
| Západní Evropa | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 |
| Velká Británie – jih | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 |
| Spojené království – západ | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 |
| Francie – střed | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 |
| Francie – jih | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 |
| **Asie a Tichomoří** | |
| Východní Asie | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 |
| Jihovýchodní Asie | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 |
| **Japonsko** | |
| Japonsko – východ | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 |
| Japonsko – západ | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 |
| **Austrálie** | |
| Austrálie – východ | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 |
| Austrálie – jihovýchod | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 |
| **Australská vláda** | |
| Austrálie – střed | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 |
| Austrálie – střed 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 |
| **Indie** | |
| Indie – jih | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 |
| Indie – západ | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 |
| Indie – střed | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 |
| **Jižní Korea** | |
| Jižní Korea – jih | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 |
| Jižní Korea – střed | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 |
| **Jižní Afrika**| |
| Jižní Afrika – sever | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 |
| Jižní Afrika – západ | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 |
| **SPOJENÉ ARABSKÉ EMIRÁTY**| |
| Spojené arabské emiráty – sever | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 |
| Spojené arabské emiráty – střed | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 |


Všechny trasy inzerované Microsoftem budou označené odpovídající hodnotou komunity. 

> [!IMPORTANT]
> Globální předpony jsou označené odpovídající hodnotou komunity.
> 
> 

### <a name="service-to-bgp-community-value"></a>Hodnota služby pro komunitu BGP
Kromě výše uvedeného bude Microsoft také označovat předpony podle služby, ke které patří. To se týká jenom partnerského vztahu Microsoftu. Následující tabulka poskytuje mapování služby na hodnotu komunity protokolu BGP.

| **Služba** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Online Skype pro firmy | 12076:5030 |
| Dynamics 365 | 12076:5040 |
| Azure globální služby * | 12076:5050 |
| Jiné online služby Office 365 | 12076:5100 |

\* Globální služby Azure v tuto chvíli obsahují jenom Azure DevOps.


> [!NOTE]
> Microsoft nectí žádné hodnoty komunity protokolu BGP, které jste přiřadili trasám inzerovaným Microsoftu.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Podpora komunity protokolu BGP v národních cloudech

| **Oblast Azure národních cloudů**| **Hodnota komunity protokolu BGP** |
| --- | --- |
| **US Government** |  |
| USA (Gov) – Arizona | 12076:51106 |
| US Gov – Iowa | 12076:51109 |
| USA (Gov) – Virginia | 12076:51105 |
| USA (Gov) – Texas | 12076:51108 |
| US DoD – střed | 12076:51209 |
| US DoD – východ | 12076:51205 |


| **Služba v národních cloudech** | **Hodnota komunity protokolu BGP** |
| --- | --- |
| **US Government** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Online Skype pro firmy |12076:5130 |
| Dynamics 365 |12076:5140 |
| Jiné online služby Office 365 |12076:5200 |

## <a name="next-steps"></a>Další postup
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
