---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku v Azure | Microsoft Docs
description: Vytvořte nový virtuální počítač s Windows připojením specializovaného spravovaného disku jako disku s operačním systémem pomocí modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 5dde098277b16c7ec5339aa6b963b04dd608c8ac
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079672"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Vytvoření virtuálního počítače s Windows z specializovaného disku pomocí PowerShellu

Vytvořte nový virtuální počítač připojením specializovaného spravovaného disku jako disku s operačním systémem. Specializovaný disk je kopií virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další údaje o stavu z původního virtuálního počítače. 

Při použití specializovaného virtuálního pevného disku k vytvoření nového virtuálního počítače si nový virtuální počítač zachová název počítače původního virtuálního počítače. Další informace specifické pro počítač jsou také uchovávány, a v některých případech mohou tyto duplicitní informace způsobovat problémy. Při kopírování virtuálního počítače si pamatujte na typy informací specifických pro konkrétní počítač, na kterých aplikace závisí.

Máte několik možností:
* [Použijte existující spravovaný disk](#option-1-use-an-existing-disk). Tato možnost je užitečná, pokud máte virtuální počítač, který nefunguje správně. Pokud chcete vytvořit nový virtuální počítač, můžete virtuální počítač odstranit a pak znovu použít spravovaný disk. 
* [Nahrání virtuálního pevného disku](#option-2-upload-a-specialized-vhd) 
* [Zkopírování existujícího virtuálního počítače Azure pomocí snímků](#option-3-copy-an-existing-azure-vm)

Pomocí Azure Portal můžete také [vytvořit nový virtuální počítač ze specializovaného virtuálního pevného disku](create-vm-specialized-portal.md).

V tomto článku se dozvíte, jak používat spravované disky. Pokud máte starší verzi nasazení, která vyžaduje použití účtu úložiště, přečtěte si téma [Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku v účtu úložiště](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="option-1-use-an-existing-disk"></a>Možnost 1: Použít existující disk

Pokud máte virtuální počítač, který jste odstranili a chcete znovu použít disk s operačním systémem k vytvoření nového virtuálního počítače, použijte [příkaz Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Tento disk teď můžete připojit jako disk s operačním systémem k [novému virtuálnímu počítači](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Možnost 2: Nahrání specializovaného virtuálního pevného disku

Virtuální pevný disk můžete nahrát z specializovaného virtuálního počítače vytvořeného pomocí nástroje pro místní virtualizaci, jako je Hyper-V nebo virtuální počítač exportovaný z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Použijte virtuální pevný disk tak, jak je, k vytvoření nového virtuálního počítače. 
  
  * [Připravte si virtuální pevný disk Windows, který se má nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Neprovádějte GENERALIZACI virtuálního počítače pomocí nástroje Sysprep.
  * Odeberte všechny virtualizační nástroje a agenty hosta, které jsou nainstalované na virtuálním počítači (například nástroje VMware).
  * Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby získal IP adresu a nastavení DNS z protokolu DHCP. Tím se zajistí, že při spuštění serveru získá IP adresu v rámci virtuální sítě. 


### <a name="get-the-storage-account"></a>Získat účet úložiště
K uložení nahraného virtuálního pevného disku budete potřebovat účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Zobrazit dostupné účty úložiště.

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, přejděte k části [odeslání virtuálního pevného disku](#upload-the-vhd-to-your-storage-account) .

Vytvoření účtu úložiště.

1. Budete potřebovat název skupiny prostředků, ve které se účet úložiště vytvoří. Použijte příkaz Get-AzResourceGroup, který uvidí všechny skupiny prostředků v rámci vašeho předplatného.
   
    ```powershell
    Get-AzResourceGroup
    ```

    V *západní USA* oblasti vytvořte skupinu prostředků s názvem *myResourceGroup* .

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Vytvořte účet úložiště s názvem *mystorageaccount* v nové skupině prostředků pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) .
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště 
Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte VHD do kontejneru v účtu úložiště. Tento příklad nahraje soubor *myVHD. VHD* z "C:\Users\Public\Documents\Virtual pevných disků\" na účet úložiště s názvem *mystorageaccount* " ve skupině prostředků *myResourceGroup* . Soubor je uložený v kontejneru s názvem *myContainer* a nový název souboru bude *myUploadedVHD. VHD*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Pokud jsou příkazy úspěšné, dostanete odpověď, která vypadá nějak takto:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dokončení příkazu může chvíli trvat v závislosti na připojení k síti a velikosti souboru VHD.

### <a name="create-a-managed-disk-from-the-vhd"></a>Vytvoření spravovaného disku z VHD

Pomocí [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk)vytvořte spravovaný disk ze specializovaného virtuálního pevného disku v účtu úložiště. V tomto příkladu se pro název disku používá *myOSDisk1* , disk se převede do úložiště *Standard_LRS* a použije *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* se jako identifikátor URI pro zdrojový virtuální pevný disk.

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Vytvořte nový disk s operačním systémem z nahraného virtuálního pevného disku. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Možnost 3: Kopírovat existující virtuální počítač Azure

Pomocí snímku virtuálního počítače můžete vytvořit kopii virtuálního počítače, který používá spravované disky, a pak pomocí tohoto snímku vytvořit nový spravovaný disk a nový virtuální počítač.


### <a name="take-a-snapshot-of-the-os-disk"></a>Pořídit snímek disku s operačním systémem

Můžete pořídit snímek celého virtuálního počítače (včetně všech disků) nebo jenom jednoho disku. Následující kroky ukazují, jak pořídit snímek jenom disku s operačním systémem virtuálního počítače pomocí rutiny [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

Nejdřív nastavte některé parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Získejte objekt virtuálního počítače.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Získejte název disku s operačním systémem.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Vytvořte konfiguraci snímku. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Pořídit snímek.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Pokud chcete pomocí tohoto snímku vytvořit virtuální počítač, který musí být vysoký, přidejte do příkazu New- `-AccountType Premium_LRS` AzSnapshotConfig parametr. Tento parametr vytvoří snímek tak, aby byl uložen jako spravovaný disk úrovně Premium. Premium Managed Disks jsou dražší než standard, takže před použitím tohoto parametru se ujistěte, že budete potřebovat prémii.

### <a name="create-a-new-disk-from-the-snapshot"></a>Vytvoření nového disku ze snímku

Pomocí [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk)vytvořte ze snímku spravovaný disk. V tomto příkladu se pro název disku používá *myOSDisk* .

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nastavte název disku operačního systému. 

```powershell
$osDiskName = 'myOsDisk'
```

Vytvořte spravovaný disk.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Vytvořte sítě a další prostředky virtuálních počítačů, které má nový virtuální počítač používat.

### <a name="create-the-subnet-and-virtual-network"></a>Vytvoření podsítě a virtuální sítě

Vytvořte [virtuální síť](../../virtual-network/virtual-networks-overview.md) a podsíť pro virtuální počítač.

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem *mySubNet*ve skupině prostředků *myDestinationResourceGroup*a nastaví předponu adresy podsítě na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Vytvořte virtuální síť. V tomto příkladu se nastaví název virtuální sítě na *myVnetName*, umístění, které se má *západní USA*, a předpona adresy pro virtuální síť na *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
Abyste se mohli přihlásit ke svému VIRTUÁLNÍmu počítači pomocí protokolu RDP (Remote Desktop Protocol), budete muset mít bezpečnostní pravidlo, které umožňuje přístup protokolu RDP na portu 3389. V našem příkladu byl virtuální pevný disk pro nový virtuální počítač vytvořený z existujícího specializovaného virtuálního počítače, takže můžete použít účet, který existoval na zdrojovém virtuálním počítači pro protokol RDP.

V tomto příkladu se nastaví název skupiny zabezpečení sítě (NSG) na *myNsg* a název pravidla RDP na *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodech a pravidlech NSG najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. V tomto příkladu je název veřejné IP adresy nastavený na *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Vytvořte síťovou kartu. V tomto příkladu je název síťové karty nastavený na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Nastavení názvu a velikosti virtuálního počítače

V tomto příkladu se nastaví název virtuálního počítače na *myVM* a velikost virtuálního počítače na *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidat síťovou kartu
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Přidat disk s operačním systémem 

Přidejte disk s operačním systémem do konfigurace pomocí [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). V tomto příkladu se nastaví velikost disku na *128 GB* a připojí se spravovaný disk jako disk s operačním *systémem Windows* .
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Dokončete virtuální počítač. 

Vytvořte virtuální počítač pomocí [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) s konfiguracemi, které jsme právě vytvořili.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Pokud je tento příkaz úspěšný, zobrazí se výstup podobný tomuto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že byl virtuální počítač vytvořen.
Nově vytvořený virtuální počítač by se měl zobrazit buď v [Azure Portal](https://portal.azure.com) v části **Procházet** > **virtuální počítače**, nebo pomocí následujících příkazů PowerShellu.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Další kroky
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md).

