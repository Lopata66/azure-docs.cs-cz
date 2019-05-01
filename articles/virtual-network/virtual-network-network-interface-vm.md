---
title: Síťová rozhraní pro přidání nebo odebrání z virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat síťová rozhraní k nebo odebrání síťových rozhraní virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: 23e46290af6bdb4c217d8fa0cd836673652fc81d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701373"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Síťová rozhraní pro přidání nebo odebrání síťových rozhraní z virtuálních počítačů

Zjistěte, jak přidat existující síťové rozhraní, když vytváříte virtuální počítač Azure (VM), nebo k přidání nebo odebrání síťových rozhraní z existujícího virtuálního počítače ve stavu Zastaveno (přidělení zrušeno). Síťové rozhraní umožňuje virtuálnímu počítači Azure ke komunikaci s Internetem, Azure a místním prostředkům. Virtuální počítač může mít jednu nebo víc síťových rozhraní. 

Pokud potřebujete přidat, změnit, nebo odebrání IP adres pro síťové rozhraní, naleznete v tématu [správě IP adres rozhraní sítě](virtual-network-network-interface-addresses.md). Pokud potřebujete vytvořit, změnit, nebo odstranění síťových rozhraní, naleznete v tématu [Správa síťových rozhraní](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Přidat existující síťová rozhraní k novému virtuálnímu počítači

Při vytváření virtuálního počítače prostřednictvím portálu pro portál vytvoří síťové rozhraní s výchozím nastavením a připojí ho k virtuálnímu počítači. Nelze přidat existující síťová rozhraní k novému virtuálnímu počítači, ani vytvoření virtuálního počítače s několika síťovými rozhraními s využitím webu Azure portal. Jak provést pomocí Powershellu nebo rozhraní příkazového řádku, ale nezapomeňte seznámit s [omezení](#constraints). Pokud vytvoříte virtuální počítač s několika síťovými rozhraními, musíte také nakonfigurovat operační systém je po vytvoření virtuálního počítače správně použít. Další informace o konfiguraci [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro několik síťových rozhraní.

### <a name="commands"></a>Příkazy

Než vytvoříte virtuální počítač, vytvořte síťové rozhraní pomocí kroků v [vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).

|Tool|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Přidat síťové rozhraní existujícímu virtuálnímu počítači

1. Přihlaste se k portálu Azure.
2. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, do které chcete přidat síťové rozhraní, nebo vyhledejte virtuální počítač tak, že vyberete **všechny služby**a potom **virtuálních počítačů**. Když najdete virtuálního počítače, vyberte ho. Virtuální počítač musí podporovat počet síťových rozhraní, které chcete přidat. Chcete-li zjistit, kolik síťových rozhraní jednotlivé velikosti virtuálních počítačů podporuje, najdete v článku [velikostí pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro Windows virtual machines v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Vyberte **přehled**v části **nastavení**. Vyberte **Zastavit**a potom počkejte **stav** virtuálního počítače se změní na **zastaveno (přidělení zrušeno)**.
4. Vyberte **sítě**v části **nastavení**.
5. Vyberte **připojit síťové rozhraní**. V seznamu síťových rozhraní, které aktuálně nejste připojení k jinému virtuálnímu počítači vyberte ten, který chcete připojit.

   >[!NOTE]
   >Síťové rozhraní, které jste vybrali nemůže mít akcelerované síťové služby povolené, nemůže mít přiřazena adresa protokolu IPv6 a musí existovat ve stejné virtuální síti jako ten, který obsahuje síťové rozhraní v současné době připojené k virtuálnímu počítači.

   Pokud nemáte existující síťové rozhraní, musíte nejprve vytvoříte jeden. Chcete-li to provést, vyberte **vytvořit síťové rozhraní**. Další informace o tom, jak vytvořit síťové rozhraní, naleznete v tématu [vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface). Další informace o dalších omezení při přidávání síťových rozhraní virtuálních počítačů najdete v tématu [omezení](#constraints).

6. Vyberte **OK**.
7. Vyberte **přehled**v části **nastavení**a potom **Start** ke spuštění virtuálního počítače.
8. Nakonfigurujte operační systém virtuálního počítače správně použít několik síťových rozhraní. Další informace o konfiguraci [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro několik síťových rozhraní.

### <a name="commands"></a>Příkazy
|Tool|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[Přidání az vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobný postup](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Přidat AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobný postup](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Zobrazení síťových rozhraní virtuálního počítače

Můžete zobrazit síťová rozhraní v současné době připojené k virtuálnímu počítači další informace o konfiguraci každé síťové rozhraní a IP adresy přiřazené každému síťovému rozhraní. 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu, který má přiřazenou roli vlastník, Přispěvatel nebo Přispěvatel sítě pro vaše předplatné. Další informace o tom, jak přiřadit role účty, najdete v článku [předdefinované role pro řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Do pole, které obsahuje text **vyhledat prostředky** v horní části stránky na webu Azure portal, zadejte **virtuálních počítačů**. Když **virtuálních počítačů** se zobrazí ve výsledcích hledání vyberte ji.
3. Vyberte název virtuálního počítače, pro kterou chcete zobrazit síťová rozhraní.
4. V **nastavení** oddílu pro virtuální počítač jste vybrali, vyberte **sítě**. Další informace o nastavení síťového rozhraní a jejich změny najdete v tématu [Správa síťových rozhraní](virtual-network-network-interface.md). Další informace o tom, jak přidat, změna nebo odebrání IP adresy přiřazené k síťovému rozhraní najdete v tématu [správě IP adres rozhraní sítě](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Příkazy

|Tool|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Odebrání síťového rozhraní virtuálního počítače

1. Přihlaste se k portálu Azure.
2. Do pole Hledat v horní části portálu vyhledejte název virtuálního počítače, které chcete odebrat (odpojit) síťových rozhraní nebo procházení pro virtuální počítač tak, že vyberete **všechny služby**a potom **virtuálních počítačů**. Když najdete virtuálního počítače, vyberte ho.
3. Vyberte **přehled**v části **nastavení**a potom **Zastavit**. Počkejte, dokud **stav** virtuálního počítače se změní na **zastaveno (přidělení zrušeno)**.
4. Vyberte **sítě**v části **nastavení**.
5. Vyberte **Odpojit síťové rozhraní**. V seznamu síťových rozhraní, které jsou aktuálně připojené k virtuálnímu počítači vyberte síťové rozhraní, které chcete odpojit.

   >[!NOTE]
   >Pokud pouze jedno síťové rozhraní je uveden, nemůže ho, odpojit, protože virtuální počítač musí mít vždy alespoň jedno síťové rozhraní připojené k němu.
6. Vyberte **OK**.

### <a name="commands"></a>Příkazy

|Tool|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ vm nic odebrat](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobný postup](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Odebrat AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobný postup](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Omezení

- Virtuální počítač musí mít aspoň jedno síťové rozhraní připojené k němu.
- Virtuální počítač může mít pouze jako mnoho síťová rozhraní připojená k němu jako podporuje velikost virtuálního počítače. Další informace o tom, kolik síťových rozhraních podporuje všechny velikosti virtuálních počítačů najdete v tématu [velikostí pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro Windows virtual machines v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Všechny velikosti podporují alespoň dvě síťová rozhraní.
- Síťová rozhraní, které přidáte do virtuálního počítače nemůže aktuálně připojen k jinému virtuálnímu počítači. Další informace o tom, jak vytvořit síťová rozhraní, naleznete v tématu [vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).
- V minulosti může být síťová rozhraní pouze přidán do virtuálních počítačů, které nepodporuje více síťových rozhraní a byly vytvořeny s alespoň dvěma síťovými rozhraními. Nelze přidat síťové rozhraní k virtuálnímu počítači, který byl vytvořen s jedním síťovým rozhraním, i v případě, že velikost virtuálního počítače nepodporuje více síťových rozhraní. Naopak můžete pouze odebrat síťová rozhraní z virtuálního počítače s alespoň tři síťová rozhraní, protože virtuální počítače vytvořené pomocí aspoň dva síťové rozhraní vždycky musí mít aspoň dvě síťová rozhraní. Už ani jeden z těchto omezení platí. Nyní můžete vytvořit virtuální počítač s libovolným počtem síťová rozhraní (až počet podporovaný velikostí virtuálních počítačů).
- Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu počítači definován jako *primární* síťové rozhraní. Všechna další síťová rozhraní ve virtuálním počítači jsou *sekundární* síťová rozhraní.
- I když můžete určit, jaké síťové rozhraní ve výchozím nastavení odesílají odchozí provoz, odešle se veškerý odchozí provoz z virtuálního počítače IP adresa přiřazená primární konfigurace IP primárního síťového rozhraní.
- V minulosti byly všechny virtuální počítače ve stejné skupině dostupnosti musí mít jeden nebo více síťových rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní může nyní existovat ve stejné sadě dostupnosti, až na počet podporovaný velikostí virtuálních počítačů. Virtuální počítač můžete přidat pouze do skupiny dostupnosti při jeho vytvoření. Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů v Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Síťová rozhraní v jednom virtuálním počítači můžete připojeny k různým podsítím v rámci virtuální sítě, síťová rozhraní musí všechny připojeny ke stejné virtuální síti.
- Žádné IP adresu pro všechny konfigurace IP z každé primární nebo sekundární síťové rozhraní můžete přidat do fondu back-end pro vyrovnávání zatížení Azure. V minulosti jenom primární IP adresu primárního síťového rozhraní může být přidán do fondu back-end. Další informace o IP adresy a konfiguracích najdete v tématu [přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md).
- Odstranění virtuálního počítače nedojde k odstranění síťových rozhraní, které jsou k němu připojená. Když odstraníte virtuální počítač, síťová rozhraní jsou odpojeny od virtuálního počítače. Můžete přidat síťová rozhraní k různým virtuálním počítačům nebo je odstranit.
- Pokud síťové rozhraní má přiřazenou privátní adresu IPv6, je nutné přidat (připojit) ji do virtuálního počítače při vytváření virtuálního počítače. Síťové rozhraní s přiřazenou adresu IPv6 nelze přidat k virtuálnímu počítači po vytvoření virtuálního počítače. Pokud chcete přidat síťové rozhraní s přiřazenou privátní IPv6 adresou při vytváření virtuálního počítače, můžete přidat jenom toto síťové rozhraní k virtuálnímu počítači, bez ohledu na velikost virtuálního počítače podporuje počet síťových rozhraní. Zobrazit [správě IP adres rozhraní sítě](virtual-network-network-interface-addresses.md) Další informace o tom, jak přiřadit IP adresy pro síťová rozhraní.
- Stejně jako u protokolu IPv6, nejde připojit síťové rozhraní s akcelerovanými síťovými službami, které jsou povolené na virtuálním počítači po jeho vytvoření. Abyste mohli využívat akcelerované síťové služby, musíte dále také dokončit kroky v operačním systému virtuálního počítače. Další informace o akcelerovaných síťových služeb a další omezení při použití, pro [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) virtuálních počítačů.

## <a name="next-steps"></a>Další postup
Vytvoření virtuálního počítače s více síťových rozhraní nebo adresy IP, naleznete v následujících článcích:

|Úkol|Tool|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvořit jeden virtuální počítač síťovou kartu s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvořte jeden virtuální počítač síťovou kartu s privátní IPv6 adresou (za služby Azure Load Balancer)|[Rozhraní příkazového řádku](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
