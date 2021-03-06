---
title: Přesunutí virtuálního počítače Azure do různých oblastí
description: Přečtěte si, jak přesunout virtuální počítač s autosprávou napříč oblastmi.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e880992143bf79a5a99cc9830957f83167b6b46
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808186"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Přesunout virtuální počítač Azure automanage do jiné oblasti
Tento článek popisuje, jak zajistit, aby byla při přesunu do jiné oblasti zapnutá funkce automanage na virtuálním počítači. Virtuální počítače můžete chtít přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, abyste splnili požadavky na interní zásady a požadavky zásad správného řízení nebo v reakci na požadavky na plánování kapacity. Tyto virtuální počítače, které přesouváte, mohou být aktuálně spravovány a můžete chtít, aby byly po přesunu nadále spravovány.

## <a name="prerequisites"></a>Požadavky
* Ujistěte se, že se vaše cílová oblast [podporuje prostřednictvím automanage](./automanage-virtual-machines.md#prerequisites).
* Zajistěte, aby vaše oblast pracovního prostoru Log Analytics, oblast účtu služby Automation a vaše cílová oblast byly všemi oblastmi, které [tady](https://docs.microsoft.com/azure/automation/how-to/region-mappings)mapování oblastí podporuje.

## <a name="prepare-your-automanaged-vms-for-moving"></a>Příprava virtuálních počítačů, které se spravují, pro přesun
Zakažte na vašich autospravovaných virtuálních počítačích automanage. To můžete provést tak, že v okně pro správu vyberete své virtuální počítače a v okně pro správu kliknete na **zakázat správu** .

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Přesuňte své spravované virtuální počítače a znovu povolte automanage.
Podrobnosti o tom, jak přesunout vaše virtuální počítače, najdete v tomto [článku](https://docs.microsoft.com/azure/resource-mover/tutorial-move-region-virtual-machines).

Po přesunutí virtuálních počítačů do různých oblastí můžete znovu povolit opětovnou správu. Podrobnosti jsou k dispozici [zde](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Další kroky
* [Další informace o službě Azure automanage](./automanage-virtual-machines.md)
* [Zobrazit nejčastější dotazy týkající se služby Azure automanage](./faq.md)