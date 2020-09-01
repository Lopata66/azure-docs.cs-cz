---
title: Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge přes Azure PowerShell
description: Popisuje, jak vytvořit a spravovat virtuální počítače na Azure Stack zařízení GPU na hraničních zařízeních pomocí Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d5210a3788f7bb054492c2d83c595c26fa3c4f42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265707"
---
# <a name="deploy-vms-on-your-azure-stack-edge-gpu-device-via-azure-powershell"></a>Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge přes Azure PowerShell

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

V tomto kurzu se dozvíte, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pomocí Azure PowerShell.

## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

Pracovní postup nasazení je znázorněný v následujícím diagramu.

![Pracovní postup nasazení virtuálních počítačů](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>Dotaz na integrované předplatné na zařízení

Pro Azure Resource Manager se podporuje jenom jedno uživatelem viditelné pevné předplatné. Toto předplatné je jedinečné pro každé zařízení a tento název předplatného nebo ID předplatného se nedá změnit.

Toto předplatné obsahuje všechny prostředky, které jsou vytvořené pro vytváření virtuálních počítačů. 

> [!IMPORTANT]
> Toto předplatné není připojené k vašemu předplatnému Azure a v místním prostředí je na vašem zařízení.

Toto předplatné se použije k nasazení virtuálních počítačů.

1.  Pokud chcete zobrazit seznam tohoto předplatného, zadejte:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Ukázkový výstup najdete níž.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Získá seznam registrovaných poskytovatelů prostředků spuštěných v zařízení. Tento seznam obvykle zahrnuje výpočetní výkon, síť a úložiště.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Poskytovatelé prostředků jsou předem registrováni a nelze je změnit ani změnit.
    
    Ukázkový výstup je uveden níže:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako je například účet úložiště, disk a spravovaný disk.

> [!IMPORTANT]
> Všechny prostředky se vytvoří ve stejném umístění jako zařízení a umístění je nastavené na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Ukázkový výstup najdete níž.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte nový účet úložiště pomocí skupiny prostředků vytvořené v předchozím kroku. Jedná se o **místní účet úložiště** , který se použije k nahrání image virtuálního disku pro virtuální počítač.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Pomocí Azure Resource Manager lze vytvořit pouze účty místního úložiště, jako je například místně redundantní úložiště (Standard_LRS nebo Premium_LRS). Pokud chcete vytvořit vrstvené účty úložiště, přečtěte si postup v tématu [Přidání, připojení k účtům úložiště na Azure Stack hraničních](azure-stack-edge-j-series-deploy-add-storage-accounts.md)zařízeních.

Ukázkový výstup najdete níž.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Pokud chcete získat klíč účtu úložiště, spusťte `Get-AzureRmStorageAccountKey` příkaz. Zde je zobrazen ukázkový výstup tohoto příkazu.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>Přidat do souboru hostitelů identifikátor URI objektu BLOB

Identifikátor URI objektu BLOB jste už přidali v souboru Hosts pro klienta, který používáte pro připojení k úložišti objektů BLOB v oddílu [Úprava souboru hostitele pro překlad názvů koncových bodů](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Toto byla položka pro identifikátor URI objektu BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> ..\<dnsdomain\>


## <a name="install-certificates"></a>Instalace certifikátů

Pokud používáte *protokol HTTPS*, musíte na zařízení nainstalovat vhodné certifikáty. V takovém případě nainstalujte certifikát koncového bodu objektu BLOB. Další informace najdete v tématu Vytvoření a nahrání certifikátů v tématu [Správa certifikátů](azure-stack-edge-j-series-manage-certificates.md).

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Zkopírujte všechny bitové kopie disků, které se mají použít, do objektů blob stránky v místním účtu úložiště, který jste vytvořili v předchozích krocích. Pomocí nástroje, jako je [AzCopy](../storage/common/storage-use-azcopy-v10.md) , můžete nahrát virtuální pevný disk do účtu úložiště, který jste vytvořili v předchozích krocích. 

Než začnete používat AzCopy, ujistěte se, že [je AzCopy správně nakonfigurovaný](#configure-azcopy) pro použití se službou blob Storage REST API verze, kterou používáte se zařízením Azure Stack Edge.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Nastavte `BlobType` na stránku pro vytvoření spravovaného disku z disku VHD. Nastavte `BlobType` blokování při zápisu do vrstvených účtů úložiště pomocí AzCopy.

Image disků si můžete stáhnout z webu Marketplace. Podrobný postup najdete v [části získání image virtuálního disku z webu Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Vzorový výstup používající AzCopy 7,3 je uveden níže. Další informace o tomto příkazu najdete v pro [nahrání souboru VHD do účtu úložiště pomocí AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>Vytvoření spravovaných disků z VHD

Z nahraného virtuálního pevného disku vytvořte spravovaný disk.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Ukázkový výstup je uveden níže: 

$DiskConfig = New-AzureRmDiskConfig-Location DBELocal-CreateOption import – SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Ukázkový výstup najdete níž. Další informace o této rutině najdete na webu [New-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Vytvoření image virtuálního počítače z disku spravovaného imagí

Pomocí následujícího příkazu vytvořte image virtuálního počítače ze spravovaného disku. Nahraďte hodnoty v rámci \< \> zvolených názvů.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Ukázkový výstup najdete níž. Další informace o této rutině najdete na webu [New-AzureRmImage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Vytvoření virtuálního počítače s dříve vytvořenými prostředky

Před vytvořením a nasazením virtuálního počítače musíte vytvořit jednu virtuální síť a přidružit ji k virtuálnímu síťovému rozhraní.

> [!IMPORTANT]
> Při vytváření virtuální sítě a rozhraní virtuální sítě platí následující pravidla:
> - Dá se vytvořit jenom jedna virtuální síť (dokonce i přes skupiny prostředků) a ta se musí přesně shodovat s logickou sítí z hlediska adresního prostoru.
> -   Ve virtuální síti se povolí jenom jedna podsíť. Podsíť musí být přesně stejný adresní prostor jako virtuální síť.
> -   Během vytváření vNIC se povolí jenom statická metoda alokace a uživatel musí zadat privátní IP adresu.

 
**Vytvoření virtuální sítě**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Vytvoření vNIC pomocí ID podsítě virtuální sítě**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Ukázkový výstup těchto příkazů je uveden níže:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Volitelně při vytváření vNIC pro virtuální počítač můžete předat veřejnou IP adresu. V takovém případě bude veřejná IP adresa vracet privátní IP adresu. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Vytvoření virtuálního počítače**

Image virtuálního počítače teď můžete použít k vytvoření virtuálního počítače a připojit ho k virtuální síti, kterou jste vytvořili dříve.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Připojení k virtuálnímu počítači

Připojte se k virtuálnímu počítači pomocí privátní IP adresy, kterou jste předali během vytváření virtuálního počítače.

Otevřete relaci SSH pro připojení k IP adrese.

`ssh -l <username> <ip address>`

Po zobrazení výzvy zadejte heslo, které jste použili při vytváření virtuálního počítače.

Pokud potřebujete zadat klíč SSH, použijte tento příkaz.

SSH-i c:/uživatelé/správce/. ssh/id_rsa Administrator@5.5.41.236

Pokud jste při vytváření virtuálního počítače použili veřejnou IP adresu, můžete k ní připojit virtuální počítač pomocí této IP adresy. Získání veřejné IP adresy: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
Veřejná IP adresa v tomto případě bude stejná jako soukromá IP adresa, kterou jste předali během vytváření virtuálního síťového rozhraní.


## <a name="manage-vm"></a>Správa virtuálního počítače

Následující část popisuje některé běžné operace kolem virtuálního počítače, který vytvoříte na zařízení Azure Stack Edge.

### <a name="list-vms-running-on-the-device"></a>Výpis virtuálních počítačů spuštěných v zařízení

Pokud chcete vrátit seznam všech virtuálních počítačů, které běží na vašem zařízení Azure Stack Edge, spusťte následující příkaz.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Zapnout virtuální počítač

Spuštěním následující rutiny zapněte virtuální počítač běžící na vašem zařízení:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


Další informace o této rutině získáte v nabídce [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0).

### <a name="suspend-or-shut-down-the-vm"></a>Pozastavit nebo vypnout virtuální počítač

Spuštěním následující rutiny zastavte nebo vypněte virtuální počítač běžící na vašem zařízení:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


Další informace o této rutině najdete na webu [rutiny Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0).

### <a name="add-a-data-disk"></a>Přidat datový disk

Pokud se požadavky na váš virtuální počítač zvyšují, možná budete muset přidat datový disk.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Spuštěním následující rutiny odeberte virtuální počítač ze zařízení:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Další informace o této rutině najdete v [rutině Remove-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0).


## <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Velikost virtuálního počítače určuje množství výpočetních prostředků, jako jsou CPU, GPU a paměť, které jsou k dispozici pro virtuální počítač. Virtuální počítače by měly být vytvořené pomocí velikosti virtuálního počítače vhodné pro zatížení. I když všechny počítače budou spuštěné na stejném hardwaru, velikosti počítačů mají různá omezení pro přístup k disku, což vám umožní spravovat celkový přístup k disku napříč virtuálními počítači. Pokud se pracovní zátěž zvýší, je také možné velikost existujícího virtuálního počítače změnit.

Pro vytváření Azure Stackch hraničních zařízení se podporují následující virtuální počítače řady Standard Dv2 Series.

### <a name="dv2-series"></a>Dv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4×500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8×500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16×500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32×500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>DSv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Dv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8×500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16×500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32×500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>DSv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Další informace najdete na [Dv2 Series v pro obecné účely velikosti virtuálních počítačů](../virtual-machines/dv2-dsv2-series.md#dv2-series).

## <a name="unsupported-vm-operations-and-cmdlets"></a>Nepodporované operace a rutiny virtuálního počítače

Rozšíření, sady škálování, sady dostupnosti, snímky se nepodporují.

## <a name="configure-azcopy"></a>Konfigurace AzCopy

Když nainstalujete nejnovější verzi AzCopy, budete muset AzCopy nakonfigurovat tak, aby se zajistilo, že bude odpovídat verzi REST API služby Blob Storage Azure Stack hraničního zařízení.

Na klientovi, který se používá pro přístup k vašemu zařízení Azure Stack Edge, nastavte globální proměnnou tak, aby odpovídala REST APImu úložišti objektů BLOB.

### <a name="on-windows-client"></a>V klientovi Windows 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>V klientovi se systémem Linux

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

Chcete-li ověřit, zda byla proměnná prostředí pro AzCopy nastavena správně, proveďte následující kroky:

1. Spustit AzCopy ENV
2. Najít `AZCOPY_DEFAULT_SERVICE_API_VERSION` parametr To by mělo mít hodnotu, kterou jste nastavili v předchozích krocích.


## <a name="next-steps"></a>Další kroky

[Rutiny Azure Resource Manager](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
