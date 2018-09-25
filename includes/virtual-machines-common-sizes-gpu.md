---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/10/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: e9f88a018ba0f40d41bf7ad17a74850a9edf8ae6
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043845"
---
GPU optimalizované virtuálních počítačů velikosti jsou specializované virtuální počítače s jedním nebo několika grafickými procesory NVIDIA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní prostředky, náročné na grafiku a vizualizace. Tento článek obsahuje informace o počtu a typu GPU, virtuálních procesorů, datové disky a síťové adaptéry. Úložiště propustnost a šířku pásma sítě jsou také zahrnuté pro jednotlivé velikosti v této skupině. 

* **Síťový adaptér, NCv2, NCv3 a ND** velikostí optimalizovaných pro aplikace náročné na výpočetní výkon i síťové prostředky a algoritmy. Některé příklady jsou aplikace pro systém CUDA a OpenCL a simulace, AI a hloubkového učení. Řady NCv3-series se zaměřuje na vysoce výkonné výpočetní úlohy s NVIDIA Tesla V100 GPU.  Řada ND-Series se zaměřuje na scénáře odvozování a trénování pro účely hloubkového učení. Využívá grafické procesory NVIDIA Tesla P40.
* **NV a NVv2** velikosti jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hry, kódování a scénáře VDI s využitím architektur typu OpenGL a DirectX.  Tyto virtuální počítače se zálohují na NVIDIA Tesla M60 GPU.


## <a name="nc-series"></a>NC-Series

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

Virtuální počítače řady NC-series využívají [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty. Uživatel může zpracovávejte prostřednictvím dat rychleji s využitím CUDA pro zkoumání aplikace energie, simulace selhání, ray trasovaných vykreslování, obsáhlý learning a další. Konfiguraci NC24r poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.


| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA

## <a name="ncv2-series"></a>NCv2-Series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NCv2-series využívají [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU. Tyto grafické procesory poskytují více než 2 x větší výpočetní výkon řady NC-Series. Zákazníci můžou využít tyto aktualizované grafické procesory pro tradiční úlohy HPC, jako je modelování ložisek, DNA sekvencování, proteinové analýzy, simulace typu Monte Carlo a další. Konfigurace v2 NC24rs poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.

> [!IMPORTANT]
> Pro tuto řadu velikostí kvóty virtuálních procesorů (jader) v rámci vašeho předplatného je zpočátku nastaven 0 v jednotlivých oblastech. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

## <a name="ncv3-series"></a>NCv3-series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NCv3-series využívají [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU. Tyto grafické procesory poskytují výpočetní výkon řady NCv2-series 1, 5násobek. Zákazníci můžou využít tyto aktualizované grafické procesory pro tradiční úlohy HPC, jako je modelování ložisek, DNA sekvencování, proteinové analýzy, simulace typu Monte Carlo a další. Konfigurace v3 NC24rs poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.

> [!IMPORTANT]
> Pro tuto řadu velikostí kvóty virtuálních procesorů (jader) v rámci vašeho předplatného je zpočátku nastaven 0 v jednotlivých oblastech. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = jedna karta V100.

*Podpora RDMA

## <a name="nd-series"></a>ND-Series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače ND-series jsou novým rozšířením řady GPU pro úlohy AI a hloubkového učení. Nabízejí mimořádný výkon por školení a odvozování. Instance ND využívají [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU. Tyto instance poskytují špičkový výkon pro jednoduchou přesnost s plovoucí desetinnou čárkou bodu operace pro úlohy AI využívající Microsoft Cognitive Toolkit, TensorFlow, Caffe a další architektury. Řada ND-Series také nabízí mnohem větší paměť GPU (24 GB) a umožňuje zpracovat mnohem větší modely neuronových sítí. Stejně jako NC-series i řada ND-series nabízí konfiguraci se sekundární síť s nízkou latencí a vysokou propustností přes RDMA a připojení InfiniBand, takže můžete spouštět rozsáhlé školicí úlohy využívající velký počet GPU.

> [!IMPORTANT]
> Pro tuto řadu velikostí je 0 zpočátku nastaven kvóty virtuálních procesorů (jader) na oblast v rámci vašeho předplatného. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

## <a name="nv-series"></a>NV-Series

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

Virtuální počítače řad NV využívají [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a NVIDIA GRID technologie pro desktop accelerated aplikací i virtuálních desktopů kde Zákazníci mají možnost vizualizovat svoje data nebo simulace. Uživatelé budou moci vizualizovat své graficky náročné pracovní postupy na instancích NV na špičkové grafické možnosti a navíc spouštět úlohy s jednoduchou přesností například kódování a vykreslování. 

Každý GPU v instancích NV obsahuje licenci mřížky. Tuto licenci získáte flexibilitu používat NV instance jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů můžete připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet | Virtuálních pracovních stanic | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.

## <a name="nvv2-series-preview"></a>NVv2-series (ve verzi Preview)

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NVv2 využívají [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a NVIDIA GRID technologii procesorů Intel Broadwell. Tyto virtuální počítače je určená pro urychlení GPU grafických aplikací a virtuálních ploch, kde chcete zákazníkům vizualizovat svoje data, simulovat výsledky k zobrazení, pracovat na CAD nebo vykreslování a datového proudu obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy s jednoduchou přesností, jako je kódování a vykreslování. NVv2 virtuálních počítačů podporují službu Premium Storage a dodávají s dvakrát systémové paměti (RAM) ve srovnání s jeho předchůdce NV-series.  

Každý GPU v instancích NVv2 obsahuje licenci mřížky. Tuto licenci získáte flexibilitu používat NV instance jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů můžete připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Max. datových disků | Maximální počet síťových karet | Virtuálních pracovních stanic | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 8 | 4 | 100 |

1 GPU = polovina karty M60.

 
