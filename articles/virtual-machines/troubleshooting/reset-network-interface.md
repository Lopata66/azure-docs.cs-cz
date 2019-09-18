---
title: Postup resetování síťového rozhraní pro virtuální počítač Azure s Windows | Microsoft Docs
description: Ukazuje, jak resetovat síťové rozhraní pro virtuální počítače Azure s Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: afb8335d3206a76b8f9bc47733e9816126e80af0
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058468"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Postup resetování síťového rozhraní pro virtuální počítač Azure s Windows 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Tento článek popisuje, jak resetovat síťové rozhraní pro virtuální počítače Azure s Windows za účelem řešení problémů, když se nemůžete připojit k Microsoft Azure virtuálnímu počítači s Windows (VM) po:

* Zakážete výchozí síťové rozhraní (NIC). 
* Ručně jste pro síťovou kartu nastavili statickou IP adresu. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Resetování síťové rozhraní

### <a name="for-vms-deployed-in-resource-group-model"></a>Pro virtuální počítače nasazené v modelu skupiny prostředků

1.  Přejděte na [Azure Portal](https://ms.portal.azure.com).
2.  Vyberte ovlivněný virtuální počítač.
3.  Vyberte **sítě** a pak vyberte síťové rozhraní virtuálního počítače.

    ![Umístění síťového rozhraní](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Vyberte **Konfigurace protokolu IP**.
5.  Vyberte IP adresu. 
6.  Pokud **přiřazení privátních IP adres** není **statické**, změňte ho na **static**.
7.  Změňte **IP adresu** na jinou IP adresu, která je k dispozici v podsíti.
8. Virtuální počítač se restartuje a Inicializuje novou síťovou kartu pro systém.
9.  Zkuste k počítači protokol RDP. Pokud chcete, můžete privátní IP adresu v případě úspěchu změnit zpátky na původní. V opačném případě ji můžete zachovat. 

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte nainstalovanou [nejnovější Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
2. Otevřete relaci Azure PowerShell se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Zkuste k počítači protokol RDP.  Pokud chcete, můžete privátní IP adresu v případě úspěchu změnit zpátky na původní. V opačném případě ji můžete zachovat.

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

K resetování síťového rozhraní použijte tento postup:

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

1.  Přejděte na [Azure Portal]( https://ms.portal.azure.com).
2.  Vyberte **Virtual Machines (Classic)** .
3.  Vyberte ovlivněný virtuální počítač.
4.  Vyberte **IP adresy**.
5.  Pokud **přiřazení privátních IP adres** není **statické**, změňte ho na **static**.
6.  Změňte **IP adresu** na jinou IP adresu, která je k dispozici v podsíti.
7.  Vyberte **Uložit**.
8.  Virtuální počítač se restartuje a Inicializuje novou síťovou kartu pro systém.
9.  Zkuste k počítači protokol RDP. V případě úspěchu můžete privátní IP adresu obnovit zpátky na původní.  

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte nainstalovanou [nejnovější Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .
2. Otevřete relaci Azure PowerShell se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Zkuste k počítači protokol RDP. Pokud chcete, můžete privátní IP adresu v případě úspěchu změnit zpátky na původní. V opačném případě ji můžete zachovat. 

## <a name="delete-the-unavailable-nics"></a>Odstranění nedostupných síťových karet
Po provedení vzdálené plochy k počítači je nutné odstranit staré síťové karty, aby nedocházelo k potenciálním potížím:

1.  Otevřete Device Manager.
2.  Vyberte **Zobrazit** > **Zobrazit skrytá zařízení**.
3.  Vyberte **síťové adaptéry**. 
4.  Vyhledejte adaptéry s názvem Microsoft Hyper-V síťový adaptér.
5.  Možná se šedě zobrazí nedostupný adaptér. Klikněte pravým tlačítkem na adaptér a pak vyberte odinstalovat.

    ![bitová kopie síťového rozhraní](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Odinstalujte jenom nedostupné adaptéry, které mají název Microsoft Hyper-V síťový adaptér. Pokud odinstalujete některé z ostatních skrytých adaptérů, může to způsobit další problémy.
    >
    >

6.  Nyní by měly být všechny nedostupné adaptéry smazány v systému.
