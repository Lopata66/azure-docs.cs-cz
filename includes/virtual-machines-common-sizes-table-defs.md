---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 03/09/2018
ms.date: 04/16/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: ad21dbb356a6630cec0a6f5658b20cb8568890f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344243"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Disky pro ukládání dat můžou fungovat v režimu s mezipamětí, nebo bez ní. Pro diskové operace s mezipamětí je možné nastavit mezipaměť na hostiteli **jen na čtení** nebo **na čtení i zápis**.  Pro diskové operace bez mezipaměti je mezipaměť na hostiteli nastavená na **žádná**.
- Pokud chcete získat nejlepší výkon pro vaše virtuální počítače, byste měli omezit počet datových disků na 2 disky na virtuální procesor.
- **Byl očekáván šířky pásma sítě** je maximální agregovaná [šířka pásma přidělená podle typu virtuálního počítače](../articles/virtual-network/virtual-machine-network-throughput.md) pro všechny síťové karty pro všechny cíle. Horní omezení nejsou garantována, ale slouží jako vodítko pro výběr správného typu virtuálního počítače pro příslušnou aplikaci. Skutečný výkon sítě bude záviset na řadě faktorů, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [Optimalizace propustnosti sítě pro Windows a Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Pro dosažení očekávaného výkonu sítě na Linuxu nebo ve Windows může být nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [Spolehlivé testování propustnosti virtuálního počítače](../articles/virtual-network/virtual-network-bandwidth-testing.md).

<!--Update_Description: update meta properties -->
