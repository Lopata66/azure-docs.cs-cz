---
title: Velikosti virtuálních počítačů Windows v Azure | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro Windows virtual machines v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: jonbeck
ms.openlocfilehash: 78b0de43652aba20563e2712e249e18ccd18c407
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710083"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Velikosti virtuálních počítačů Windows v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které lze použít ke spouštění aplikací pro Windows a úloh. Také poskytuje důležité informace o nasazení je potřeba vědět při plánování použití těchto prostředků.  Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| type                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Vyvážený poměr procesorů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)        | Fsv2           | Vysoký poměr procesorů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery.        |
| [Optimalizované z hlediska paměti](sizes-memory.md)         | Esv3, Ev3, Mv2, M, DSv2, Dv2  | Vysoký poměr paměti na procesor. Velmi vhodné pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](sizes-storage.md)        | Lsv2                | Vysoká propustnost disku a vstupně-výstupních operací ideální pro velké objemy dat, SQL, NoSQL databáze, datových skladů a velké transakční databáze.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3 (Preview) | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa, jakož i model trénování a odvozování (ND) s obsáhlým learningem. K dispozici s jedním nebo několika grafickými procesory.       |
| [Vysokovýkonné výpočetní prostředí](sizes-hpc.md) | HB, HC,  H | Naše nejrychlejší a procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). |


<br> 

- Informace o různých velikostí cenách najdete v tématu [ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Obecná omezení na virtuálních počítačích Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md).
- Náklady na úložiště se počítají samostatně na základě využitých stránek v účtu úložiště. Podrobnosti najdete [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.


## <a name="rest-api"></a>REST API

Informace o použití rozhraní REST API k dotazování pro velikosti virtuálních počítačů naleznete v následujících tématech:

- [Seznam dostupných velikostí virtuálních počítačů pro změnu velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Seznam dostupných velikostí virtuálních počítačů pro předplatné](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Seznam dostupných velikostí virtuálních počítačů ve skupině dostupnosti](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Přečtěte si další informace o výpočetní výkon pro virtuální počítače s Windows pomocí [CoreMark srovnávacích](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další postup

Další informace o různých velikostech virtuálních počítačů, které jsou k dispozici:
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)
- [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- Zkontrolujte, [předchozí generace](sizes-previous-gen.md) stránky A Standard, Dv1 (D1-4 a D11-14 v1) a A8-A11-series




