---
title: Nastavení testovacího prostředí s grafickými procesory v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak nastavit testovací prostředí s virtuálními počítači GPU (Graphics Processing Unit).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 63b7a8c58bb6d277233268ed4fc0bc870fdd337f
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85443396"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Nastavení testovacího prostředí s virtuálními počítači GPU

V tomto článku se dozvíte, jak provádět následující úlohy:

- Vyberte si mezi *vizualizacemi* a *výpočetními* jednotkami grafiky (GPU).
- Ujistěte se, že jsou nainstalované příslušné ovladače GPU.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Výběr mezi vizualizací a výpočetními velikostmi GPU
Na první stránce Průvodce vytvořením testovacího prostředí vyberte v rozevíracím seznamu **Velikost virtuálního počítače** , kterou potřebujete, a vyberte velikost virtuálních počítačů potřebných pro vaši třídu.  

![Snímek obrazovky s podoknem nové laboratoře pro výběr velikosti virtuálního počítače](./media/how-to-setup-gpu/lab-gpu-selection.png)

V tomto procesu máte možnost vybrat buď **vizualizaci** , nebo **výpočetní** GPU.  Je důležité zvolit typ GPU, který je založený na softwaru, který budou vaši studenti používat.  

Jak je popsáno v následující tabulce, *Výpočet* velikosti GPU procesorů je určený pro aplikace náročné na výpočetní výkon.  Například [obsáhlý Learning v typu třídy zpracování přirozeného jazyka](./class-type-deep-learning-natural-language-processing.md) používá velikost **malého GPU (COMPUTE)** .  Výpočetní procesor je vhodný pro tento typ třídy, protože studenti využívají architektury hloubkového učení a nástroje, které jsou k dispozici v [Data Science Virtual Machine image](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) , k výuce modelů pro obsáhlý Learning s velkými sadami dat.

| Velikost | Cores | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Malý grafický procesor (COMPUTE) | -&nbsp;6 &nbsp; jader<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Tato velikost nejlépe vyhovuje aplikacím náročným na počítač, jako je například umělá logika (AI) a obsáhlý Learning. |

Velikosti GPU pro *vizualizaci* jsou určené pro aplikace náročné na grafiku.  Například [typ třídy SolidWorks Engineering](./class-type-solidworks.md) ukazuje použití velikosti **malého GPU (vizualizace)** .  Grafický procesor vizualizace je vhodný pro tento typ třídy, protože studenti pracují s prostředím SolidWorks 3D Computer-to design (CAD) pro modelování a vizualizaci tuhých objektů.

| Velikost | Cores | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Malý grafický procesor (vizualizace) | -&nbsp;6 &nbsp; jader<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní a kódování využívající architektury, jako je OpenGL a DirectX. |
| Střední GPU (vizualizace) | -&nbsp;12 &nbsp; jader<br>-&nbsp;112 &nbsp; GB &nbsp; RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní a kódování využívající architektury, jako je OpenGL a DirectX. |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Ujistěte se, že jsou nainstalované příslušné ovladače GPU.
Pokud chcete využít možnosti GPU vašich virtuálních počítačů v testovacím prostředí, ujistěte se, že jsou nainstalované příslušné ovladače GPU.  Když v Průvodci vytvořením testovacího prostředí vyberete velikost virtuálního počítače GPU, můžete vybrat možnost **nainstalovat ovladače GPU** .  

![Snímek obrazovky s "novým testovacím prostředím", který ukazuje možnost nainstalovat ovladače GPU](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Jak je znázorněno na předchozím obrázku, tato možnost je ve výchozím nastavení povolená, což zajistí, že jsou nainstalované *nejnovější* ovladače pro typ GPU a image, kterou jste vybrali.
- Když vybíráte velikost *výpočetního* GPU, virtuální počítače v testovacím prostředí jsou napájené pomocí GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  V tomto případě jsou nainstalovány nejnovější ovladače [COMPUTE Unified Device Architecture (CUDA)](https://www.nvidia.com/object/io_69526.html) , což umožňuje vysoce výkonné výpočetní prostředí.
- Když vyberete velikost GPU pro *vizualizaci* , vaše testovací virtuální počítače jsou napájené technologií GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) a [technologií Grid](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  V takovém případě jsou nainstalovány nejnovější ovladače mřížky, což umožňuje použití aplikací náročných na grafiku.

### <a name="install-the-drivers-manually"></a>Ruční instalace ovladačů
Možná budete muset nainstalovat jinou verzi ovladače než nejnovější verzi.  V této části se dozvíte, jak ručně nainstalovat vhodné ovladače v závislosti na tom, jestli používáte *výpočetní* procesor (GPU) nebo grafický procesor *vizualizace* .

#### <a name="install-the-compute-gpu-drivers"></a>Instalace ovladačů COMPUTE GPU

Chcete-li ručně nainstalovat ovladače pro velikost procesoru výpočetní GPU, postupujte následovně:

1. V Průvodci vytvořením testovacího prostředí při [vytváření testovacího](./how-to-manage-classroom-labs.md)prostředí zakažte nastavení **nainstalovat ovladače GPU** .

1. Po vytvoření testovacího prostředí se připojte k virtuálnímu počítači šablony a nainstalujte příslušné ovladače.

   ![Snímek obrazovky se stránkou ke stažení ovladačů NVIDIA](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. V prohlížeči přejdete na stránku se [soubory ke stažení ovladačů NVIDIA](https://www.nvidia.com/Download/index.aspx).  
   b. Nastavte **typ produktu** na **Tesla**.  
   c. Nastavte **řadu produktů** na **řady k**.  
   d. Nastavte **operační systém** podle typu základní image, kterou jste vybrali při vytváření testovacího prostředí.  
   e. Nastavte **CUDA Toolkit** na verzi ovladače CUDA, kterou potřebujete.  
   f. Vyberte **Hledat** , chcete-li vyhledat ovladače.  
   například Vyberte **Stáhnout** a stáhněte instalační program.  
   h. Spusťte instalační program, aby byly ovladače nainstalované na virtuálním počítači šablony.  
1. Podle pokynů v části [ověření nainstalovaných ovladačů](how-to-setup-lab-gpu.md#validate-the-installed-drivers) ověřte, jestli jsou ovladače správně nainstalované. 
1. Po instalaci ovladačů a dalšího softwaru, který je pro vaši třídu nutný, vyberte **publikovat** a vytvořte virtuální počítače studentů.

> [!NOTE]
> Pokud používáte bitovou kopii systému Linux, po stažení instalačního programu nainstalujte ovladače podle pokynů v tématu [install CUDA Drivers on Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Instalace ovladačů GPU pro vizualizaci

Chcete-li ručně nainstalovat ovladače pro velikost grafického procesoru vizualizace, postupujte následovně:

1. V Průvodci vytvořením testovacího prostředí při [vytváření testovacího](./how-to-manage-classroom-labs.md)prostředí zakažte nastavení **nainstalovat ovladače GPU** .
1. Po vytvoření testovacího prostředí se připojte k virtuálnímu počítači šablony a nainstalujte příslušné ovladače.
1. Nainstalujte ovladače mřížky poskytované Microsoftem na VIRTUÁLNÍm počítači šablony podle pokynů pro váš operační systém:
   -  [Ovladače Windows NVIDIA GRID](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Ovladače pro MŘÍŽKu NVIDIA pro Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Restartujte šablonu virtuálního počítače.
1. Podle pokynů v části [ověření nainstalovaných ovladačů](how-to-setup-lab-gpu.md#validate-the-installed-drivers) ověřte, jestli jsou ovladače správně nainstalované.
1. Po instalaci ovladačů a dalšího softwaru, který je pro vaši třídu nutný, vyberte **publikovat** a vytvořte virtuální počítače studentů.

### <a name="validate-the-installed-drivers"></a>Ověřit nainstalované ovladače
Tato část popisuje, jak ověřit, jestli jsou ovladače GPU správně nainstalované.

#### <a name="windows-images"></a>Bitové kopie systému Windows
1.  Postupujte podle pokynů v části "ověřit instalaci ovladače" v tématu [instalace ovladačů NVIDIA GPU pro virtuální počítače řady N-Series s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Pokud používáte grafický procesor *vizualizace* , můžete také:
    - Zobrazení a úprava nastavení GPU v Ovládacích panelech NVIDIA. Provedete to tak, že v **Ovládacích panelech systému Windows**vyberete **hardware**a pak vyberete **ovládací panel NVIDIA**.

      ![Snímek obrazovky s ovládacím panelem Windows zobrazující odkaz na ovládací panel NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Zobrazení výkonu GPU pomocí **Správce úloh**.  Provedete to tak, že vyberete kartu **výkon** a potom vyberete možnost **GPU** .

       ![Snímek obrazovky, na které se zobrazuje karta výkon GPU Správce úloh](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Nastavení ovládacího panelu NVIDIA lze použít pouze pro grafické procesory *vizualizace* .  Pokud se pokusíte otevřít ovládací panel NVIDIA pro výpočetní procesor (GPU), zobrazí se následující chyba: "nastavení zobrazení NVIDIA nejsou k dispozici.  V tuto chvíli nepoužíváte zobrazení připojené k GPU NVIDIA. "  Podobně informace o výkonu GPU ve Správci úloh jsou k dispozici pouze pro grafické procesory vizualizace.

#### <a name="linux-images"></a>Image Linux
Postupujte podle pokynů v části "ověřit instalaci ovladače" v tématu [instalace ovladačů NVIDIA GPU pro virtuální počítače řady N-Series se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Vytváření a správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md)
- [Typ třídy SolidWorks počítačového návrhu (CAD)](class-type-solidworks.md)
- [Typ třídy MATLAB (maticová laboratoř)](class-type-matlab.md)



