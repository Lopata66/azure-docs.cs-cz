---
title: Stažení virtuálního pevného disku se systémem Linux z Azure
description: Stažení virtuálního pevného disku se systémem Linux pomocí rozhraní příkazového řádku Azure a Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: f0c5e51665b4b1d31e7d2b3e25e7be31b481d0d9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203163"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stažení virtuálního pevného disku se systémem Linux z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) pro Linux z Azure pomocí Azure Portal. 

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pro stažení virtuálního pevného disku je nutné zastavit virtuální počítač. 

1.  Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2.  V nabídce vlevo vyberte **Virtual Machines**.
3.  V seznamu vyberte virtuální počítač.
4.  Na stránce pro virtuální počítač vyberte **zastavit**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Zobrazuje tlačítko nabídky k zastavení virtuálního počítače.":::

## <a name="generate-sas-url"></a>Vygenerovat adresu URL SAS

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1. V nabídce stránky pro virtuální počítač vyberte **disky**.
2. Vyberte disk s operačním systémem pro virtuální počítač a pak vyberte **exportovat disk**.
1. V případě potřeby aktualizujte hodnotu adresy URL tak, že **vyprší za (sekundy)** a získáte tak dostatek času na dokončení stahování. Výchozí hodnota je 3600 sekund (jedna hodina).
3. Vyberte **generovat adresu URL**.
 
      
## <a name="download-vhd"></a>Stáhnout VHD

1.  V části vygenerovaná adresa URL vyberte **Stáhnout soubor VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Zobrazuje tlačítko ke stažení virtuálního pevného disku.":::

2.  Možná budete muset vybrat **Uložit** v prohlížeči a zahájit stahování. Výchozí název souboru VHD je *abcd*.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát a vytvořit virtuální počítač se systémem Linux z vlastního disku pomocí Azure CLI](upload-vhd.md). 
- [Spravujte Azure na discích Azure CLI](tutorial-manage-disks.md).
