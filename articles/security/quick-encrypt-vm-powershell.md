---
title: Rychlý start – Šifrování virtuálního počítače IaaS s Windows pomocí Azure PowerShellu | Microsoft Docs
description: V tomto rychlém startu zjistěte, jak šifrování virtuálního počítače s Windows IaaS v Azure pomocí Azure Powershellu.
services: security
documentationcenter: na
author: msmbaldwin
manager: MBaldwin
ms.assetid: c8abd340-5ed4-42ec-b83f-4d679b61494d
ms.service: security
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: mbaldwin
ms.custom: seodec18
ms.openlocfilehash: c36f9506665f573a2e7990102af81a9de088e95b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501637"
---
# <a name="quickstart-encrypt-a-windows-iaas-vm-with-azure-powershell"></a>Rychlý start: Šifrování virtuálního počítače s Windows IaaS s využitím Azure Powershellu

Azure Disk Encryption pomáhá šifrovat disky virtuálních počítačů IaaS s Windows a Linuxem. Toto řešení je integrované do služby Azure Key Vault a pomáhá řídit a spravovat klíče a tajné klíče pro šifrování disků. Pomocí služby Azure Disk Encryption můžete zajistit zabezpečení neaktivních virtuálních počítačů s využitím standardní šifrovací technologie. V tomto rychlém startu vytvoříte virtuální počítač s Windows Serverem 2016 a zašifrujete disk s operačním systémem.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell
- Instalaci nebo aktualizaci [nejnovější verzi modulu Azure PowerShell](/powershell/azure/install-az-ps)
    - Verze modulu Az musí být 1.0.0 nebo vyšší. Použití `Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path` chcete zkontrolovat verzi.
- Kopie [skriptu pro požadované součásti služby Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
    - Pokud tento skript již máte, stáhněte si novou kopii, protože nedávno došlo k jeho změně. 
    - Pomocí **CTRL-A** vyberte všechen text a pak ho pomocí **CTRL-C** zkopírujte do Poznámkového bloku.
    - Uložte soubor jako **ADEPrereqScript.ps1**.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Klikněte pravým tlačítkem na **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** a klikněte na **Spustit jako správce**.
1. V **správce: Windows PowerShell ISE** okna, klikněte na tlačítko **zobrazení** a potom klikněte na tlačítko **zobrazit podokno skriptu**.
1. V podokně skriptu zadejte následující rutinu: 

     ```azurepowershell
      Connect-AzAccount
     ```

1. Klikněte na zelenou šipku u **Spustit skript** nebo použijte F5. 
2. Pomocí interaktivního přihlášení dokončete připojení ke svému účtu Azure.
3. Zkopírujte své **ID předplatného**, které se vrátí a které použijete při spuštění dalšího skriptu PowerShellu. 

## <a name="bkmk_PrereqScript"></a>Spuštění skriptu pro požadované součásti služby Azure Disk Encryption
 Skript **ADEPrereqScript.ps1** vytvoří skupinu prostředků a trezor klíčů a nastaví zásady přístupu k trezoru klíčů. Tento skript také pro trezor klíčů vytvoří zámek prostředku, který ho pomůže chránit před nechtěným odstraněním.  

1. V **správce: Windows PowerShell ISE** okna, klikněte na tlačítko **souboru** a potom klikněte na tlačítko **otevřít**. Přejděte k souboru **ADEPrereqScript.ps1** a dvakrát na něj klikněte. Skript se otevře v podokně skriptu.
2. Kliknutím na zelenou šipku u **Spustit skript** nebo pomocí F5 spusťte skript. 
3. Zadejte názvy nové **skupiny prostředků** a nového **trezoru klíčů**. Pro účely tohoto rychlého startu nepoužívejte existující skupinu prostředků ani trezor klíčů, protože později skupinu prostředků odstraníme. 
4. Zadejte umístění, ve kterém chcete prostředky vytvořit, například **EastUS**. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.
5. Vložte své zkopírované **ID předplatného**. Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.  
6. Klikněte na zelenou šipku u **Spustit skript**. 
7. Zkopírujte si vrácené hodnoty **DiskEncryptionKeyVaultUrl** a **DiskEncryptionKeyVaultId**, které použijete později.

![Skript pro požadované součásti služby Azure Disk Encryption spuštěný v Integrovaném skriptovacím prostředí (ISE) v prostředí PowerShell](media/azure-security-disk-encryption/ade-prereq-script.PNG)


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače 
Teď je potřeba vytvořit virtuální počítač, abyste mohli zašifrovat jeho disk. Skript, který použijete, vytvoří virtuální počítač s Windows Serverem 2016 s 8 GB paměti RAM a 30GB diskem s operačním systémem. 

1. Zkopírujte skript do **správce: Windows PowerShell ISE** podokno skriptu a měnit proměnné první tři. Skupina prostředků a umístění se musí shodovat s těmi, které jste použili pro [skript pro požadované součásti](#bkmk_PrereqScript).  

   ```azurepowershell
    # Variables for common values
    $resourceGroup = "MySecureRG"
    $location = "EastUS"
    $vmName = "MySecureVM"
    
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
    
    # Create a resource group
    New-AzResourceGroup -Name $resourceGroup -Location $location
    
    # Create a subnet configuration
    $subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    
    # Create a virtual network
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    
    # Create a public IP address and specify a DNS name
    $pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    
    # Create an inbound network security group rule for port 3389
    $nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    
    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    
    # Create a virtual machine configuration
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D2_v3 | `
    Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-smalldisk -Version latest | `
    Add-AzVMNetworkInterface -Id $nic.Id
    
    # Create a virtual machine
    New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
   ```

2. Kliknutím na zelenou šipku u **Spustit skript** sestavte virtuální počítač.  


## <a name="encrypt-the-disk-of-the-vm"></a>Šifrování disku virtuálního počítače
Teď, když máte vytvořený a nakonfigurovaný trezor klíčů a virtuálním Počítačem, může zašifrování disku pomocí **Set-AzVmDiskEncryptionExtension** rutiny. 
 
1. Spuštěním následující rutiny zašifrujte disk virtuálního počítače:

    ```azurepowershell
     Set-AzVmDiskEncryptionExtension -ResourceGroupName "MySecureRG" -VMName "MySecureVM" `
     -DiskEncryptionKeyVaultId "<Returned by the prerequisites script>" -DiskEncryptionKeyVaultUrl "<Returned by the prerequisites script>"
     ```


1. Po dokončení šifrování můžete zašifrování disku ověřit pomocí následující rutiny: 

     ```azurepowershell
     Get-AzVmDiskEncryptionStatus -ResourceGroupName "MySecureRG" -VMName "MySecureVM"
     ```
    ![Get-AzVmDiskEncryptionStatus output](media/azure-security-disk-encryption/ade-get-encryption-status.PNG)
    
## <a name="clean-up-resources"></a>Vyčištění prostředků
 Skript **ADEPrereqScript.ps1** vytvoří pro trezor klíčů zámek prostředku. Pokud chcete vyčistit prostředky z tohoto rychlého startu, musíte nejprve odebrat zámek prostředku a pak odstranit skupinu prostředků. 

1. Odeberte zámek prostředku z trezoru klíčů.

     ```azurepowershell
     $LockId =(Get-AzResourceLock -ResourceGroupName "MySecureRG" -ResourceName "MySecureVault" -ResourceType "Microsoft.KeyVault/vaults").LockID 
     Remove-AzResourceLock -LockID $LockId
      ```
    
2. Odeberte skupinu prostředků. Tím se odstraní také všechny prostředky v této skupině. 
     ```azurepowershell
      Remove-AzResourceGroup -Name "MySecureRG"
      ```

## <a name="next-steps"></a>Další postup
V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Požadavky na službu Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
