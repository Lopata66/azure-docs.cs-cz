---
title: Úvod do IP tok ověřit ve službě Azure Network Watcher | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled nástroje Network Watcher IP tok ověřit funkce
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709587"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Úvod do IP tok ověřit ve službě Azure Network Watcher

Ověření toku protokolu IP kontroly, jestli je paket povolený nebo zakázaný do nebo z virtuálního počítače. Informace se skládá z směr, protokol, místní IP Adrese, vzdálenou IP adresu, místní port a vzdálených portů. Pokud paketu je skupina zabezpečení, je vrácen název pravidla, které paket zamítlo. Při jakékoli zdrojové nebo cílové IP adresy mohou být vybrána, ověření toku protokolu IP umožňuje správcům rychle diagnostikovat problémy s připojením z nebo internet a z nebo v místním prostředí.

Ověření toku protokolu IP přihlíží pravidla pro všechny skupiny zabezpečení sítě (Nsg) použita k síťovému rozhraní, jako je například na síťový adaptér se podsítě nebo virtuální počítač Tok potom ověření podle nakonfigurovaného nastavení do nebo z rozhraní sítě. Ověření toku protokolu IP je užitečné v potvrzení, pokud se pravidlo skupiny zabezpečení sítě blokuje příchozí a odchozí provoz do nebo z virtuálního počítače.

Instance Network Watcher je potřeba vytvořit ve všech oblastech, které máte v úmyslu spustit IP tok ověřit. Network Watcher je místní služba a může být ji spustili jenom s prostředky ve stejné oblasti. Instance používané nemá vliv na výsledky IP tok ověřit, jak přidružené k síťové KARTĚ nebo podsítě trasy je stále vrátit.

![1][1]

## <a name="next-steps"></a>Další postup

Naleznete v následujícím článku se dozvíte, pokud je paket povolený nebo zakázaný pro konkrétní virtuální počítač na portálu. [Zkontrolujte, jestli je povolený provoz na virtuální počítač s IP tok ověřit pomocí portálu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

