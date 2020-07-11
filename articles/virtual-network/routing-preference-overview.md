---
title: Předvolby směrování v Azure
description: Přečtěte si, jak si můžete vybrat, jak vaše trasy přenosů mezi Azure a internetem a prioritou směrování.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 171ae04a70f3639981e74d20d260a03359a0f2c8
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187434"
---
# <a name="what-is-routing-preference-preview"></a>Co je předvolby směrování (Preview)?

Předvolby směrování Azure vám umožní zvolit způsob přenosu tras mezi Azure a internetem. Provoz můžete směrovat buď přes síť Microsoft, nebo prostřednictvím sítě poskytovatele internetových služeb (veřejný Internet). Tyto možnosti se také označují jako *Směrování studených brambor* a *horké směrování brambor* . Cena za odchozí přenosy dat se liší v závislosti na výběru směrování. Při vytváření veřejné IP adresy můžete zvolit možnost směrování. Veřejná IP adresa může být přidružená k prostředkům, jako jsou virtuální počítače, sady škálování virtuálních počítačů, internetový nástroj pro vyrovnávání zatížení atd. Můžete také nastavit předvolby směrování pro prostředky služby Azure Storage, jako jsou objekty blob, soubory, webové služby a Azure datalake. Ve výchozím nastavení jsou přenosy směrovány prostřednictvím globální sítě Microsoft pro všechny služby Azure.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="routing-via-microsoft-global-network"></a>Směrování prostřednictvím globální sítě Microsoft

Při směrování provozu přes *globální síť Microsoftu*se provoz doručuje přes jednu z největších sítí na celém světě, která pokrývá více než 160 000 mil na vláknu s více než 165 body přítomnosti (pop). Síť je dobře zřízena s několika redundantními vlákny a zajišťuje tak mimořádně vysokou spolehlivost a dostupnost. Technologie provozu je spravovaná softwarem, který je definovaný pro síťový adaptér WAN, který zajišťuje výběr cest s nízkou latencí pro provoz a nabízí špičkový výkon sítě.

![Směrování prostřednictvím globální sítě Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Příchozí přenos dat:** Globální oznámení o příchozím přenosu BGP zajišťuje, že příchozí provoz vstoupí do sítě Microsoftu co nejblíže uživateli. Pokud například uživatel z Singapuru přistupuje k prostředkům Azure hostovaným v Chicagu, USA, pak se provoz do globální sítě Microsoft v rámci Singapuru Edge zahájí a v síti se zaznamená do služby hostované v Chicagu.

**Odchozí přenos dat:** Odchozí přenosy se řídí stejným principem. Provoz cestuje v rámci své cesty v globální síti Microsoft a ukončuje se nejblíže k uživateli. Například pokud je provoz z Azure Chicago určen uživateli ze Singapuru, provoz se v síti Microsoftu přenáší z Chicago do Singapuru a ukončí síť Microsoftu v rámci nástroje pro Singapur Edge.

Příchozí i výstupní provoz zachová velkou cestu k globální síti Microsoftu. Tento postup se označuje také jako *Směrování studených brambor*.


## <a name="routing-over-public-internet-isp-network"></a>Směrování přes veřejný Internet (síť poskytovatele internetových služeb)

Nová možnost směrování *sítě Internet* minimalizuje služební cestu k globální síti Microsoft a používá tranzitní síť poskytovatele internetových služeb ke směrování provozu. Tato možnost směrování s optimálním výkonem nabízí výkon sítě, který je porovnatelný z jiných poskytovatelů cloudu.

![Směrování přes veřejný Internet](media/routing-preference-overview/route-via-isp-network.png)

**Příchozí přenos dat:** Cesta příchozího provozu používá *aktivní směrování brambor* , což znamená, že provoz vstoupí do sítě Microsoftu, která je nejblíže oblasti hostované služby. Například pokud uživatel z Singapuru přistupuje k prostředkům Azure hostovaným v Chicagu, provoz se prochází přes veřejný Internet a přejde do globální sítě Microsoft v Chicagu.

**Odchozí přenos dat:** Odchozí přenosy se řídí stejným principem. Provoz ukončí síť Microsoftu ve stejné oblasti, ve které je služba hostovaná. Pokud je například provoz z vaší služby v Azure v Chicagu pro uživatele určený od Singapuru, pak provoz ukončí síť Microsoftu v Chicagu a v rámci veřejného Internetu se přenese do uživatele v Singapuru.

## <a name="supported-services"></a>Podporované služby

Veřejná IP adresa s volbou předvolby směrování "globální síť Microsoft" může být přidružená k jakékoli službě Azure. Veřejné IP adresy s volbou předvolby směrování si **ale můžete** přidružit k následujícím prostředkům Azure:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Azure Firewall

Pro úložiště primární koncové body vždy používají **globální síť Microsoft**. Můžete povolit sekundární koncové body s **internetem** podle vašeho výběru pro směrování provozu. Podporované služby úložiště:

* Objekty blob
* Soubory
* Web
* Azure DataLake

## <a name="pricing"></a>Ceny
Cenový rozdíl mezi oběma možnostmi se odráží v ceně za přenos dat z Internetu. Směrování prostřednictvím služby **Microsoft Global Network** Data Transfer je stejné jako aktuální cena za internetovou cenu. Nejnovější informace o cenách najdete na [stránce s cenami na šířku pásma Azure](https://azure.microsoft.com/pricing/details/bandwidth/) . Směrování přes **veřejný Internet** je levnější, jak je uvedeno v následující tabulce:

| Zdrojová oblast pro výstup | 0-5 GB/měsíc | 5 GB – 10 TB/měsíc | 10-50 TB/měsíc | 50-150 TB/měsíc | 150-500 TB/měsíc |
| --- | --- | --- | --- | --- | --- |
| Zóna 1 | $0/GB | $0.085/GB | $0.065/GB | $0.06/GB | $0.04/GB |
| Zóna 2 | $0/GB | $0,11/GB | $0.075/GB | $0,07/GB | $0.06/GB  |

[Kontaktujte nás](https://azure.microsoft.com/overview/sales-number/) pro měsíční objemy přes 500 TB.
* Zóna 1 – střední Austrálie, Austrálie – střed 2, Kanada – střed, Kanada – východ, Severní Evropa, Západní Evropa, Francie – střed, Francie – jih, Německo – sever (Public), Německo – středozápad (veřejný), Norsko – východ, Švýcarsko – sever, Švýcarsko – západ, Velká Británie – jih, Velká Británie – západ, střed USA, východní USA, východní USA 2, Střed USA – sever, Střed USA – jih, Západní USA, západní USA 2, středozápadní USA, a.

* Zóna 2 – Východní Asie, jihovýchodní Asie, Austrálie – východ, Austrálie – jihovýchod, Střed Indie, Jižní Indie, Západní Indie, Japonsko – východ, Japonsko – západ, Korea – střed a Jižní Korea.

* Zóna 3 – Brazílie – jih, Jižní Afrika – sever, Jižní Afrika – západ, Spojené arabské emiráty střed a Spojené arabské emiráty sever.

## <a name="availability"></a>Dostupnost

Podpora předvoleb směrování je dostupná v následujících oblastech pro služby, jako je virtuální počítač a internetový nástroj pro vyrovnávání zatížení, který používá veřejnou IP adresu pro internetovou odchozí Severní Evropa, Západní Evropa, Francii jih, Velká Británie – jih, Východní USA, Střed USA – sever, Střed USA – jih, Západní USA, Středozápadní USA, jihovýchodní Asie, Německo – středozápad, Švýcarsko – západ, Japonsko – východ a Japonsko – západ.

Podpora předvoleb směrování pro účet úložiště je k dispozici v následujících oblastech Azure – Střed USA – sever, Středozápadní USA, Střed USA – jih Východní USA, Západní USA, Severní Evropa, Francie – jih, Německo – středozápad, Švýcarsko – západ, Jižní Východní Asie, Japonsko – východ a Japonsko – západ.
## <a name="limitations"></a>Omezení

* Preference směrování je kompatibilní jenom se standardní SKU veřejné IP adresy. Základní SKU veřejné IP adresy se nepodporuje.
* Preference směrování aktuálně podporuje jenom veřejné IP adresy IPv4. Veřejné IP adresy protokolu IPv6 nejsou podporovány.
* Virtuální počítače s několika síťovými kartami můžou mít jenom jeden typ předvolby směrování.


## <a name="next-steps"></a>Další kroky

* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure CLI](configure-routing-preference-virtual-machine-cli.md)
