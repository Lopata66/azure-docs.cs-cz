---
title: Převést virtuální počítač s Linuxem v Azure z nespravovaných disků na managed disks – Azure Managed Disks | Dokumentace Microsoftu
description: Postup převedení virtuálního počítače s Linuxem z nespravovaných disků na managed disks pomocí příkazového řádku Azure v modelu nasazení Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d1783890ba1b304ab658e827bfd24091e208fda4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699255"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače s Linuxem z nespravovaných disků na managed disks

Pokud máte existující virtuální počítače s Linuxem (VM), které používají nespravované disky, můžete převést virtuální počítače používat [Azure Managed Disks](../linux/managed-disks-overview.md). Tento proces převede disk s operačním systémem i všechny připojené datové disky.

V tomto článku se dozvíte, jak převést virtuální počítače pomocí rozhraní příkazového řádku Azure. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Než začnete
* Kontrola [nejčastější dotazy týkající se migrace na spravované disky](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Převést virtuální počítače s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na managed disks. (Pokud jsou vaše virtuální počítače ve skupině dostupnosti, najdete v další části.) Tento postup můžete použít k převodu virtuálních počítačů z úrovně premium (SSD) nespravované disky na spravované disky úrovně premium, nebo z úrovně standard (HDD), nespravované disky na spravované disky úrovně standard.

1. Uvolněte virtuální počítač s použitím [az vm deallocate](/cli/azure/vm). V následujícím příkladu se uvolní virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Převod virtuálního počítače na managed disks pomocí [az vm, převést](/cli/azure/vm). Následující proces převede virtuální počítač s názvem `myVM`, včetně disk s operačním systémem a všechny datové disky:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Spusťte virtuální počítač po převodu na spravované disky pomocí [az vm start](/cli/azure/vm). Následující příklad spustí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních počítačů ve skupině dostupnosti

Pokud virtuální počítače, které chcete převést na spravované disky jsou ve skupině dostupnosti, musíte nejprve převést dostupnosti na spravované skupině dostupnosti.

Všechny virtuální počítače ve skupině dostupnosti musí být napřed zrušit přidělení převést skupinu dostupnosti. Plán pro převod všech virtuálních počítačů na managed disks po samotné skupinu dostupnosti se převedly na spravované skupině dostupnosti. Potom spusťte všechny virtuální počítače a pokračovat v činnosti, jako obvykle.

1. Seznam všech virtuálních počítačů ve skupině dostupnosti s využitím [az vm skupinu dostupnosti seznamu](/cli/azure/vm/availability-set). Následující příklad zobrazí seznam všech virtuálních počítačů ve skupině dostupnosti s názvem `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Uvolnit všechny virtuální počítače s použitím [az vm deallocate](/cli/azure/vm). V následujícím příkladu se uvolní virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Převést skupině dostupnosti s využitím [az vm skupinu dostupnosti převést](/cli/azure/vm/availability-set). Následující příklad převádí skupinu dostupnosti `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Převést všechny virtuální počítače na managed disks pomocí [az vm, převést](/cli/azure/vm). Následující proces převede virtuální počítač s názvem `myVM`, včetně disk s operačním systémem a všechny datové disky:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Spustit všechny virtuální počítače po převodu na spravované disky s použitím [az vm start](/cli/azure/vm). Následující příklad spustí virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Další postup
Další informace o možnostech úložiště najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md).
