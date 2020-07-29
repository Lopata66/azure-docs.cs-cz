---
title: DC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače DC-Series.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: c5074a81625f38cf94d5acd9751414bad4a9c3e8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292463"
---
# <a name="dcsv2-series"></a>DCsv2-Series


DCsv2-Series může přispět k ochraně důvěrnosti a integrity vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2288G s využitím technologie SGX. Technologie Intel Turbo zvyšuje tyto počítače až do verze 5.0 GHz. Instance řad DCsv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávy pro ochranu kódu a dat, když se používají.

Příklady případů použití: důvěrné sdílení dat s více částmi, odhalování podvodů, praní proti podvodným materiálům, blockchainí, analýzy důvěrných údajů, analýza informací a důvěrný strojové učení.

Premium Storage: podporované *

Ukládání Premium Storage do mezipaměti: podporováno *

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

* S výjimkou Standard_DC8_v2



| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | Paměť EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Virtuální počítače řady DCsv2-Series jsou [virtuální počítače generace 2](./linux/generation-2.md#creating-a-generation-2-vm) a podporují jenom `Gen2` image.
- Aktuálně k dispozici v oblastech, které jsou [zde](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)uvedeny.
- Předchozí generace důvěrných výpočetních virtuálních počítačů: [DC Series](sizes-previous-gen.md#preview-dc-series)
- Vytvoření virtuálních počítačů s DCsv2 pomocí [Azure Portal](./linux/quick-create-portal.md) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
