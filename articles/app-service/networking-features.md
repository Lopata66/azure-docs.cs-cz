---
title: Síťové funkce
description: Přečtěte si o funkcích sítě v Azure App Service a o tom, jaké funkce potřebujete pro vaše síťové požadavky na zabezpečení nebo funkčnost.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 87a8291387d50fae7027d007eedf49fa55b39b74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052781"
---
# <a name="app-service-networking-features"></a>Funkce App Service sítě

Aplikace v Azure App Service lze nasadit různými způsoby. Ve výchozím nastavení jsou App Service hostované aplikace přímo přístupné z Internetu a můžou mít přístup jenom k hostovaným koncovým bodům Internetu. Mnoho zákaznických aplikací ale potřebuje k řízení příchozího a odchozího síťového provozu. V App Service je k dispozici několik funkcí k uspokojení těchto potřeb. Tato výzva má na vědomí, jakou funkci byste měli použít k vyřešení daného problému. Účelem tohoto dokumentu je pomáhat zákazníkům určit, jakou funkci byste měli použít na základě některých příkladů případů použití.

Existují dva typy primárních nasazení pro Azure App Service. Je k dispozici veřejná služba s více klienty, která hostuje App Service plány v cenových jednotkách Free, Shared, Basic, Standard, Premium a Premiumv2. Pak je k dispozici jeden tenant App Service Environment (pomocný), který hostuje izolované SKU App Service plány přímo ve službě Azure Virtual Network (VNet). Funkce, které použijete, se budou lišit, pokud jste ve službě pro více tenantů nebo v pomocném modulu pro čtení. 

## <a name="multi-tenant-app-service-networking-features"></a>Funkce pro více tenantů App Service sítě 

Azure App Service je distribuovaný systém. Role, které zpracovávají příchozí požadavky HTTP/HTTPS, se nazývají front-endy. Role, které hostují úlohy zákazníka, se nazývají pracovní procesy. Všechny role v nasazení App Service existují v síti s více klienty. Vzhledem k tomu, že existuje mnoho různých zákazníků ve stejné App Service jednotce škálování, nemůžete připojit síť App Service přímo do vaší sítě. Místo připojení sítí potřebujeme funkce pro zpracování různých aspektů komunikace s aplikacemi. Funkce, které zpracovávají požadavky na vaši aplikaci, se nedají použít k řešení problémů při volání z vaší aplikace. Podobně funkce, které řeší problémy pro volání z vaší aplikace, se nedají použít k řešení problémů s vaší aplikací.  

| Příchozí funkce | Odchozí funkce |
|---------------------|-------------------|
| Adresa přiřazená aplikaci | Hybridní připojení |
| Omezení přístupu | Požadovaná brána Integration VNet |
| Koncové body služby | Integrace virtuální sítě |

Pokud není uvedeno jinak, lze použít všechny funkce společně. Můžete kombinovat funkce a vyřešit různé problémy.

## <a name="use-case-and-features"></a>Případ a funkce použití

U každého daného případu použití může být problém vyřešit několika způsoby.  Správná funkce, která má být použita, je někdy způsobena důvody mimo samotný případ použití. Následující případy příchozího použití ukazují, jak používat funkce App Service sítě k řešení problémů s řízením provozu, který do vaší aplikace směřuje. 
 
| Případy příchozího použití | Funkce |
|---------------------|-------------------|
| Podpora protokolu SSL založeného na protokolu IP pro vaši aplikaci | Adresa přiřazená aplikaci |
| Nesdílená a vyhrazená příchozí adresa pro vaši aplikaci | Adresa přiřazená aplikaci |
| Omezení přístupu k aplikaci ze sady dobře definovaných adres | Omezení přístupu |
| Omezení přístupu k aplikaci z prostředků ve virtuální síti | Koncové body služby </br> INTERNÍHO NÁSTROJE POMOCNÉHO MECHANISMU </br> Privátní koncový bod (Preview) |
| Zpřístupnit moji aplikaci na privátní IP adrese ve virtuální síti | INTERNÍHO NÁSTROJE POMOCNÉHO MECHANISMU </br> privátní IP adresa pro příchozí u Application Gateway s koncovými body služby </br> Koncový bod služby (Preview) |
| Ochrana aplikace pomocí WAF | Application Gateway + interního nástroje pomocného mechanismu </br> Application Gateway s koncovými body služby </br> Přední dvířka Azure s omezeními přístupu |
| Vyrovnávání zatížení u mých aplikací v různých oblastech | Přední dvířka Azure s omezeními přístupu | 
| Vyrovnávat zatížení provozu ve stejné oblasti | [Application Gateway s koncovými body služby][appgwserviceendpoints] | 

Následující případy odchozího použití ukazují, jak používat funkce App Service sítě k řešení požadavků na odchozí přístup k vaší aplikaci. 

| Případy odchozího použití | Funkce |
|---------------------|-------------------|
| Přístup k prostředkům v Azure Virtual Network ve stejné oblasti | Integrace virtuální sítě </br> ASE |
| Přístup k prostředkům v Azure Virtual Network v jiné oblasti | Požadovaná brána Integration VNet </br> Pomocného a virtuálním počítačem |
| Přístup k prostředkům zabezpečeným pomocí koncových bodů služby | Integrace virtuální sítě </br> ASE |
| Přístup k prostředkům v privátní síti nepřipojené k Azure | Hybridní připojení |
| Přístup k prostředkům napříč okruhy ExpressRoute | Integrace virtuální sítě </br> ASE | 
| Zabezpečení odchozího provozu z vaší webové aplikace | Integrace virtuální sítě a skupiny zabezpečení sítě </br> ASE | 
| Směrování odchozího provozu z vaší webové aplikace | Integrace virtuální sítě a směrovací tabulky </br> ASE | 


### <a name="default-networking-behavior"></a>Výchozí chování sítě

Jednotky škálování Azure App Service podporují mnoho zákazníků v každém nasazení. Bezplatné a sdílené SKU naplánují hostování úloh zákazníků na víceklientské pracovní procesy. Základní a vyšší plánuje úlohy hostitele, které jsou vyhrazeny jenom jednomu App Service plánu (ASP). Pokud máte plán Standard App Service, všechny aplikace v tomto plánu se spustí na stejném pracovním procesu. Při horizontálním navýšení kapacity pracovního procesu se všechny aplikace v tomto prostředí ASP replikují na nového pracovního procesu pro každou instanci ASP. Pracovní procesy používané pro Premiumv2 se liší od pracovníků používaných pro jiné plány. Každé nasazení App Service má jednu IP adresu, která se používá pro veškerý příchozí provoz do aplikací v rámci nasazení App Service. K odchozím voláním je ale možné použít libovolné místo od 4 do 11 adres. Tyto adresy sdílí všechny aplikace v rámci tohoto App Service nasazení. Odchozí adresy se liší v závislosti na různých typech pracovních procesů. To znamená, že adresy používané na úrovni Free, Shared, Basic, Standard a Premium ASP se liší od adres používaných pro odchozí hovory z Premiumv2 ASP. Pokud ve vlastnostech své aplikace hledáte, můžete zobrazit příchozí a odchozí adresy, které vaše aplikace používá. Pokud potřebujete uzamknout závislost se seznamem ACL protokolu IP, použijte possibleOutboundAddresses. 

![Vlastnosti aplikace](media/networking-features/app-properties.png)

App Service má počet koncových bodů, které se používají ke správě služby.  Tyto adresy jsou publikovány v samostatném dokumentu a jsou také ve značce AppServiceManagement IP Service. Značka AppServiceManagement se používá jenom s App Service Environment (pomocným mechanismem), kde je potřeba tento provoz umožňovat. Příchozí adresy App Service jsou sledovány ve značce služby IP AppService. Neexistuje žádná značka služby IP, která obsahuje odchozí adresy, které používá App Service. 

![App Service příchozího a odchozího diagramu](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresa přiřazená aplikaci 

Funkce přiřazená adresa aplikace je offshoot funkce SSL založené na protokolu IP a je k ní přistupovaná nastavením protokolu SSL s vaší aplikací. Tato funkce se dá použít pro volání SSL na základě IP adresy, ale dá se použít taky k tomu, aby se vaše aplikace dala adresovat jenom pro vaši aplikaci. 

![Diagram adres přiřazený aplikaci](media/networking-features/app-assigned-address.png)

Když použijete adresu přiřazenou aplikaci, váš provoz stále projde stejné role front-endu, které zpracovávají veškerý příchozí provoz do App Service jednotky škálování. Adresa, která je přiřazená vaší aplikaci, se ale používá jenom ve vaší aplikaci. Případy použití této funkce jsou:

* Podpora protokolu SSL založeného na protokolu IP pro vaši aplikaci
* Nastavte vyhrazenou adresu pro vaši aplikaci, která není sdílená s jakýmkoli jiným

V tomto kurzu se dozvíte, jak nastavit adresu v aplikaci pomocí kurzu [Přidání certifikátu TLS/SSL v Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Omezení přístupu 

Funkce omezení přístupu umožňuje filtrovat **příchozí** požadavky na základě původní IP adresy. Akce filtrování probíhá na front-end rolích, které jsou od rolí pracovních procesů, kde běží vaše aplikace, v nadřazeném umístění. Vzhledem k tomu, že role front-endu jsou z pracovních procesů nadřazené, může být schopnost omezení přístupu považovat za ochranu na úrovni sítě pro vaše aplikace. Tato funkce umožňuje vytvořit seznam bloků adres povolování a odepření, které jsou vyhodnocovány v pořadí podle priority. Podobá se funkci skupiny zabezpečení sítě (NSG), která existuje v sítích Azure.  Tuto funkci můžete použít v pomocném modulu pro čtení nebo ve službě pro více tenantů. Při použití s pomocným mechanismem interního nástroje můžete omezit přístup z privátních bloků adres.

![Omezení přístupu](media/networking-features/access-restrictions.png)

Funkce omezení přístupu pomáhá ve scénářích, kdy chcete omezit IP adresy, které se dají použít k přístupu k vaší aplikaci. Mezi případy použití této funkce patří:

* Omezení přístupu k aplikaci ze sady dobře definovaných adres 
* Omezte přístup k přihlašování prostřednictvím služby Vyrovnávání zatížení, jako jsou třeba přední dveře Azure. Pokud jste chtěli zablokovat příchozí provoz na přední dvířka Azure, vytvořte pravidla, která povolí přenos z 147.243.0.0/16 a 2a01:111:2050::/44. 

![Omezení přístupu s předními dvířky](media/networking-features/access-restrictions-afd.png)

Pokud chcete přístup k aplikaci uzamknout, aby se mohla získat jenom z prostředků v Azure Virtual Network (VNet), budete potřebovat statickou veřejnou adresu na jakémkoli zdroji ve vaší virtuální síti. Pokud prostředky nemají veřejnou adresu, měli byste místo toho použít funkci koncové body služby. Naučte se, jak tuto funkci povolit s kurzem [Konfigurace omezení přístupu][iprestrictions].

### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby umožňují Uzamknout **příchozí** přístup k aplikaci tak, že zdrojová adresa musí pocházet ze sady podsítí, kterou vyberete. Tato funkce funguje v kombinaci s omezeními přístupu k IP adresám. Koncové body služby se nastavují ve stejném uživatelském prostředí jako omezení přístupu IP. Můžete vytvořit seznam povolených a zakázaných pravidel přístupu, který zahrnuje veřejné adresy i podsítě v virtuální sítě. Tato funkce podporuje scénáře, jako například:

![koncové body služby](media/networking-features/service-endpoints.png)

* Nastavení Application Gateway s vaší aplikací pro uzamknutí příchozího provozu do vaší aplikace
* Omezte přístup k vaší aplikaci na prostředky ve vaší virtuální síti. To může zahrnovat virtuální počítače, služby ASE nebo i další aplikace, které používají integraci virtuální sítě. 

![koncové body služby s aplikační bránou](media/networking-features/service-endpoints-appgw.png)

Další informace o konfiguraci koncových bodů služby s vaší aplikací najdete v kurzu [Konfigurace omezení přístupu koncového bodu služby][serviceendpoints] .

### <a name="private-endpoint-preview"></a>Privátní koncový bod (Preview)

Privátní koncový bod je síťové rozhraní, které vám prostřednictvím privátního propojení Azure připojuje soukromě a bezpečně ke své webové aplikaci. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně přináší webovou aplikaci do vaší virtuální sítě. Tato funkce je určena pouze pro **příchozí** toky do vaší webové aplikace.
[Používání privátních koncových bodů pro webovou aplikaci Azure (Preview)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hybridní připojení

App Service Hybrid Connections umožňuje vašim aplikacím provádět **odchozí** volání do zadaných koncových bodů TCP. Koncový bod může být místní, ve virtuální síti nebo kdekoli, který umožňuje odchozí provoz do Azure na portu 443. Tato funkce vyžaduje instalaci přenosového agenta s názvem Správce hybridního připojení (HCM) na Windows Serveru 2012 nebo novějším hostiteli. HCM musí být schopné kontaktovat Azure Relay na portu 443. HCM je možné stáhnout z uživatelského rozhraní Hybrid Connections App Service na portálu. 

![Tok Hybrid Connections sítě](media/networking-features/hybrid-connections.png)

Funkce App Service Hybrid Connections je postavená na Hybrid Connections funkce Azure Relay. App Service používá specializovanou formu funkce, která podporuje pouze odchozí volání z vaší aplikace na hostitele a port TCP. Tento hostitel a port je potřeba přeložit jenom na hostiteli, na kterém je nainstalovaná HCM. Když aplikace v App Service provede vyhledávání DNS na hostiteli a portu definovaném v hybridním připojení, přenos se automaticky přesměruje, aby procházel prostřednictvím hybridního připojení a Správce hybridního připojení. Pokud se chcete dozvědět víc o Hybrid Connections, přečtěte si dokumentaci k [App Service Hybrid Connections][hybridconn]

Tato funkce se běžně používá k těmto akcím:

* Přístup k prostředkům v privátních sítích, které nejsou připojené k Azure pomocí sítě VPN nebo ExpressRoute
* Podpora přezvednutí a posunutí místních aplikací na App Service, aniž byste museli přesunout i podpůrné databáze  
* Bezpečně poskytněte přístup k jednomu hostiteli a portu na hybridní připojení. Většina síťových funkcí otevřela přístup k síti a s Hybrid Connections máte jenom jednoho hostitele a port, ke kterému se dostanete.
* Pokrytí scénářů, na které se nevztahuje jiné metody odchozího připojení
* Vývoj v App Service, kde aplikace můžou snadno využívat místní prostředky 

Vzhledem k tomu, že tato funkce umožňuje přístup k místním prostředkům bez příchozího otvoru brány firewall, je oblíbená pro vývojáře. Ostatní funkce pro odchozí App Service sítě jsou velmi související s virtuálními sítěmi Azure. Hybrid Connections nemá závislost při přechodu přes virtuální síť a dá se použít pro širší škálu síťových potřeb. Je důležité si uvědomit, že funkce App Service Hybrid Connections nezáleží ani na tom, co v nich děláte. To znamená, že ji můžete použít pro přístup k databázi, webové službě nebo libovolnému soketu TCP v rámci sálového počítače. Tato funkce v podstatě odpojování paketů TCP. 

I když je Hybrid Connections populární pro vývoj, používá se také v mnoha produkčních aplikacích. Je ideální pro přístup k webové službě nebo databázi, ale není vhodná pro situace zahrnující vytváření mnoha připojení. 

### <a name="gateway-required-vnet-integration"></a>Požadovaná brána Integration VNet 

Funkce Brána App Service Integration VNet umožňuje vaší aplikaci vytvářet **odchozí** požadavky do Azure Virtual Network. Funkce funguje připojením hostitele, na kterém je aplikace spuštěná, do Virtual Network brány ve vaší virtuální síti pomocí sítě VPN typu Point-to-site. Když nakonfigurujete funkci, aplikace získá jednu z adres Point-to-site přiřazených k jednotlivým instancím. Tato funkce umožňuje přístup k prostředkům v klasickém nebo Správce prostředků virtuální sítě v libovolné oblasti. 

![Požadovaná brána Integration VNet](media/networking-features/gw-vnet-integration.png)

Tato funkce řeší potíže s přístupem k prostředkům v jiných virtuální sítě a je možné ji dokonce použít k připojení přes virtuální síť k jiným virtuální sítě nebo dokonce i místnímu. Nefunguje s ExpressRoute připojenými virtuální sítě, ale s propojenými sítěmi VPN typu Site-to-site. Tato funkce se obvykle nedoporučuje používat z aplikace v App Service Environment (pomocného mechanismu), protože je už ve vaší virtuální síti. Případy použití, které tato funkce řeší:

* Přístup k prostředkům na privátních IP adresách v Azure Virtual Networks 
* Přístup k prostředkům v místním prostředí, pokud existuje síť VPN typu Site-to-site 
* Přístup k prostředkům v virtuální sítě s partnerským vztahem 

Když je tato funkce povolená, bude aplikace používat server DNS, se kterým je nakonfigurovaná cílová virtuální síť VNet. Další informace o této funkci najdete v dokumentaci k [integraci virtuální sítě App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrace virtuální sítě

Funkce vyžadovat integraci virtuální sítě pro bránu je velmi užitečná, ale stále neřeší přístup k prostředkům napříč ExpressRoute. Aby bylo možné dosáhnout mezi připojeními ExpressRoute, je potřeba, aby aplikace mohly volat zabezpečené služby koncového bodu služby. Aby bylo možné oba tyto další požadavky vyřešit, byla přidána další funkce integrace virtuální sítě. Nová funkce integrace virtuální sítě umožňuje umístit back-end aplikace do podsítě ve Správce prostředků virtuální síti ve stejné oblasti. Tato funkce není k dispozici z App Service Environment, která je již ve virtuální síti. Tato funkce umožňuje:

* Přístup k prostředkům v Správce prostředků virtuální sítě ve stejné oblasti
* Přístup k prostředkům, které jsou zabezpečené pomocí koncových bodů služby 
* Přístup k prostředkům, které jsou přístupné přes ExpressRoute nebo připojení VPN
* Zabezpečení všech odchozích přenosů 
* Vynutí tunelování veškerého odchozího provozu. 

![Integrace virtuální sítě](media/networking-features/vnet-integration.png)

Další informace o této funkci najdete v článku věnovaném [integraci virtuální sítě App Service][vnetintegration].

## <a name="app-service-environment"></a>App Service Environment 

App Service Environment (pomocným mechanismem) je nasazení jednoho tenanta Azure App Service, které ve vaší virtuální síti běží. Pomocí pomocného mechanismu můžete použít případy použití jako:

* Přístup k prostředkům ve virtuální síti
* Přístup k prostředkům napříč ExpressRoute
* Zveřejňujte své aplikace pomocí soukromé adresy ve virtuální síti. 
* Přístup k prostředkům napříč koncovými body služby 

Pomocí pomocného mechanismu služeb nemusíte používat funkce, jako je integrace virtuální sítě nebo koncové body služby, protože služba pomocného mechanismu už je ve vaší virtuální síti. Pokud chcete získat přístup k prostředkům, jako je SQL nebo úložiště prostřednictvím koncových bodů služby, povolte koncové body služby v podsíti pomocného mechanismu. Pokud chcete získat přístup k prostředkům ve virtuální síti, není nutná žádná další konfigurace.  Pokud chcete získat přístup k prostředkům v rámci ExpressRoute, už jste ve virtuální síti a nemusíte konfigurovat žádné informace na pomocném panelu nebo v aplikacích, které v něm jsou. 

Vzhledem k tomu, že aplikace v interního nástroje pomocném modulu pro přístup k síti můžou být vystavené na privátní IP adrese, můžete snadno přidat zařízení WAF a zpřístupnit tak pouze aplikace, které chcete používat na internetu, a zachovat zabezpečení REST. Přímo se zapůjčuje k usnadnění vývoje vícevrstvých aplikací. 

Existují některé věci, které ještě nejsou možné z víceklientské služby, která je z pomocného mechanismu pro čtení. Mezi ně patří například:

* Zveřejňujte své aplikace na privátní IP adrese
* Zabezpečení všech odchozích přenosů pomocí síťových ovládacích prvků, které nejsou součástí vaší aplikace 
* Hostování aplikací v jedné službě tenanta 
* Škálovat až na mnoho dalších instancí, než je možné ve službě pro více tenantů 
* Načtení klientských certifikátů privátní certifikační autority pro použití vašimi aplikacemi s zabezpečenými koncovými body privátní certifikační autority 
* Vynutit TLS 1,1 napříč všemi aplikacemi hostovanými v systému bez možnosti zakázat na úrovni aplikace 
* Poskytněte vyhrazenou výstupní adresu pro všechny aplikace v rámci služby řízení přihlašování, která není sdílená s žádnými zákazníky. 

![Pomocného mechanismu ve virtuální síti](media/networking-features/app-service-environment.png)

Pomocného programu poskytuje nejlepší příběh kolem izolované a vyhrazené hostování aplikací, ale přináší problémy se správou. Před používáním funkčního pomocného mechanismu je potřeba zvážit tyto věci:
 
 * Pomocného programu se spouští uvnitř vaší virtuální sítě, ale má závislosti mimo virtuální síť. Tyto závislosti musí být povoleny. Další informace najdete v tématu [požadavky na síť pro App Service Environment][networkinfo]
 * Služba pomocného mechanismu se nedá škálovat hned jako víceklientské služby. Místo reaktivního škálování je potřeba odhadnout požadavky na škálování. 
 * K pomocnému programu se přinese vyšší náklady na front-end. Abyste mohli využít své služby přihlašování na maximum, měli byste naplánovat velký počet úloh do jednoho pomocného mechanismu, místo aby se používaly pro malé úsilí.
 * Aplikace v pomocném mechanismu přístupu nemůžou omezovat přístup k některým aplikacím v pomocném formuláři, a ne ostatním.
 * Pomocného nástroje je v podsíti a všechna síťová pravidla se vztahují na veškerý provoz do a z tohoto pomocného mechanismu. Pokud chcete přiřadit pravidla příchozího přenosu jenom pro jednu aplikaci, použijte omezení přístupu. 

## <a name="combining-features"></a>Kombinování funkcí 

Funkce zjištěné pro víceklientské služby lze použít společně k řešení složitějších případů použití. Dva z častých případů použití jsou zde popsány, ale jsou zde pouze příklady. Tím, že pochopíte jednotlivé funkce, můžete vyřešit skoro všechny požadavky vaší systémové architektury.

### <a name="inject-app-into-a-vnet"></a>Vložení aplikace do virtuální sítě

Běžným požadavkem je postup, jak umístit aplikaci do virtuální sítě. Uvedení vaší aplikace do virtuální sítě znamená, že příchozí a odchozí koncové body pro aplikaci jsou v rámci virtuální sítě. Pomocného programu poskytuje nejlepší řešení pro vyřešení tohoto problému, ale můžete získat většinu toho, co je potřeba ve službě pro více tenantů, a to díky kombinování funkcí. Například můžete hostovat jenom aplikace intranetu s privátními příchozími a odchozími adresami:

* Vytvoření Application Gateway s privátní příchozí a odchozí adresou
* Zabezpečení příchozího provozu do aplikace pomocí koncových bodů služby 
* Využijte novou integraci virtuální sítě, aby se back-end vaší aplikace ve vaší virtuální síti používal. 

Tento styl nasazení vám neposkytne vyhrazenou adresu pro odchozí provoz na Internet nebo vám poskytne možnost Uzamknout veškerý odchozí provoz z vaší aplikace.  Tento styl nasazení vám poskytne spoustu toho, co byste jinak získali pomocí pomocného mechanismu. 

### <a name="create-multi-tier-applications"></a>Vytváření vícevrstvých aplikací

Vícevrstvá aplikace je aplikace, ve které se k aplikacím back-endu rozhraní API dá dostat jenom z front-endové úrovně. Chcete-li vytvořit vícevrstvou aplikaci, můžete:

* Použití integrace virtuální sítě k připojení back-endu webové aplikace front-end k podsíti ve virtuální síti
* Použití koncových bodů služby k zabezpečení příchozího provozu do vaší aplikace API, aby se dostaly jenom z podsítě používané front-end webovou aplikací

![Vícevrstvá aplikace](media/networking-features/multi-tier-app.png)

Můžete mít několik front-endové aplikací, které používají stejnou aplikaci API pomocí integrace virtuální sítě z jiných front-end aplikací a koncových bodů služby z aplikace API s jejich podsítěmi.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
