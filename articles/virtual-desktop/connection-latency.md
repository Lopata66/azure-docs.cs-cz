---
title: Latence připojení uživatele k virtuální ploše Windows – Azure
description: Latence připojení pro uživatele virtuálních klientů Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607397"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Určení latence připojení uživatele ve virtuální ploše Windows

Virtuální klient Windows je globálně dostupný. Správci můžou vytvářet virtuální počítače v libovolné oblasti Azure, které chtějí. Latence připojení se bude lišit v závislosti na umístění uživatelů a virtuálních počítačů. Služba Windows Virtual Desktop Service se průběžně zavede do nových geografických oblastí, aby se zlepšila latence. 
 
[Nástroj Estimator Experience pro virtuální počítače s Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) vám pomůže určit nejlepší umístění pro optimalizaci latence vašich virtuálních počítačů. Doporučujeme, abyste tento nástroj používali každých dvou až tří měsíců, abyste se ujistili, že se optimální umístění nezměnilo, protože se virtuální plocha Windows vyřadí do nových oblastí. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Virtuální plocha Windows používá Traffic Manager Azure, která kontroluje umístění serveru DNS uživatele, aby našel nejbližší instanci služby Virtual Desktop systému Windows. Před volbou umístění pro virtuální počítače doporučujeme správcům zkontrolovat umístění serveru DNS daného uživatele.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit nejlepší umístění pro optimální latenci, přečtěte si [Nástroj Estimator pro Windows Virtual Desktop Experience](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Cenové tarify najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pokud chcete začít s nasazením virtuálního počítače s Windows, přečtěte si [náš kurz](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).