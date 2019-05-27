---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 02/22/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 72d9ec52732a78e39f6481e2cb2d40f17f86f028
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147419"
---
# <a name="enable-write-accelerator"></a>Povolení akcelerátoru zápisu

Zapsat že akcelerátor je funkce disku pro řadu M-Series virtuálních počítačů (VM) na Premium Storage, službou Azure Managed Disks výhradně. Jak uvádí název účelem funkce je zlepšit latenci vstupu/výstupu zápisů ve službě Azure Storage úrovně Premium. Zapsat že akcelerátoru je ideálním řešením, kdy jsou aktualizace souborů protokolu vyžaduje k uchování pro disk způsobem vysoce výkonných moderních databází.

Zapsat že akcelerátoru je obecně dostupná pro virtuální počítače řady M-series ve veřejném cloudu.

## <a name="planning-for-using-write-accelerator"></a>Plánování pro použití akcelerátorem zápisu

Zapsat že akcelerátoru byste měli použít pro svazky, které obsahují transakční protokol nebo znovu protokoly DBMS. Není doporučeno použít akcelerátorem zápisu pro datové svazky systému DBMS, jak funkce optimalizovaná pro použití s disky s protokoly.

Zápis akcelerátoru pouze funguje ve spojení s [Azure managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Povolení Write Accelerator pro disk s operačním systémem virtuálního počítače se restartuje virtuální počítač.
>
> Povolit akcelerátorem zápisu k existujícímu disku Azure, který není součástí sestavení svazku z více disků se vedoucí disk nebo svazek, prostory úložiště Windows, souborový server Windows horizontální navýšení kapacity (SOFS), Linux LVM nebo MDADM, úloh, přístup k Azure disk Windows musí být vypnut. Databázové aplikace pomocí Azure disk musí být vypnut.
>
> Pokud chcete povolit nebo zakázat Write Accelerator pro existující svazek, která je založená mimo několik disků Azure Premium Storage a prokládané použijete Správce Windows disk nebo svazek, prostory úložiště Windows, horizontální navýšení kapacity Windows souborový server (SOFS) Linux LVM nebo MDADM, všechny disky vytváření svazku musí být zapnutá nebo vypnutá akcelerátorem zápisu v samostatné kroky. **Před povolením nebo zakázáním akcelerátorem zápisu v takové konfiguraci, vypněte virtuální počítač Azure**.

Povolení Write Accelerator pro disky s operačním systémem nemělo být nutné pro virtuální počítač SAP související konfigurace.

### <a name="restrictions-when-using-write-accelerator"></a>Omezení při použití akcelerátorem zápisu

Při použití akcelerátorem zápisu pro Azure disk nebo virtuální pevný disk, platí tato omezení:

- Premium diskové mezipaměti musí být nastavena na hodnotu 'None' nebo "Jen pro čtení". Všechny jiné režimy ukládání do mezipaměti nejsou podporovány.
- Snímek nejsou aktuálně podporovány pro disky s povoleným akcelerátorem zápisu. Při zálohování služba Azure Backup automaticky vyloučí s povoleným akcelerátorem zápisu disky připojené k virtuálnímu počítači.
- Menší velikost vstupně-výstupních operací (< = 512 KiB) trvá akcelerované cestu. V úloze situacích, kde data prochází hromadné načtení nebo pokud vyrovnávacích pamětí protokolu transakce různých DBMS jsou vyplněny ve větší míře před získávání trvale uložena do úložiště, je pravděpodobné, vstupně-výstupních operací zapsána do disku nejde akcelerované cestu.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat akcelerátorem zápisu. Aktuální omezení platí pro:

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Počet disků akcelerátorem zápisu | Zápis disku akcelerátoru IOPS na virtuální počítač |
| --- | --- | --- |
| M128ms, 128s | 16 | 20000 |
| M64s M64ms M64ls, | 8 | 10000 |
| M32ms M32s M32ls M32ts, | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms M8s | 1 | 1250 |

Jsou na limity IOPS na virtuální počítač a *není* na disk. Všechny disky akcelerátorem zápisu sdílet stejný limit vstupně-výstupních operací na virtuální počítač.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Povolení akcelerátoru zápisu na určitém disku

Několik částí popisuje, jak může být s povoleným akcelerátorem zápisu na Azure Premium Storage virtuálních pevných discích.

### <a name="prerequisites"></a>Požadavky

Následující požadavky platí v daném okamžiku použití akcelerátorem zápisu:

- Disky, které chcete použít akcelerátor zápisu Azure proti musí být [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) na Premium Storage.
- Musí používat virtuálních počítačů řady M-series

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Povolení Azure akcelerátorem zápisu pomocí Azure Powershellu

Modul prostředí Azure PowerShell verze 5.5.0 zahrnují změny na příslušné rutiny k povolení nebo zakázání Write Accelerator pro konkrétní disky Azure Premium Storage.
Pokud chcete povolit nebo nasadit disky, které jsou podporovány akcelerátorem zápisu, máte následující příkazy prostředí PowerShell změnit a rozšířená tak, aby přijímat parametr pro akcelerátorem zápisu.

Nového přepínacího parametru **- WriteAccelerator** byla přidána do následujících rutin:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Nesmí mít parametr nastaví vlastnost na hodnotu false a nasadí disky, které nepodporují podle akcelerátorem zápisu.

Nového přepínacího parametru **- OsDiskWriteAccelerator** byl přidán do následujících rutin:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Ve výchozím nastavení vrací disky, které nechcete využívat akcelerátorem zápisu bez zadání parametru nastaví vlastnost na hodnotu false.

Nový volitelný parametr logické hodnoty (NULL), **- OsDiskWriteAccelerator** byl přidán do následujících rutin:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Zadejte $true nebo $false řídit podpora akcelerátor zápisu Azure s disky.

Příklady příkazů může vypadat:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dva základní scénáře možnost využívat skripty, jak je znázorněno v následujících částech.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Přidání nového disku podporované akcelerátorem zápisu pomocí Powershellu

Tento skript slouží k přidání nového disku k virtuálnímu počítači. Disk vytvořený pomocí tohoto skriptu používá akcelerátorem zápisu.

Nahraďte `myVM`, `myWAVMs`, `log001`, velikost disku a LunID disku s hodnotami, které jsou vhodné pro vaše konkrétní nasazení.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Povolení akcelerátoru zápisu na disk s existující Azure pomocí Powershellu

Tento skript můžete použít k povolení akcelerátorem zápisu na disk s existující. Nahraďte `myVM`, `myWAVMs`, a `test-log001` s hodnotami, které jsou vhodné pro vaše konkrétní nasazení. Skript přidá do existujícího disku akcelerátorem zápisu kde hodnota **$newstatus** je nastavena na "$true". Pomocí hodnoty "$false" zakážete akcelerátorem zápisu na daný disk.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Provádění skriptu výše odpojit disk určený, povolí akcelerátorem zápisu na disk a pak připojte disk

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Povolení akcelerátoru zápisu pomocí webu Azure portal

Prostřednictvím portálu, kde můžete určit nastavení mezipaměti disku můžete povolit akcelerátorem zápisu:

![Akcelerátor zápisu na portálu Azure portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Povolení akcelerátoru zápisu pomocí Azure CLI

Můžete použít [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) umožňující akcelerátorem zápisu.

Chcete-li povolit akcelerátorem zápisu na stávající disk, použijte [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), můžete použít následující příklady Pokud nahradíte diskName, VMName a skupina prostředků s vlastními hodnotami: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Připojení disku s akcelerátorem zápisu povolené použití [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), můžete použít následující příklad je-li změnit v vlastními hodnotami: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Chcete-li zakázat akcelerátorem zápisu, použijte [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), nastavení vlastnosti na hodnotu false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Povolení akcelerátoru zápisu pomocí rozhraní Rest API

K nasazení pomocí Rest API služby Azure, budete muset nainstalovat Azure armclient.

### <a name="install-armclient"></a>Nainstalujte armclient

Ke spuštění armclient, musíte ji nainstalovat prostřednictvím Chocolatey. Můžete ji nainstalovat prostřednictvím cmd.exe nebo prostředí powershell. Použití se zvýšenými oprávněními pro tyto příkazy ("Spustit jako správce").

Pomocí cmd.exe, spusťte následující příkaz: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Pomocí prostředí PowerShell, spusťte následující příkaz: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nyní můžete nainstalovat armclient pomocí následujícího příkazu v cmd.exe nebo prostředí PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Získávání aktuální konfigurace virtuálního počítače

Chcete-li změnit atributy vaše konfigurace disků, musíte nejprve získat aktuální konfiguraci v souboru JSON. Aktuální konfigurace můžete získat spuštěním následujícího příkazu: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Podmínky v rámci <> <>nahraďte vaše data, včetně názvu souboru, který by měl mít soubor JSON.

Výstup může vypadat:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Dál aktualizujte soubor JSON a povolení akcelerátorem zápisu na disk říká "log1". To lze provést přidáním tohoto atributu do souboru JSON za položka mezipaměti disku.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Pak aktualizujte stávající nasazení pomocí tohoto příkazu: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Výstup by měl vypadat jako následující. Uvidíte, že s povoleným akcelerátorem zápisu pro jeden disk.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Po provedení této změny disku by měla podporovat akcelerátorem zápisu.
