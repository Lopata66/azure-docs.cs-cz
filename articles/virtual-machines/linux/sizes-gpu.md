---
title: Azure velikostí virtuálního počítače s Linuxem – hardwarově akcelerovanou compute | Dokumentace Microsoftu
description: Seznamy různé GPU optimalizované velikosti, které jsou k dispozici pro virtuální počítače s Linuxem v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 3c59c73914a78374da0b8d2f9aeb18b9005e00ac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070016"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU optimalizované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Linuxem, musí být nainstalována ovladačů NVIDIA GPU. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) nainstaluje vhodných ovladačů NVIDIA CUDA nebo mřížky na virtuální počítač řady N-series. Instalovat ani spravovat rozšíření pomocí webu Azure portal nebo nástrojů, jako jsou šablony Azure Resource Manageru nebo rozhraní příkazového řádku Azure. Zobrazit [dokumentaci rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) podporovaných distribucích a kroky nasazení. Obecné informace o rozšíření virtuálních počítačů najdete v tématu [rozšíření virtuálního počítače Azure a funkce](../extensions/overview.md).

Pokud zvolíte ruční instalace ovladačů NVIDIA GPU, přečtěte si téma [instalace ovladačů v instancích N-series GPU pro Linux](n-series-driver-setup.md) pro podporované distribuce, ovladačů a postup instalace a ověření.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* X byste neměli instalovat server nebo jiné systémy, které používají `Nouveau` ovladač na virtuální počítače s Ubuntu síťového adaptéru. Před instalací ovladače NVIDIA GPU, je nutné zakázat `Nouveau` ovladače.  

## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup
Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.