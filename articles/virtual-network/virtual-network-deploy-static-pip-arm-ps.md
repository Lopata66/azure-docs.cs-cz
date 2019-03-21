---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu – PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí Powershellu.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 422a48a3671974248e9e3258be16d3537713f762
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089994"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvoření virtuálního počítače se statickou veřejnou IP adresu. Veřejná IP adresa umožňuje komunikaci k virtuálnímu počítači z Internetu. Přiřadíte statickou veřejnou IP adresu, nikoli s dynamickou adresou, k zajištění, že adresa nikdy nemění. Další informace o [statické veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#allocation-method). Chcete-li změnit přiřazené do stávajícího virtuálního počítače z dynamické na statickou veřejnou IP adresu pro práci s privátními IP adresami, najdete v článku [přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Mají veřejné IP adresy [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a je [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejné IP adresy, které můžete použít jedno předplatné.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Ze svého místního počítače nebo s použitím Azure Cloud Shell, můžete provést následující kroky. Použití místního počítače, ujistěte se, máte [nainstalovaný Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu pole libovolný příkaz, který následuje. Cloud Shell se přihlásíte do Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazového řádku a přihlášení do Azure s využitím `Connect-AzAccount`.
2. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. Následující příklad vytvoří skupinu prostředků v oblasti Azure USA – východ:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Vytvoření virtuálního počítače s [rutiny New-AzVM](/powershell/module/az.Compute/New-azVM) příkazu. `-AllocationMethod "Static"` Možnost statickou veřejnou IP adresu přiřadí k virtuálnímu počítači. Následující příklad vytvoří virtuální počítač s Windows serverem s statické, základní SKU veřejné IP adresy s názvem *myPublicIpAddress*. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které má být použit jako přihlašovací údaje pro virtuální počítač:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Pokud je veřejná IP adresa musí být standardní SKU, budete muset [vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address), [vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface), [přiřadit veřejné IP adresy k síťovému rozhraní](virtual-network-network-interface-addresses.md#add-ip-addresses)a potom [vytvořit virtuální počítač se síťovým rozhraním](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)v samostatné kroky. Další informace o [veřejné IP adresy skladové položky](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač se přidají do fondu back-end veřejný Azure Load Balancer, skladovou Položku virtuálního počítače veřejné IP adresy musí odpovídat SKU nástroje pro vyrovnávání zatížení veřejnou IP adresu. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Zobrazit přiřazenou veřejnou IP adresu a potvrďte, že byl vytvořen jako statickou adresu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure přiřadit veřejnou IP adresu z adresy používané v oblasti, kterou jste vytvořili virtuální počítač. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Neprovádějte žádné změny nastavení IP adresy v rámci operačního systému virtuálního počítače. Operační systém je vědět o Azure veřejné IP adresy. I když nastavení privátní IP adresy můžete přidat do operačního systému, doporučujeme, pokud to neuděláte není-li nutné a nikoli až po čtení [přidejte privátní IP adresu pro operační systém](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všech prostředků, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

- Další informace o [veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statickou privátní IP adresou](virtual-network-network-interface-addresses.md#add-ip-addresses) na virtuálním počítači Azure
- Další informace o vytváření [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů
