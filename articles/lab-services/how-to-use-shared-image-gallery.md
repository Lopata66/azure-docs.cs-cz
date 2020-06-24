---
title: Použití Galerie sdílených imagí v Azure Lab Services | Microsoft Docs
description: Naučte se, jak nakonfigurovat účet testovacího prostředí pro použití Galerie sdílených imagí, aby uživatel mohl sdílet image s ostatními a jiný uživatel může image použít k vytvoření virtuálního počítače šablony v testovacím prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: spelluru
ms.openlocfilehash: 6e5a893a66d4b69bd9f05b7ee473ab0bdf86889b
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895819"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Použití Galerie sdílených imagí v Azure Lab Services
V tomto článku se dozvíte, jak můžou pedagogi/správci testovacího prostředí ukládat image virtuálního počítače šablony do [sdílené Image Galerie](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) , aby ji mohli používat ostatní k vytváření Labs. 

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, které tato funkce podporuje: 

- Správce účtu testovacího prostředí připojí galerii sdílených imagí k účtu testovacího prostředí a nahraje image do galerie sdílených imagí mimo kontext testovacího prostředí. Pak tvůrci testovacího prostředí můžou pomocí této image z Galerie sdílených imagí vytvořit Labs. 
- Správce účtu testovacího prostředí připojí galerii sdílených imagí k účtu testovacího prostředí. Tvůrce testovacího prostředí (instruktor) uloží přizpůsobenou image testovacího prostředí do galerie sdílených imagí. Ostatní tvůrci testovacích prostředí pak můžou tuto image vybrat z Galerie sdílených imagí a vytvořit šablonu pro jejich cvičení. 

    Když se obrázek uloží do galerie sdílených imagí, Azure Lab Services replikuje uložený obrázek do jiných oblastí dostupných ve stejné [geografické](https://azure.microsoft.com/global-infrastructure/geographies/)oblasti. Zajišťuje, aby byla bitová cvičení dostupná pro testovací prostředí vytvořená v jiných oblastech ve stejné geografické oblasti. Při ukládání imagí do galerie sdílených imagí se vyskytnou další náklady, včetně nákladů na všechny replikované bitové kopie. Tato cena je oddělená od nákladů na využití Azure Lab Services. Další informace o cenách Galerie sdílených imagí najdete v tématu [Galerie sdílených imagí – fakturace]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Požadavky
- Pomocí [Azure PowerShell](../virtual-machines/windows/shared-images.md) nebo rozhraní příkazového [řádku Azure](../virtual-machines/linux/shared-images.md)můžete vytvořit galerii sdílených imagí.
- Připojili jste galerii sdílených imagí k účtu testovacího prostředí. Podrobné pokyny najdete v tématu [Postup připojení nebo odpojení Galerie sdílených imagí](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložení obrázku do galerie sdílených imagí
Po připojení Galerie sdílených imagí může správce účtu testovacího prostředí nebo Educator Uložit image do galerie sdílených imagí, aby ji mohli použít jiní pedagogy. 

1. Na stránce **Šablona** testovacího prostředí vyberte **exportovat do galerie sdílených imagí** na panelu nástrojů.

    ![Tlačítko Uložit obrázek](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. V dialogovém okně **exportovat do sdílené bitové kopie** zadejte **název bitové kopie**a pak vyberte **exportovat**. 

    ![Dialog Exportovat do galerie sdílených imagí](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Průběh této operace můžete zobrazit na stránce **šablony** . Tato operace může nějakou dobu trvat. 

    ![Probíhá export.](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Po úspěšném provedení operace exportu se zobrazí následující zpráva:

    ![Export dokončen](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Po uložení image do galerie sdílených imagí můžete tuto image použít z Galerie při vytváření jiného testovacího prostředí. Můžete také nahrát obrázek do galerie sdílených imagí mimo kontext testovacího prostředí. Další informace najdete v tématu [Přehled Galerie sdílených imagí](../virtual-machines/windows/shared-images.md). 

    > [!IMPORTANT]
    > Když [uložíte obrázek šablony testovacího prostředí](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) v Azure Lab Services do galerie sdílených imagí, obrázek se nahraje do galerie jako **speciální obrázek**. [Specializované obrázky](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) udržují informace specifické pro počítač a profily uživatelů. Do galerie můžete i nadále přímo nahrát zobecněnou image mimo Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použití obrázku z Galerie sdílených imagí
Educator může vybrat vlastní image, která je k dispozici v galerii sdílených imagí pro šablonu během nového vytváření testovacího prostředí.

![Použití image virtuálního počítače z Galerie](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Můžete vytvořit šablonu virtuálního počítače na základě **zobecněných** i **specializovaných** imagí v Azure Lab Services. 


## <a name="next-steps"></a>Další kroky
Další informace o galeriích sdílených imagí najdete v tématu [Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries).
