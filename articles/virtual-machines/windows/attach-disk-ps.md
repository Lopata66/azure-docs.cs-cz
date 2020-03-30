---
title: Připojení datového disku k virtuálnímu počítači Windows v Azure pomocí PowerShellu
description: Jak připojit nový nebo existující datový disk k virtuálnímu virtuálnímu počítače se systémem Windows pomocí prostředí PowerShell s modelem nasazení Správce prostředků.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267752"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Připojení datového disku k virtuálnímu virtuálnímu počítače s Windows pomocí Prostředí PowerShell

Tento článek ukazuje, jak připojit nové i existující disky k virtuálnímu počítači s Windows pomocí prostředí PowerShell. 

Nejprve si přečtěte tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Další informace najdete [v tématu Velikosti pro virtuální počítače](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Chcete-li používat prémiové ssd dispony, budete potřebovat [typ virtuálního počítače s podporou úložiště](sizes-memory.md)s podporou úložiště , jako je virtuální počítač řady DS nebo GS.

Tento článek používá PowerShell v rámci [Prostředí Azure Cloud ,](https://docs.microsoft.com/azure/cloud-shell/overview)který se neustále aktualizuje na nejnovější verzi. Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v horní části libovolného bloku kódu.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Přidání prázdného datového disku do virtuálního počítače

Tento příklad ukazuje, jak přidat prázdný datový disk do existujícího virtuálního počítače.

### <a name="using-managed-disks"></a>Použití spravovaných disků

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Použití spravovaných disků v zóně dostupnosti

Chcete-li vytvořit disk v zóně dostupnosti, použijte příkaz `-Zone` [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) s parametrem. Následující příklad vytvoří disk v zóně *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicializovat disk

Po přidání prázdného disku jej budete muset inicializovat. Chcete-li disk inicializovat, můžete se přihlásit k virtuálnímu počítače a použít správu disku. Pokud jste při jeho vytvoření povolili [službu WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) a certifikát na virtuálním počítači, můžete k inicializaci disku použít vzdálené prostředí PowerShell. Můžete také použít vlastní rozšíření skriptu:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Soubor skriptu může obsahovat kód pro inicializaci disků, například:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojení existujícího datového disku k virtuálnímu počítače

Existující spravovaný disk můžete připojit k virtuálnímu počítače jako datový disk.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Další kroky

Spravované disky můžete také nasadit pomocí šablon. Další informace najdete [v tématu Použití spravovaných disků v šablonách Správce prostředků Azure](using-managed-disks-template-deployments.md) nebo v [šabloně rychlého startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) pro nasazení více datových disků.
