---
title: Vytvoření virtuální sítě – rychlý start – Azure PowerShell
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.openlocfilehash: 57c3b5099c24151794b27f4aeec7845495a4630a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670398"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Rychlý start: Vytvoření virtuální sítě pomocí Powershellu

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě mezi sebou a s Internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Potom z Internetu připojit k virtuálním počítačům a komunikovat soukromě mezi přes virtuální síť.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně místo toho, v tomto rychlém startu je potřeba použít modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Zobrazit [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) pro instalaci a informace o upgradu.

Nakonec, pokud používáte PowerShell místně, bude také potřeba spustit `Connect-AzAccount`. Tento příkaz vytvoří připojení k Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvořte skupinu prostředků a virtuální sítě

Existuje několik kroků, které budete muset provede získat skupinu prostředků a virtuální síť nakonfigurovanou.

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte virtuální síť, je nutné vytvořit skupinu prostředků k hostování ve virtuální síti. Vytvořte skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* v *EastUS* umístění:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidání podsítě

Azure nasadí prostředků do podsítě ve virtuální síti, takže je třeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *výchozí* s [přidat AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružení podsítě do virtuální sítě

Můžete tuto konfiguraci podsítě zapište do virtuální sítě pomocí [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvoření prvního virtuálního počítače s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Zadejte uživatelské jméno a heslo pro virtuální počítač:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

`-AsJob` Možnost se virtuální počítač vytvoří na pozadí. Můžete pokračovat k dalšímu kroku.

Když Azure zahájí vytváření virtuálního počítače na pozadí, které ozveme vypadat přibližně takto:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Vytvoření druhého virtuálního počítače pomocí tohoto příkazu:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Budete muset vytvořit další uživatele a heslo. Azure trvá několik minut, vytvoření virtuálního počítače.

> [!IMPORTANT]
> Nemusíte pokračovat dalším krokem, dokud Azure nedokončí.  Budete vědět, že se provádí, když se vrátí výstup do prostředí PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Použití [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) vrátí veřejnou IP adresu virtuálního počítače. V tomto příkladu vrátí veřejnou IP adresu *myVm1* virtuálního počítače:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Otevřete příkazový řádek v místním počítači. Spusťte příkaz `mstsc`. Nahraďte `<publicIpAddress>` s veřejnou IP adresou vrácenou poslední krok:

> [!NOTE]
> Pokud běží tyto příkazy z příkazového řádku Powershellu v místním počítači a že používáte PowerShell Az modulu verze 1.0 nebo novější, můžete pokračovat v tomto rozhraní.

```cmd
mstsc /v:<publicIpAddress>
```

Remote Desktop Protocol (*RDP*) soubor se stáhne do vašeho počítače a otevře relaci vzdálené plochy.

1. Pokud se zobrazí výzva, vyberte **Připojit**.

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

    > [!NOTE]
    > Budete muset vybrat **víc možností** > **použít jiný účet**, a zadejte přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Může se zobrazit upozornění certifikátu. Pokud vyberete, **Ano** nebo **pokračovat**.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše *myVm1*, otevřete prostředí PowerShell.

1. Zadejte `ping myVm2`.

    Zobrazí se něco jako toto zpět:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Příkaz ping selže, protože používá ovládací prvek zpráva ICMP (Internet Protocol). Ve výchozím nastavení nemá povolený průchod brány Windows firewall.

1. Chcete-li povolit *myVm2* příkaz ping *myVm1* v pozdějším kroku, zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Že příchozí příkaz umožňuje ICMP přes bránu Windows firewall.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Opakujte kroky v [připojení k virtuálnímu počítači z Internetu](#connect-to-a-vm-from-the-internet). Tentokrát připojení k *myVm2*.

1. Na příkazovém řádku na virtuálním počítači *myVm2* zadejte `ping myvm1`.

    Zobrazí se něco jako toto zpět:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Obdržíte odpovědi z virtuálního počítače *myVm1*, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači *myVm1*.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s virtuální sítí a virtuální počítače, použijte [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. K jednomu virtuálnímu počítači jste se připojili z internetu a navázali jste soukromou komunikaci mezi tímto a druhým virtuálním počítačem. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači. Ve výchozím nastavení Azure umožňuje pouze příchozí připojení ke vzdálené ploše pro virtuální počítače s Windows z Internetu. Další informace o konfiguraci různých typů síťové komunikace mezi virtuálním počítači, přejděte [filtrování síťového provozu](tutorial-filter-network-traffic.md) kurzu.
