---
title: SNAT pro odchozí připojení
description: Popisuje, jak Azure Load Balancer použít k provedení SNAT pro odchozí připojení k Internetu.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d1632c66791dd5e697b95a2c5aaaddea81629abf
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052818"
---
# <a name="using-snat-for-outbound-connections"></a>Použití SNAT pro odchozí připojení

Front-endové IP adresy veřejného nástroje pro vyrovnávání zatížení se dají použít k zajištění odchozího připojení k Internetu pro back-endové instance. Tato konfigurace používá **Překlad zdrojového síťového adres (SNAT)**. SNAT přepíše IP adresu back-endu na veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení. 

SNAT umožňuje **maskování IP** instance back-endu. Tato maskování brání vnějším zdrojům, aby měly přímo adresu back-end instancí. Sdílení IP adresy mezi back-end instancemi snižuje náklady na statické veřejné IP adresy a podporuje scénáře, jako je například zjednodušení seznamů povolených IP adres, ze známých veřejných IP adres. 

>[!Note]
> Pro aplikace, které vyžadují velký počet odchozích připojení nebo podnikových zákazníků, kteří vyžadují jednu sadu IP adres, která se má použít z dané virtuální sítě, je doporučeným řešením [Virtual Network překlad adres (NAT)](../virtual-network/nat-overview.md) . Toto dynamické přidělení umožňuje jednoduchou konfiguraci a > nejúčinnější využití portů SNAT z každé IP adresy. Umožňuje také všem prostředkům ve virtuální síti sdílet sadu IP adres bez nutnosti sdílení > nástroje pro vyrovnávání zatížení.

>[!Important]
> I bez nakonfigurovaného odchozího SNAT budou účty Azure Storage v rámci stejné oblasti pořád dostupné a back-end prostředky budou mít i nadále přístup ke službám Microsoftu, jako jsou třeba aktualizace Windows.

>[!NOTE] 
>Tento článek se zabývá jenom nasazeními Azure Resource Manager. Zkontrolujte [odchozí připojení (Classic)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) pro všechny scénáře nasazení Classic v Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Sdílení IP adresy front-endu v prostředcích

Pokud back-end prostředky nástroje pro vyrovnávání zatížení nemají adresy veřejných IP adres na úrovni instance (ILPIP), navážou odchozí připojení prostřednictvím front-endu veřejného nástroje pro vyrovnávání zatížení. Porty slouží ke generování jedinečných identifikátorů používaných k údržbě různých toků. Internet používá k poskytnutí tohoto rozlišení pět-Tuple.

Pět-Tuple se skládá z těchto:

* Cílová IP adresa
* Cílový port
* Zdrojová IP adresa
* Zdrojový port a protokol pro zajištění tohoto rozlišení.

Pokud se port používá pro příchozí připojení, bude mít **naslouchací proces** pro požadavky na příchozí připojení na tomto portu a nedá se použít pro odchozí připojení. Aby bylo možné navázat odchozí připojení, musí být k dispozici **dočasný port** pro určení portu, na kterém má být zajištěna komunikace a údržba odlišného toku přenosů. Když se tyto dočasné porty používají k provedení SNAT, budou se nazývají **porty SNAT** . 

Podle definice Každá IP adresa má 65 535 portů. Každý port lze použít pro příchozí nebo odchozí připojení protokolu TCP (Transmission Control Protocol) a UDP (protokol User Datagram). Když se do nástroje pro vyrovnávání zatížení přidá veřejná IP adresa jako front-endové IP adresy, Azure vám poskytne 64 000 s nárokem na použití jako porty SNAT. 

>[!NOTE]
> Každý port používaný pro pravidlo vyrovnávání zatížení nebo příchozí překlad adres (NAT) bude využívat rozsah osmi portů z těchto 64 000 portů, čímž se sníží počet portů, které mají nárok na SNAT. Pokud je vyrovnávání zatížení > nebo pravidlo překladu adres (NAT) ve stejném rozsahu, než je osm, bude spotřebovávat žádné další porty. 

Prostřednictvím [odchozích pravidel](./outbound-rules.md) a pravidel vyrovnávání zatížení se tyto porty SNAT dají distribuovat do back-endu instancí, aby mohly sdílet veřejné IP adresy nástroje pro vyrovnávání zatížení pro odchozí připojení.

Pokud je nakonfigurován [scénář 2](#scenario2) níže, bude hostitel pro každou back-end instancí provádět až SNAT v paketech, které jsou součástí odchozího připojení. Při provádění protokolu SNAT u odchozího připojení z back-endu instance hostitel přepíše zdrojovou IP adresu do jedné z IP adres front-endu. Aby bylo možné zachovat jedinečné toky, hostitel přepíše zdrojový port každého odchozího paketu na jeden z portů SNAT přidělených pro instanci back-endu.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Chování odchozího připojení pro různé scénáře
  * Virtuální počítač s veřejnou IP adresou
  * Virtuální počítač bez veřejné IP adresy
  * Virtuální počítač bez veřejné IP adresy a bez standardního nástroje pro vyrovnávání zatížení.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Scénář 1: virtuální počítač s veřejnou IP adresou


 | Přidružení | Metoda | Protokoly IP |
 | ---------- | ------ | ------------ |
 | Veřejný Nástroj pro vyrovnávání zatížení nebo samostatný | [SNAT (zdrojový překlad adresy zdrojové sítě)](#snat) </br> nepoužívá se. | TCP (protokol řízení přenosů) </br> UDP (protokol datadatagram uživatele) </br> ICMP (Internet Control Message Protocol) </br> ESP (zapouzdření datové části zabezpečení) |


 #### <a name="description"></a>Description


 Azure používá veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP síťové karty instance pro všechny odchozí toky. Instance má k dispozici všechny dočasné porty. Nezáleží na tom, jestli je virtuální počítač vyrovnaný k vyrovnávání zatížení. Tento scénář má přednost před ostatními. 


 Veřejná IP adresa přiřazená k virtuálnímu počítači je vztah 1:1 (nikoli 1: mnoho) a implementovaný jako bezstavové 1:1 NAT.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scénář 2: virtuální počítač bez veřejné IP adresy a za standardním veřejným Load Balancer


 | Přidružení | Metoda | Protokoly IP |
 | ------------ | ------ | ------------ |
 | Standardní veřejný Nástroj pro vyrovnávání zatížení | Použití IP adresy front-endu nástroje pro vyrovnávání zatížení pro [SNAT](#snat).| TCP </br> UDP |


 #### <a name="description"></a>Description


 Prostředek nástroje pro vyrovnávání zatížení je nakonfigurovaný pomocí odchozího pravidla nebo pravidla vyrovnávání zatížení, které umožňuje výchozí SNAT. Toto pravidlo slouží k vytvoření propojení mezi veřejnou IP frontou front-endu a back-end fondem. 


 Pokud konfiguraci pravidla nedokončíte, bude chování popsané ve scénáři 3. 


 Pro úspěšné provedení testu stavu není vyžadováno pravidlo s naslouchacím rozhraním.


 Když virtuální počítač vytvoří odchozí tok, Azure převede zdrojovou IP adresu na veřejnou IP adresu front-endu veřejného nástroje pro vyrovnávání zatížení. Tento překlad se provádí přes [SNAT](#snat). 


 Dočasné porty pro veřejnou IP adresu front-endu pro vyrovnávání zatížení se používají k odlišení jednotlivých toků pocházejících z virtuálního počítače. SNAT dynamicky používá [předpřidělené dočasné porty](#preallocatedports) při vytváření odchozích toků. 


 V tomto kontextu se dočasné porty používané pro SNAT nazývají porty SNAT. Důrazně doporučujeme, aby [odchozí pravidlo](./outbound-rules.md) bylo explicitně nakonfigurované. Pokud použijete výchozí SNAT prostřednictvím pravidla vyrovnávání zatížení, porty SNAT jsou předem přiděleny, jak je popsáno ve [výchozí tabulce alokace portů SNAT](#snatporttable).

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scénář 3: virtuální počítač bez veřejné IP adresy a za standardním interním Load Balancer


 | Přidružení | Metoda | Protokoly IP |
 | ------------ | ------ | ------------ |
 | Standardní interní nástroj pro vyrovnávání zatížení | Bez připojení k Internetu.| Žádné |

 #### <a name="description"></a>Description
 
Při použití standardního interního nástroje pro vyrovnávání zatížení není k dispozici žádné použití dočasných IP adres pro SNAT. Tím se standardně podporují zabezpečení a zajišťují, že všechny IP adresy, které prostředek používá, se dají konfigurovat a můžou být rezervované. Aby bylo možné dosáhnout odchozího připojení k Internetu při použití standardního interního nástroje pro vyrovnávání zatížení, nakonfigurujte veřejnou IP adresu na úrovni instance tak, aby následovala podle chování (scénář 1) [#scenario1], nebo přidejte back-end instance ke standardnímu veřejnému nástroji pro vyrovnávání zatížení s odchozím pravidlem nakonfigurovaným v dále do interního nástroje pro vyrovnávání zatížení #scenario2, aby 

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scénář 4: virtuální počítač bez veřejné IP adresy a za základní Load Balancer


 | Přidružení | Metoda | Protokoly IP |
 | ------------ | ------ | ------------ |
 |Žádné </br> Základní nástroj pro vyrovnávání zatížení | [SNAT](#snat) s dynamickou IP adresou na úrovni instance| TCP </br> UDP | 

 #### <a name="description"></a>Description


 Když virtuální počítač vytvoří odchozí tok, Azure převede zdrojovou IP adresu dynamicky přidělené veřejné zdrojové IP adrese. Tato veřejná IP adresa **není konfigurovatelná** a nedá se rezervovat. Tato adresa se nepočítá s omezením prostředků veřejné IP adresy předplatného. 


 Veřejná IP adresa se uvolní a nová veřejná IP adresa se požaduje, pokud znovu nasadíte: 


 * Virtuální počítač
 * Skupina dostupnosti
 * Škálovací sada virtuálních počítačů 


 Nepoužívejte tento scénář pro přidání IP adres do seznamu povolených serverů. Použijte scénář 1 nebo 2, kde explicitně deklarujete odchozí chování. Porty [SNAT](#snat) jsou předpřidělené, jak je popsáno ve [výchozí tabulce alokace portů SNAT](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Vyčerpání portů

Každé připojení ke stejné cílové IP adrese a cílovému portu bude používat port SNAT. Toto připojení udržuje odlišný **přenosový tok** z instance back-endu nebo z **klienta** na **Server**. Tento proces přidělí serveru jedinečný port, na který se má adresovat přenos. Bez tohoto procesu nevíte na klientském počítači, který tok paketu je součástí.

Představte si, že budete mít více prohlížečů https://www.microsoft.com , což je:

* Cílová IP adresa = 23.53.254.142
* Cílový port = 443
* Protokol = TCP

Bez různých cílových portů pro návratový provoz (port SNAT použitý k navázání připojení) nebude mít klient žádný způsob, jak oddělit jeden výsledek dotazu od jiného.

Odchozí připojení se můžou rozpojovat. Instanci back-endu je možné přidělit nedostatečným portům. Bez povoleného **opětovného použití připojení** se zvyšuje riziko **vyčerpání portů** SNAT.

Nová odchozí připojení k cílové IP adrese selžou, když dojde k vyčerpání portů. Po zpřístupnění portu budou připojení úspěšná. K této vyčerpání dojde, když se porty 64 000 z IP adresy šíří dynamicky napříč celou řadou back-end instancí. Pokyny ke zmírnění vyčerpání portů SNAT najdete v [Průvodci odstraňováním potíží](./troubleshoot-outbound-connection.md).  

Pro připojení TCP bude nástroj pro vyrovnávání zatížení používat pro každou cílovou IP adresu a port jeden port SNAT. Tento Multiuse umožňuje více připojení ke stejné cílové IP adrese se stejným portem SNAT. Tato Multiuse je omezená, pokud není připojení k různým cílovým portům.

V případě připojení UDP využívá nástroj pro vyrovnávání zatížení algoritmus **překladu adres (NAT) s omezeným portem** , který využívá jeden port SNAT na cílovou IP adresu bez ohledu na cílový port. 

Port se znovu používá pro neomezený počet připojení. Port se znovu použije jenom v případě, že cílová IP adresa nebo port je jiný.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Výchozí přidělení portu

Každé veřejné IP adresy přiřazené jako front-endové IP službě pro vyrovnávání zatížení mají porty 64 000 SNAT pro své členy fondu back-endu. Porty nelze sdílet se členy fondu back-end. Rozsah portů SNAT může použít jenom jediná instance back-endu, aby se zajistilo správné směrování vrácených paketů. 

Doporučuje se použít explicitní odchozí pravidlo pro konfiguraci přidělování portů SNAT. Toto pravidlo maximalizuje počet portů SNAT, které každá instance back-end má k dispozici pro odchozí připojení. 

Pokud používáte automatické přidělování odchozích SNAT prostřednictvím pravidla vyrovnávání zatížení, bude tabulka alokace definovat vaše přidělení portu.

V následující <a name="snatporttable"></a> tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 | 

>[!NOTE]
> Pokud máte back-end fond s maximální velikostí 10, každá instance může mít 64000/10 = 6 400 portů, pokud definujete explicitní odchozí pravidlo. Podle výše uvedené tabulky bude mít každá z nich jenom 1 024, pokud zvolíte automatické přidělování.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Odchozí pravidla a Virtual Network překlad adres (NAT)

Azure Load Balancer odchozí pravidla a Virtual Network překlad adres (NAT) jsou dostupné možnosti pro odchozí přenos dat z virtuální sítě.

Další informace o odchozích pravidlech najdete v tématu [odchozí pravidla](outbound-rules.md).

Další informace o službě Azure Virtual Network NAT najdete v tématu [co je azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Omezení

*   Když je připojení nečinné, protože se neodesílají žádné nové pakety, porty se uvolní po 4 až 120 minutách.
  * Tato prahová hodnota se dá nakonfigurovat prostřednictvím odchozích pravidel.
*   Každá IP adresa poskytuje 64 000 portů, které se dají použít pro SNAT.
*   Každý port se dá použít pro připojení TCP i UDP k cílové IP adrese.
  * Port UDP SNAT je vyžadován, pokud je cílový port jedinečný nebo nikoli. Pro každé připojení UDP k cílové IP adrese se použije jeden port UDP SNAT.
  * Port TCP SNAT lze použít pro více připojení ke stejné cílové IP adrese, pokud jsou cílové porty odlišné.
*   K vyčerpání SNAT dojde v případě, že instance back-endu běží z daných portů SNAT. Nástroj pro vyrovnávání zatížení může mít stále nepoužívané porty SNAT. Pokud použité porty SNAT překročí zadané porty SNAT, nebude možné vytvořit nová odchozí připojení.

## <a name="next-steps"></a>Další kroky

*   [Řešení selhání odchozího připojení kvůli vyčerpání SNAT](./troubleshoot-outbound-connection.md)
*   [Projděte si metriky SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) a seznamte se se správným způsobem, jak je filtrovat, rozdělit a zobrazit.
