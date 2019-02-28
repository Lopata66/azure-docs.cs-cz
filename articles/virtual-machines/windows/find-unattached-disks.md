---
title: Vyhledat a odstranit nepřipojené spravovaných a nespravovaných disků v Azure | Dokumentace Microsoftu
description: Jak najít a odstranit nepřipojené spravovaných a nespravovaných (objekty BLOB virtuálních pevných disků nebo stránky) disků v Azure pomocí Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 5b9ab1c2735bd92855c032fe2eff17b804636e18
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983349"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Vyhledat a odstranit nepřipojené spravovaných a nespravovaných disků v Azure

Když odstraníte virtuální počítač (VM) v Azure, ve výchozím nastavení, nebudou odstraněny všechny disky, které jsou připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat v důsledku neúmyslnému odstranění virtuálních počítačů. Po odstranění virtuálního počítače budete i nadále platit za nepřipojené disky. V tomto článku se dozvíte, jak najít a odstranit všechny nepřipojené disky a omezení zbytečných nákladů.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: Vyhledat a odstranit nepřipojené disky

Následující skript hledá nepřipojené [spravované disky](managed-disks-overview.md) porovnáním hodnoty **ManagedBy** vlastnost. Když spravovaný disk je připojený k virtuálnímu počítači, **ManagedBy** vlastnost obsahuje ID prostředku virtuálního počítače. Když spravovaný disk není připojený, **ManagedBy** vlastnost má hodnotu null. Tento skript kontroluje všechny spravované disky v rámci předplatného Azure. Pokud skript vyhledá spravovaného disku s **ManagedBy** nastavenou na hodnotu null, který určuje, zda disk není připojený.

>[!IMPORTANT]
>Nejprve spusťte skript tím, že nastavíte **deleteUnattachedDisks** proměnných na hodnotu 0. Tato akce umožní vyhledat a zobrazit všechny připojené spravované disky.
>
>Poté, co si všechny nepřipojené disky, spusťte znovu skript a nastavit **deleteUnattachedDisks** proměnných na hodnotu 1. Tato akce umožňuje odstranit všechny připojené spravované disky.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledat a odstranit nepřipojené disky

Nespravované disky jsou soubory virtuálního pevného disku, které jsou uloženy jako [objekty BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účty služby Azure storage](../../storage/common/storage-create-storage-account.md). Následující skript hledá nepřipojené nespravované disky (objekty BLOB stránky) tím, že kontroluje hodnotu **LeaseStatus** vlastnost. Když nespravovaný disk je připojený k virtuálnímu počítači, **LeaseStatus** je nastavena na **uzamčené**. Když není připojený, nespravovaný disk **LeaseStatus** je nastavena na **Odemknutý**. Skript kontroluje všechny nespravovaných disků ve všech účtech úložiště Azure v rámci předplatného Azure. Pokud skript vyhledá nespravovaný disk s **LeaseStatus** vlastnost nastavena na hodnotu **Odemknutý**, skript určí, zda disk není připojený.

>[!IMPORTANT]
>Nejprve spusťte skript tím, že nastavíte **deleteUnattachedVHDs** proměnných na hodnotu 0. Tato akce umožní vyhledat a zobrazit všechny nepřipojené nespravované virtuální pevné disky.
>
>Poté, co si všechny nepřipojené disky, spusťte znovu skript a nastavit **deleteUnattachedVHDs** proměnných na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální pevné disky.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [odstranit účet úložiště](../../storage/common/storage-create-storage-account.md) a [identifikovat osamocené disky pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)