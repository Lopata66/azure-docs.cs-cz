---
title: Několik front-endu – Azure Load Balancer
description: Pomocí této cesty výukového programu začněte s přehledem více front-endu na Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: f6943a95cd327785d4907bb675958be99b902764
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644932"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Několik front-endu pro Azure Load Balancer

Azure Load Balancer umožňuje vyrovnávat zatížení služeb na několika portech, několika IP adresách nebo obojím. Můžete použít definice veřejného a interního nástroje pro vyrovnávání zatížení k vyrovnávání zatížení toků v rámci sady virtuálních počítačů.

Tento článek popisuje základy této možnosti, důležitých konceptů a omezení. Pokud máte v úmyslu zveřejnit jenom služby na jedné IP adrese, můžete najít zjednodušené pokyny pro konfiguraci [veřejného](load-balancer-get-started-internet-portal.md) nebo [interního](load-balancer-get-started-ilb-arm-portal.md) nástroje pro vyrovnávání zatížení. Přidání více front-endu je přírůstkové na jednu konfiguraci front-endu. Pomocí konceptů v tomto článku můžete kdykoli rozšířit zjednodušenou konfiguraci.

Při definování Azure Load Balancer se s pravidly připojí front-end a konfigurace fondu back-endu. Sonda stavu, na kterou pravidlo odkazuje, se používá k určení toho, jak se nové toky odesílají do uzlu ve fondu back-endu. Front-end (označovaný také jako VIP) je definován třemi řazenými kolekcemi, které se skládají z IP adresy (veřejné nebo interní), přenosového protokolu (UDP nebo TCP) a čísla portu z pravidla vyrovnávání zatížení. Back-end fond je kolekcí konfigurací IP adres virtuálních počítačů (součást prostředku síťové karty), která odkazuje na Load Balancer fond back-endu.

Následující tabulka obsahuje příklady front-endové konfigurace:

| Front-end | IP adresa | protocol | port |
| --- | --- | --- | --- |
| 1\. místo |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

V tabulce jsou uvedeny čtyři různé front-endové. Front-endové #1, #2 a #3 jsou jediné front-end s více pravidly. Stejná IP adresa se používá, ale port nebo protokol se liší pro každý front-end. Front-endové #1 a #4 jsou příkladem několika front-endu, ve kterých se stejný protokol a port front-endu používá opakovaně napříč více frontami.

Azure Load Balancer poskytuje flexibilitu při definování pravidel vyrovnávání zatížení. Pravidlo deklaruje, jak se adresa a port ve front-endu mapují na cílovou adresu a port na back-endu. Bez ohledu na to, jestli se v pravidlech znovu použijí back-endové porty, závisí na typu pravidla. Každý typ pravidla má specifické požadavky, které mohou ovlivnit konfiguraci hostitele a návrh testu paměti. Existují dva typy pravidel:

1. Výchozí pravidlo bez opakovaného použití portů back-endu
2. Plovoucí pravidlo IP, ve kterém se znovu používají back-endové porty

Azure Load Balancer umožňuje kombinovat oba typy pravidel v rámci stejné konfigurace nástroje pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení je může používat současně pro daný virtuální počítač nebo libovolnou kombinaci, pokud se řídí omezeními pravidla. Typ pravidla, které zvolíte, závisí na požadavcích vaší aplikace a na složitosti podpory této konfigurace. Měli byste vyhodnotit, které typy pravidel jsou pro váš scénář nejvhodnější.

Tyto scénáře podrobněji prozkoumáme tak, že začnete s výchozím chováním.

## <a name="rule-type-1-no-backend-port-reuse"></a>Typ pravidla #1: bez opakovaného použití portu back-endu

![Několik ilustračních front-endu se zeleným a fialovým front-endu](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

V tomto scénáři jsou front-endové konfigurace takto:

| Front-end | IP adresa | protocol | port |
| --- | --- | --- | --- |
| ![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1\. místo |65.52.0.1 |TCP |80 |
| ![fialový front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP je cíl příchozího toku. V back-end fondu zpřístupňuje každý virtuální počítač požadovanou službu na jedinečném portu na DIP. Tato služba je přidružená k front-endu prostřednictvím definice pravidla.

Definujeme dvě pravidla:

| Pravidlo | Mapování front-endu | Do back-endu fondu |
| --- | --- | --- |
| 1\. místo |![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![Virtuální IP adresa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Úplné mapování v Azure Load Balancer je teď následující:

| Pravidlo | Front-endová IP adresa | protocol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelené pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1\. místo |65.52.0.1 |TCP |80 |IP adresa DIP |80 |
| ![fialové pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |IP adresa DIP |81 |

Každé pravidlo musí vytvořit tok s jedinečnou kombinací cílové IP adresy a cílového portu. Díky proměnlivému cílovému portu toku může více pravidel doručovat toky do stejné DIP na různých portech.

Sondy stavu se vždycky přesměrují na DIP virtuálního počítače. Musíte zajistit, aby sonda odrážela stav virtuálního počítače.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ pravidla #2: použití back-endu portů pomocí plovoucí IP adresy

Azure Load Balancer poskytuje flexibilitu při opakovaném použití portu front-endu napříč několika front-endu bez ohledu na použitý typ pravidla. Kromě toho některé scénáře aplikací dávají přednost nebo vyžadují stejný port, který má používat víc instancí aplikace na jednom virtuálním počítači ve fondu back-endu. Mezi běžné příklady opakovaného použití portů patří clustering pro vysokou dostupnost, síťová virtuální zařízení a vystavování více koncových bodů TLS bez nutnosti opětovného šifrování.

Pokud chcete znovu použít port back-end v rámci více pravidel, musíte v definici pravidla povolit plovoucí IP adresu.

Plovoucí IP adresa je terminologie Azure, která je součástí toho, co je známo jako přímý Server pro vracení (DSR). DSR se skládá ze dvou částí: topologie toku a schéma mapování IP adres. Na úrovni platformy Azure Load Balancer vždy pracuje s topologií toku DSR bez ohledu na to, zda je povolena plovoucí IP adresa. To znamená, že výstupní část toku je vždy správně přepsána do toku přímo zpět k původnímu zdroji.

Pomocí výchozího typu pravidla Azure zveřejňuje tradiční schéma mapování IP adres pro vyrovnávání zatížení, které umožňuje snadné použití. Povolením plovoucí IP adresy změníte schéma mapování IP adres tak, aby bylo možné zvýšit flexibilitu, jak je vysvětleno níže.

Tato konfigurace je znázorněna na následujícím obrázku:

![Víc na front-endu s zeleným a fialovým front-endu pomocí DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Pro tento scénář má každý virtuální počítač ve fondu back-end tři síťová rozhraní:

* DIP: Virtuální síťová karta přidružená k virtuálnímu počítači (konfigurace protokolu IP prostředku síťových adaptérů Azure)
* Front-end 1: rozhraní zpětné smyčky v rámci hostovaného operačního systému, které má nakonfigurovanou IP adresu front-endu 1.
* Front-end 2: rozhraní zpětné smyčky v rámci hostovaného operačního systému, které má nakonfigurovanou IP adresu front-endu 2

> [!IMPORTANT]
> Konfigurace rozhraní zpětné smyčky se provádí v hostovaném operačním systému. Tuto konfiguraci neprovádí ani nespravuje Azure. Bez této konfigurace nebudou pravidla fungovat. Definice sondy stavu používají DIP virtuální počítač místo rozhraní zpětné smyčky představujícího front-endu DSR. Proto musí vaše služba poskytovat odezvy sondy na portu DIP, který odráží stav služby nabízené na rozhraní zpětné smyčky, které představuje front-endu DSR.

Pojďme předpokládat stejnou konfiguraci front-endu jako v předchozím scénáři:

| Front-end | IP adresa | protocol | port |
| --- | --- | --- | --- |
| ![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1\. místo |65.52.0.1 |TCP |80 |
| ![fialový front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definujeme dvě pravidla:

| Pravidlo | Front-end | Mapovat na back-end fond |
| --- | --- | --- |
| 1\. místo |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (v VM1 a VM2) |
| 2 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (v VM1 a VM2) |

V následující tabulce je uvedeno úplné mapování v nástroji pro vyrovnávání zatížení:

| Pravidlo | Front-endová IP adresa | protocol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelené pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1\. místo |65.52.0.1 |TCP |80 |stejné jako front-end (65.52.0.1) |stejné jako front-end (80) |
| ![fialové pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |stejné jako front-end (65.52.0.2) |stejné jako front-end (80) |

Cíl příchozího toku je IP adresa front-endu na rozhraní zpětné smyčky ve virtuálním počítači. Každé pravidlo musí vytvořit tok s jedinečnou kombinací cílové IP adresy a cílového portu. Díky proměnlivé cílové IP adrese toku je možné znovu použít port na stejném virtuálním počítači. Službu Vyrovnávání zatížení zpřístupní služba pro vyrovnávání zatížení tak, že ji naváže na IP adresu front-end a port příslušného rozhraní zpětné smyčky.

Všimněte si, že tento příklad nemění cílový port. I když se jedná o scénář s plovoucí IP adresou, Azure Load Balancer také podporuje definování pravidla pro přepsání cílového portu back-endu a jeho rozdíl od cílového portu front-endu.

Typ pravidla plovoucí IP adresy je základem několika vzorů konfigurace nástroje pro vyrovnávání zatížení. Jedním z aktuálně dostupných je konfigurace [AlwaysOn SQL s více procesy naslouchání](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . V průběhu času budeme dokumentovat více z těchto scénářů.

## <a name="limitations"></a>Omezení

* Víc konfigurací front-endu podporuje jenom virtuální počítače s IaaS.
* V případě plovoucího pravidla IP musí vaše aplikace používat primární konfiguraci IP adres pro odchozí toky SNAT. Pokud se vaše aplikace váže k IP adrese front-endu nakonfigurované na rozhraní zpětné smyčky v hostovaném operačním systému, odchozí SNAT Azure není k dispozici pro přepsání odchozího toku a tok se nezdařil.  Projděte si [odchozí scénáře](load-balancer-outbound-connections.md).
* Veřejné IP adresy mají vliv na fakturaci. Další informace najdete v tématu [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/) .
* Platí omezení předplatného. Další informace najdete v tématu [omezení služby](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) pro podrobnosti.

## <a name="next-steps"></a>Další kroky

- Projděte si [odchozí připojení](load-balancer-outbound-connections.md) , abyste pochopili dopad více front-endového chování při odchozím připojení.
