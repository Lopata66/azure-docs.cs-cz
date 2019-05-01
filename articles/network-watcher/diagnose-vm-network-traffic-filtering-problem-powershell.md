---
title: Diagnostika problému s filtrováním síťového provozu virtuálního počítače – rychlý start – Azure PowerShell | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak diagnostikovat problém s filtrováním síťového provozu virtuálního počítače pomocí funkce ověření toku protokolů IP služby Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7b8916310d7971779934ec0bbf2a4805492590a5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711304"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Rychlý start: Diagnostika virtuální počítač sítě filtrováním provozu – Azure PowerShell

V tomto rychlém startu nasadíte virtuální počítač a potom zkontrolujete obousměrnou komunikaci mezi IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, v tomto rychlém startu vyžaduje prostředí Azure PowerShell `Az` modulu. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.



## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvoření virtuálního počítače s [nové AzVM](/powershell/module/az.compute/new-azvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a PowerShell nevrátí výstup, nepokračujte ve zbývajících krocích.

## <a name="test-network-communication"></a>Test síťové komunikace

Pokud chcete otestovat síťovou komunikaci pomocí služby Network Watcher, musíte nejprve sledovací proces sítě povolit v oblasti, ve které se nachází daný virtuální počítač, a potom teprve můžete k otestování komunikace použít funkci ověření toků protokolu IP služby Network Watcher.

### <a name="enable-network-watcher"></a>Povolení sledovacího procesu sítě

Pokud už máte k dispozici sledovací proces sítě povolené v oblasti USA – východ, použijte [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) načíst sledovací proces sítě. Následující příklad načte existující sledovací proces sítě s názvem *NetworkWatcher_eastus*, který se nachází ve skupině prostředků *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Pokud ještě nemáte sledovací proces sítě povolené v oblasti USA – východ, použijte [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) vytvořit network watcher v oblasti USA – východ:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>Použití ověření toku protokolu IP

Když vytvoříte virtuální počítač, Azure u něj ve výchozím nastavení blokuje příchozí i odchozí síťový provoz. Později můžete výchozí nastavení Azure přepsat a povolit nebo odepřít další typy provozu. K otestování, jestli provoz je povolený nebo zakázaný do různých cílů a z zdrojové IP adresy, použijte [testovací AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) příkazu.

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Po několika sekundách vás vrácený výsledek informuje, že přístup je povolený pravidlem zabezpečení s názvem **AllowInternetOutbound**.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Vrácený výsledek vás informuje o tom, že přístup byl odepřen pravidlem zabezpečení s názvem **DefaultOutboundDenyAll**.

Otestujte příchozí komunikaci do virtuálního počítače z IP adresy 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Vrácený výsledek vás informuje o tom, že přístup byl odepřen kvůli pravidlu zabezpečení s názvem **DefaultInboundDenyAll**. Nyní, když už víte, která pravidla zabezpečení umožňují nebo odepírají příchozí a odchozí provoz virtuálního počítače, můžete určit, jak tyto problémy vyřešit.

## <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

Chcete-li zjistit, proč pravidla v [testování síťové komunikace](#test-network-communication) povolují nebo znemožňuje komunikaci, zkontrolujte platná pravidla zabezpečení pro síťové rozhraní s [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

Vrácený výstup obsahuje následující text pravidla **AllowInternetOutbound**, které v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) umožnilo odchozí přístup k adrese www.bing.com:

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Ve výstupu můžete vidět, že **předponou zdrojové adresy** (DestinationAddressPrefix) je **Internet**. Není však jasné, jak adresa 13.107.21.200, kterou jste testovali v části [Použití ověření toku protokolu IP](#use-ip-flow-verify), souvisí s **Internetem**. V části **ExpandedDestinationAddressPrefix** můžete vidět několik předpon adres a jednou z nich je **12.0.0.0/6**, která zahrnuje rozsah IP adres od 12.0.0.1 do 15.255.255.254. Vzhledem k tomu, že adresa 13.107.21.200 do tohoto rozsahu adres patří, pravidlo **AllowInternetOutBound** jí odchozí provoz umožní. Kromě toho na výstupu vráceném příkazem `Get-AzEffectiveNetworkSecurityGroup` nejsou uvedená žádná jiná pravidla s vyšší **prioritou** (nižším číslem), která by toto pravidlo přepisovala. Pokud byste chtěli odepřít odchozí komunikaci na IP adresu 13.107.21.200, mohli byste přidat pravidlo zabezpečení s vyšší prioritou, které portu 80 odchozí komunikaci na tuto IP adresu zakáže.

Když jste spustili příkaz `Test-AzNetworkWatcherIPFlow`, abyste v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) otestovali odchozí komunikaci na IP adresu 172.131.0.100, výstup vás informoval, že pravidlo **DefaultOutboundDenyAll** komunikaci odepřelo. Pravidlo **DefaultOutboundDenyAll** plní stejnou funkci jako pravidlo **DenyAllOutBound** uvedené v následujícím výstupu z příkazu `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

Toto pravidlo uvádí jako **předponu zdrojové adresy** (DestinationAddressPrefix) adresu **0.0.0.0/0**. Toto pravidlo zakazuje odchozí komunikaci na IP adresu 172.131.0.100, protože tato adresa se nenachází v **předponě zdrojové adresy** (DestinationAddressPrefix) žádného jiného odchozího pravidla ve výstupu z příkazu `Get-AzEffectiveNetworkSecurityGroup`. Pokud chcete odchozí komunikaci povolit, můžete přidat pravidlo zabezpečení s vyšší prioritou, které IP adrese 172.131.0.100 na portu 80 umožní odchozí komunikaci.

Když jste spustili příkaz `Test-AzNetworkWatcherIPFlow`, abyste v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) otestovali příchozí komunikaci z IP adresy 172.131.0.100, výstup vás informoval, že pravidlo **DefaultInboundDenyAll** komunikaci odepřelo. Pravidlo **DefaultInboundDenyAll** plní stejnou funkci jako pravidlo **DenyAllInBound** uvedené v následujícím výstupu z příkazu `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

Pravidlo **DenyAllInBound** se použilo, protože jak je vidět ve výstupu, žádné jiné pravidlo s vyšší prioritou, které by umožnovalo portu 80 příchozí komunikaci na virtuální počítač z IP adresy 172.131.0.100, ve výstupu z příkazu `Get-AzEffectiveNetworkSecurityGroup` neexistuje. Pokud chcete příchozí komunikaci povolit, mohli byste přidat pravidlo zabezpečení s vyšší prioritou, které by portu 80 příchozí komunikaci z IP adresy 172.131.0.100 umožnilo.

Kontroly v tomto rychlém startu testovaly konfiguraci Azure. Pokud kontroly vrátily očekávané výsledky a problémy se sítí přetrvávají, přesvědčte se, že mezi virtuálním počítačem a koncovým bodem, se kterým komunikujete, se nenachází brána firewall a že operační systém ve virtuálním počítači nemá bránu firewall, která by komunikaci povolovala nebo odepírala.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všech prostředků, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili virtuální počítač a diagnostikovali jste příchozí a odchozí filtry síťového provozu. Zjistili jste, že pravidla skupiny zabezpečení sítě umožňují nebo odepírají příchozí i odchozí provoz virtuálního počítače. Zjistěte více o [pravidlech zabezpečení](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a způsobu [jejich vytvoření](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Přestože budete používat správné filtry síťového provozu, komunikace s virtuálním počítačem může kvůli konfigurací směrování stejně selhat. Získejte více informací o [diagnostice potíží se směrováním sítě virtuálních počítačů](diagnose-vm-network-routing-problem-powershell.md) nebo si přečtěte článek o [řešení potíží s připojením](network-watcher-connectivity-powershell.md), abyste mohli diagnostikovat potíže s odchozím směrováním, latencí a filtrováním provozu.
