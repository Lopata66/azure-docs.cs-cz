---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122105"
---
Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

Nástroj pro vyrovnávání zatížení můžete konfigurovat k těmto činnostem:

* Vyrovnávání zatížení příchozích přenosů z internetu do virtuálních počítačů. Nástroj pro vyrovnávání zatížení v tomto scénáři označujeme jako [internetový nástroj pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internet-overview.md).
* Vyrovnávání zatížení přenosů mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními počítači a virtuálními počítači ve virtuální síti mezi různými místy. Nástroj pro vyrovnávání zatížení v tomto scénáři označujeme jako [interní nástroj pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internal-overview.md).
* Předávání externích přenosů konkrétní instanci virtuálního počítače
