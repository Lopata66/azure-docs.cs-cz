---
title: Azure velikosti virtuálních počítačů s Windows – GPU | Dokumentace Microsoftu
description: Seznamy různé GPU optimalizované velikosti, které jsou k dispozici pro Windows virtual machines v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 57a19b11d7aead6f67f411b75b046a9238cf7c12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055231"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU optimalizované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Windows, musí být nainstalována ovladačů NVIDIA GPU. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) nainstaluje vhodných ovladačů NVIDIA CUDA nebo mřížky na virtuální počítač řady N-series. Instalovat ani spravovat rozšíření pomocí webu Azure portal nebo nástrojů, jako je šablon Azure Resource Manageru nebo Azure Powershellu. Najdete v článku [dokumentaci rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) pro podporované operační systémy a kroky nasazení. Obecné informace o rozšíření virtuálních počítačů najdete v tématu [rozšíření virtuálního počítače Azure a funkce](../extensions/overview.md).

Pokud zvolíte ruční instalace ovladačů NVIDIA GPU, přečtěte si téma [instalace ovladačů v instancích N-series GPU pro Windows](n-series-driver-setup.md) pro podporované operační systémy, ovladačů a postup instalace a ověření.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup
Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.

