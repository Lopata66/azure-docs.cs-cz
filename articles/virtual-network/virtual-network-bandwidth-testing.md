---
title: Testování propustnosti sítě virtuálních počítačů Azure
titlesuffix: Azure Virtual Network
description: Naučte se testovat propustnost sítě virtuálních počítačů Azure.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "60743069"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testování šířky pásma a propustnosti (NTTTCP)

Při testování výkonu propustnosti sítě v Azure je nejvhodnější použít nástroj, který cílí na síť pro testování a minimalizuje používání dalších prostředků, které by mohly mít vliv na výkon. NTTTCP se doporučuje.

Zkopírujte nástroj do dvou virtuálních počítačů Azure se stejnou velikostí. Jeden virtuální počítač funguje jako ODESÍLATEL a druhý jako přijímač.

#### <a name="deploying-vms-for-testing"></a>Nasazení virtuálních počítačů pro testování
Pro účely tohoto testu musí být oba virtuální počítače buď ve stejné cloudové službě, nebo ve stejné skupině dostupnosti, aby bylo možné použít své interní IP adresy a vyloučit z testu nástroje pro vyrovnávání zatížení. Je možné provést test pomocí virtuální IP adresy, ale tento druh testování je mimo rozsah tohoto dokumentu.

Poznamenejte si IP adresu příjemce. Pojďme zavolat tuto IP adresu "a. b. c. r"

Poznamenejte si počet jader na virtuálním počítači. Pojďme zavolat tuto "\#NUM\_Core"

Spusťte test NTTTCP po dobu 300 sekund (nebo 5 minut) na virtuálním počítači odesílatele a na virtuálním počítači přijímače.

Tip: při prvním nastavování tohoto testu můžete vyzkoušet kratší dobu testování, abyste získali zpětnou vazbu dříve. Jakmile nástroj funguje podle očekávání, prodlužte zkušební dobu na 300 sekund, aby byly nejpřesnější výsledky.

> [!NOTE]
> Odesílatel **a** přijímač musí zadat **stejný** parametr doby trvání testu (-t).

Chcete-li otestovat jeden datový proud TCP po dobu 10 sekund:

Parametry přijímače: NTttcp-r-t 10-P 1

Parametry odesílatele: NTttcp-s 10.27.33.7-t 10-n 1-P 1

> [!NOTE]
> Předchozí vzorek by měl být použit pouze k potvrzení konfigurace. Platné příklady testování jsou pokryty dále v tomto dokumentu.

## <a name="testing-vms-running-windows"></a>Testování virtuálních počítačů s WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Získejte NTTTCP na virtuální počítače.

Stáhnout nejnovější verzi:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Nebo ho vyhledejte, pokud je přesunutý:--měl by se nejdřív narazit. <https://www.bing.com/search?q=ntttcp+download> \<

Zvažte vložení NTTTCP do samostatné složky, jako je c\\: Tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Povolení NTTTCP přes bránu Windows Firewall
Na PŘIJÍMAČi vytvořte v bráně Windows Firewall pravidlo povolení, které umožní doručení provozu NTTTCP. Je nejjednodušší povolit celý program NTTTCP podle názvu, ale Nepovolit příchozí konkrétní porty TCP.

Povolte NTttcp přes bránu Windows Firewall takto:

netsh advfirewall firewall Add rule program =\<cesta\>\\NTttcp. exe Name = "NTttcp" Protocol = any dir = in Action = Allow Enable = Yes Profile = any

Pokud jste například zkopírovali NTttcp. exe do složky "c:\\Tools", bude to příkaz: 

netsh advfirewall firewall Add rule program = c:\\Tools\\NTttcp. exe Name = "NTttcp" Protocol = any dir = in Action = Allow Enable = Yes Profile = any

#### <a name="running-ntttcp-tests"></a>Spouštění testů NTTTCP

Spusťte NTTTCP na PŘIJÍMAČi (**spusťte z cmd**, ne z PowerShellu):

NTttcp-r – m [2\*\#počet\_jader],\*, a. b. c. r-t 300

Pokud má virtuální počítač čtyři jádra a IP adresu 10.0.0.4, může to vypadat takto:

NTttcp-r – m 8,\*, 10.0.0.4-t 300


Spusťte NTTTCP na ODESILATELi (**spusťte z cmd**, ne z PowerShellu):

NTttcp-s – m 8,\*, 10.0.0.4-t 300 

Počkejte na výsledky.


## <a name="testing-vms-running-linux"></a>Testování virtuálních počítačů se systémem LINUX:

Použijte nttcp-for-Linux. Je k dispozici z<https://github.com/Microsoft/ntttcp-for-linux>

Na virtuálních počítačích se systémem Linux (ODESÍLATEL i přijímač) spusťte tyto příkazy pro přípravu NTttcp-for-Linux na virtuálních počítačích:

CentOS – instalace Gitu:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – instalace Gitu:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Vytvořit a nainstalovat na obou těchto počítačích:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Jako v příkladu Windows předpokládáme, že IP adresa PŘIJÍMAČe pro Linux je 10.0.0.4.

Spusťte NTTTCP-for-Linux na PŘIJÍMAČi:

``` bash
ntttcp -r -t 300
```

A na ODESILATELi spusťte:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Délka testu se nastaví na výchozí hodnotu 60 sekund, pokud není zadán žádný časový parametr.

## <a name="testing-between-vms-running-windows-and-linux"></a>Testování mezi virtuálními počítači se systémem Windows a LINUX:

V těchto scénářích doporučujeme povolit režim bez synchronizace, aby se test mohl spustit. To se provádí pomocí **příznaku-N** pro systém Linux a **označení-NS** pro Windows.

#### <a name="from-linux-to-windows"></a>Ze systému Linux na systém Windows:

> \<přijímače Windows:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

> \<odesílatele pro Linux:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Z Windows na Linux:

> \<přijímače pro Linux:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

> \<pro odesílatele systému Windows:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testování instancí cloudové služby:
Do ServiceDefinition. csdef je potřeba přidat následující oddíl.
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Další kroky
* V závislosti na výsledcích můžou být místo pro [optimalizaci počítačů propustnosti sítě](virtual-network-optimize-network-bandwidth.md) ve vašem scénáři.
* Přečtěte si o tom [, jak je šířka pásma přidělena virtuálním počítačům](virtual-machine-network-throughput.md) .
* Další informace o [nejčastějších dotazech k Azure Virtual Network (FAQ)](virtual-networks-faq.md)
